<template>
  <div class="sys-main">
    <!-- 普通表格列表组件 -->
    <common-table @select="selectRow" :selectBox="true" :tableHeadData="tableHeadData" :tableList="resultList">
      <!-- 设计状态插槽 -->
      <template v-slot:status="{row}">
        <span class="status">{{row.e}}</span>
      </template>
      <!-- 版本号插槽 -->
      <template v-slot:version="{row}">
        <span class="version">{{row.f}}</span>
      </template>
      <!-- 操作栏插槽 -->
      <template v-slot:op="{row}">
        <el-button type="text">设计</el-button>
        <el-button type="text">提交</el-button>
      </template>
    </common-table>
    <!--分页-->
  </div>
</template>
<script>
import commonTable from '@/views/pages/components/commonTable'
export default {
  components: {
    commonTable
  },
  data() {
    return {
      rowSelect: [],
      tableHeadData: [
        {
          label: '业务编号',
          prop: 'a',
          checked: true
        },
        {
          label: '墓穴位置',
          prop: 'b',
          checked: true
        },
        {
          label: '申请人',
          prop: 'd',
          checked: true
        },
        {
          label: '使用人',
          prop: 'c',
          checked: true
        },
        {
          label: '完工日期',
          prop: 'd',
          checked: true
        },
        {
          label: '设计人',
          prop: 'e',
          checked: true
        },
        {
          label: '设计状态',
          prop: 'f',
          checked: true,
          slotName: 'status'
        },
        {
          label: '版本号',
          prop: 'i',
          checked: true,
          slotName: 'version'
        },
        {
          label: '操作',
          prop: 'e',
          checked: true,
          slotName: 'op',
          fixed: 'right'
        }
      ],
      resultList: [{a:1,b:2,c:3,d:4,e:5,f:6,g:7,h:8,i:9},{},{},{},{},{},{},{}],
    };
  },
  created() {
  },
  computed: {
    ...mapGetters({
      currentTabId: 'GetCurrentTabIndex'
    })
  },
  mounted() {
    // this.list(this.pageParams);
  },
  methods: {
    selectRow(selection) {
      this.rowSelect = selection
    }
  }
};
</script>
<style scoped lang="less">
.status {
  display: inline-block;
  padding: 0 20px;
  background-color: green;
  color: #fff;
  border-radius: 20px;
}
.version {
  display: inline-block;
  padding: 0 5px;
  background-color: aqua;
  border-radius: 50%;
}
.design-content {
  overflow-y: auto;
  max-height: 300px;
  width: calc(100% + 18px)
}
</style>

