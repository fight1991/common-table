
<template>
  <div style="position:relative">
  <!--  自定义表头显示项目数 -->
    <el-popover popper-class="tableBtn-popper" v-if="checked">
      <ul>
        <li v-for="(value,key) in tableHeadData" :key="'index' + key">
          <el-checkbox v-model="value.checked" size="mini">{{value.label}}</el-checkbox>
        </li>
      </ul>
      <el-button class="poper-btn" slot="reference" icon="el-icon-s-grid" plain size="mini"></el-button>
    </el-popover>
    <!--  公共table列表 -->
    <el-table ref="commonTable"
      @select="selectSingleBox"
      @select-all="selectAllBox"
      @row-click="selectRow"
      :data="tableList"
      :max-height="tableHeight"
      size="mini"
      highlight-current-row
      :border="border"
      :show-summary="showSummary"
      @summary-method="summaryMethod">
      <el-table-column v-if="selectBox" type="selection" width="40" label="序号"></el-table-column>
      <template v-for="(item,index) in trueTableHead">
        <el-table-column
          v-if="!item.slotName"
          :key="'table' + index"
          :fixed="item.fixed || false"
          :prop="item.prop || ''"
          :label="item.label"
          :align="item.align || 'left'"
          :min-width="item.width || '200'"
        ></el-table-column>
        <el-table-column v-else
          :key="'table' + index"
          :fixed="item.fixed || false"
          :prop="item.prop || ''"
          :label="item.label"
          :align="item.align || 'left'"
          :min-width="item.width || '200'">
          <template slot-scope="scope">
            <!-- 具名插槽 -->
            <slot :name="item.slotName || 'default'" :row="scope.row"></slot>
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
*/
export default {
  name: 'common-table',
  data() {
    return {
      tableHeight: window.innerHeight - this.$store.state.HEIGHT_DIFFERENCE,
      selection: []
    }
  },
  props: {
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
    tableHeadData: {
      type: Array,
      default: () => {
        return []
      }
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
      this.selection = selection
      this.$emit('select',selection, row)
    },
    // 勾选选择框
    selectAllBox (selection) {
      this.selection = selection
      this.$emit('select',this.selection)
    },
    // 点击表格行
    selectRow (row) {
      let index = this.selection.indexOf(row)
      if (index >= 0) { // 当前的行已经被选中了
        this.$refs['commonTable'].toggleRowSelection(row, false)
        this.selection.splice(index, 1)
      } else {
        this.$refs['commonTable'].toggleRowSelection(row, true)
        this.selection.push(row)
      }
      this.$emit('select',this.selection)
    },
    // 汇总规则
    summaryMethod(param) {
      this.$emit('summary-method', param)
    }
  }
};
</script>
<style scoped lang="less">
</style>
