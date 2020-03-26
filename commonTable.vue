<template>
  <div style="position:relative">
    <el-popover
      popper-class="tableBtn-popper"
      v-if="checked"
    >
      <ul>
        <li
          v-for="(value,key) in tableHeadData"
          :key="'index' + key"
        >
          <el-checkbox
            v-model="value.checked"
            size="mini"
          >{{value.label}}</el-checkbox>
        </li>
      </ul>
      <el-button
        class="poper-btn"
        slot="reference"
        icon="el-icon-s-grid"
        plain
        size="mini"
      ></el-button>
    </el-popover>
    <!--  公共table列表 -->
    <!-- 选中行 有问题  -->
    <!-- @row-click="selectRow" -->
    <el-table
      ref="commonTable"
      @select="selectSingleBox"
      @select-all="selectAllBox"
      :data="tableList"
      :max-height="tableHeight || tableH"
      size="mini"
      highlight-current-row
      :border="border"
      :show-summary="showSummary"
      :summary-method="summaryMethod"
    >
      <!-- 自定义表格列 -->
      <el-table-column
        v-if="$attrs.selfCol"
        width="100"
        :label="$attrs.selfCol && $attrs.selfCol.label || ''"
        :prop="$attrs.selfCol && $attrs.selfCol.prop || ''"
        align="center"
      >
      </el-table-column>
      <el-table-column
        v-if="selectBox"
        type="selection"
        width="55"
      ></el-table-column>
      <el-table-column
        v-if="showNum"
        type="index"
        width="50"
        label="序号"
        align="center"
      ></el-table-column>
      <template v-for="(item,index) in trueTableHead">
        <el-table-column
          v-if="!item.slotName"
          :key="'table' + index"
          :fixed="item.fixed || false"
          :prop="item.prop || ''"
          :label="item.label"
          :align="item.align || 'left'"
          :min-width="item.minWidth || '120'"
          :width="item.width || ''"
        >
        </el-table-column>
        <el-table-column
          v-else
          :key="'table' + index"
          :fixed="item.fixed || false"
          :prop="item.prop || ''"
          :label="item.label"
          :align="item.align || 'left'"
          :min-width="item.minWidth || '120'"
          :width="item.width || ''"
        >
          <template slot-scope="scope">
            <!-- 具名插槽 -->
            <slot
              :name="item.slotName || 'default'"
              :row="scope.row"
              :$index="scope.$index"
            ></slot>
          </template>
        </el-table-column>
      </template>
    </el-table>
  </div>
</template>
<script>
/*
 *  @param :
 *  1. clearSelection --- 如果传过来的值是true时,清空所有选项,然后会自动改变父组件的值为false
 *  2. tableList -- 表格数据列表
 *  3. tableHeadData -- 表格表头数据
 *  4. selectBox --- 是否显示勾选框
 *  5. border --- 是否显示边框
 *  6. showSummary -- 是否显示汇总
 *  7. showNum -- 是否显示序号
 *  8. propertySum -- 求和项
 *  9. selectSingle -- 表格是否为单选
 */
export default {
  name: 'common-table',
  data() {
    return {
      tableH: window.innerHeight - this.$store.state.HEIGHT_DIFFERENCE,
      selection: []
    }
  },
  created() {},
  props: {
    tableHeight: {
      type: Number,
      default: () => {
        return 0
      }
    },
    tableList: {
      type: Array,
      default: () => {
        return []
      }
    },
    border: {
      type: Boolean,
      default: () => {
        return true
      }
    },
    checked: {
      type: Boolean,
      default: () => {
        return true
      }
    },
    showSummary: {
      type: Boolean,
      default: () => {
        return false
      }
    },
    clearSelection: {
      type: Boolean,
      default: () => {
        return false
      }
    },
    selectBox: {
      type: Boolean,
      default: () => {
        return true
      }
    },
    showNum: {
      type: Boolean,
      default: false
    },
    tableHeadData: {
      type: Array,
      default: () => {
        return []
      }
    },
    propertySum: {
      type: Array,
      default: () => {
        return ['totalPrice']
      }
    },
    selectSingle: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    trueTableHead() {
      return this.tableHeadData.filter(v => v.checked)
    }
  },
  watch: {
    clearSelection: function(newData) {
      if (typeof newData === 'boolean' && newData) {
        newData && this.$refs['commonTable'].clearSelection()
        this.$emit('update:clearSelection', false)
      }
    }
  },
  mounted() {
    // this.list(this.pageParams);
  },
  methods: {
    selectSingleBox(selection, row) {
      if (this.selectSingle) {
        // 设置为单选
        this.selection = [row]
        this.$refs['commonTable'].clearSelection()
        this.$refs['commonTable'].toggleRowSelection(row, true)
      } else {
        this.selection = selection
      }
      this.$emit('select', selection, row)
    },
    // 勾选选择框
    selectAllBox(selection) {
      if (this.selectSingle) {
        this.$refs['commonTable'].clearSelection()
        this.selection = []
      } else {
        this.selection = selection
      }
      this.$emit('select', this.selection)
    },
    // 点击表格行
    selectRow(row) {
      let index = this.selection.indexOf(row)
      if (this.selectSingle) {
        if (index > -1) {
          this.$refs['commonTable'].toggleRowSelection(row, false)
          this.selection = []
        } else {
          this.selection = [row]
          this.$refs['commonTable'].clearSelection()
          this.$refs['commonTable'].toggleRowSelection(row, true)
        }
        this.$emit('select', this.selection)
        return
      }
      if (index > -1) {
        // 当前的行已经被选中了
        this.selection.splice(index, 1)
        this.$refs['commonTable'].toggleRowSelection(row, false)
      } else {
        this.$refs['commonTable'].toggleRowSelection(row, true)
        this.selection.push(row)
      }
      this.$emit('select', this.selection)
    },
    // 汇总规则
    summaryMethod(param) {
      const { columns, data } = param
      const sums = []
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = '合计:'
          return
        }
        if (this.propertySum.includes(column.property)) {
          const values = data.map(item => Number(item[column.property]))
          if (!values.every(value => isNaN(value))) {
            sums[index] = values.reduce((prev, curr) => {
              const value = Number(curr)
              if (!isNaN(value)) {
                return prev + curr
              } else {
                return prev
              }
            }, 0)
          } else {
            sums[index] = ''
          }
        } else {
          sums[index] = ''
        }
      })
      return sums
    },
    //勾选
    toggleSelection(rows) {
      if (rows) {
        rows.forEach(row => {
          this.$refs.multipleTable.toggleRowSelection(row)
        })
      } else {
        this.$refs.multipleTable.clearSelection()
      }
    }
  }
}
</script>
<style scoped lang="less">
</style>
