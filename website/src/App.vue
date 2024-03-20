<template>
  <div class="app-container container">
    <section class="search_content">
      <pro-form :dataSource="dataSource" :formItems="formItems" :labelWidth="'148px'">
        <template #rptDate>
          <el-select :value="dataSource.type" class="search-select-container">
            <el-option label="收入时间" :value="'income_time'" />
            <el-option label="进账时间" :value="'stl_time'" />
          </el-select>
        </template>
        <template #render> <span>我是自定义组件</span> </template>

        <template #buttonGroups>
          <el-col :xs="24" :md="8" :xl="8" :offset="16">
            <el-form-item label-width="104px">
              <el-button type="primary" size="mini">查询</el-button>
              <el-button type="reset" size="mini">重置</el-button>
            </el-form-item>
          </el-col>
        </template>
      </pro-form>
    </section>

    <section class="table_container" ref="tableContainer">
      <div class="table-top-btn">
        <el-button type="primary" size="mini">导出</el-button>
        <el-button type="reset" size="mini" @click="handleAdd">新增</el-button>
      </div>
      <pro-table
        proTableRef="proTableRef"
        :tableColumns="tableColumns"
        :tableData="tableData"
        :tableProps="{
          border: true,
          tooltipEffect: 'dark',
          maxHeight: tableHeight,
        }"
        :paginationProps="{
          total: total,
          pageSize: pageSize,
          currentPage: page,
          sizeChange: sizeChange,
          currentChange: currentChange,
        }"
        @selection-change="handleSelectionChange"
      />
    </section>
  </div>
</template>
<script>
import { ProForm, ProTable } from 'vue2-pro-ui';

export default {
  name: 'App',
  components: {
    ProForm,
    ProTable,
  },
  data() {
    return {
      tableHeight: 500,
      proTableRef: {},
      total: 10,
      page: 1,
      pageSize: 10,
      dataSource: {
        type: 'income_time',
        ID6: 1,
        ID7: '1',
        ID8: [],
      },
      formItems: [
        // {
        //   type: 'datePicker',
        //   label: '',
        //   customLabel: true,
        //   required: true,
        //   prop: 'rptDate',
        //   attrs: { type: 'daterange' },
        //   colLayout: {
        //     xs: 24,
        //     md: 10,
        //     xl: 10,
        //   },
        // },
        {
          type: 'input',
          label: '产品名称',
          prop: 'ID1',
          colLayout: {
            xs: 24,
            md: 8,
            xl: 8,
          },
          attrs: {
            clearable: true,
          },
          required: true,
        },
        {
          type: 'input',
          label: '目的口岸',
          prop: 'ID2',
          colLayout: {
            xs: 24,
            md: 8,
            xl: 8,
          },
        },
        {
          type: 'tooltipSelect',
          label: '时效维度',
          prop: 'ID7',
          options: [
            { label: '全部', value: '1' },
            { label: '全部2', value: '2' },
          ],
          multiple: false,
          colLayout: {
            xs: 24,
            md: 8,
            xl: 8,
          },
        },
        {
          type: 'tooltipSelect',
          label: '时效维度',
          prop: 'ID8',
          options: [
            { label: '全部', value: '1' },
            { label: '全部2', value: '2' },
          ],
          multiple: false,
          colLayout: {
            xs: 24,
            md: 8,
            xl: 8,
          },
        },
        {
          type: 'tooltipSelect',
          label: '时效维度',
          prop: 'ID9',
          options: [
            { label: '全部', value: '1' },
            { label: '全部2', value: '2' },
          ],
          multiple: false,
          colLayout: {
            xs: 24,
            md: 8,
            xl: 8,
          },
        },
        {
          type: 'tooltipSelect',
          label: '时效维度',
          prop: 'ID10',
          options: [
            { label: '全部', value: '1' },
            { label: '全部2', value: '2' },
          ],
          multiple: false,
          colLayout: {
            xs: 24,
            md: 8,
            xl: 8,
          },
        },
        // {
        //   type: 'render',
        //   label: 'render',
        //   prop: 'render',
        //   colLayout: {
        //     xs: 24,
        //     md: 8,
        //     xl: 8,
        //   },
        // },
      ],
      loading: false,
      tableData: [
        { name: '男男', gender: '1', id: '1' },
        { name: '女女', gender: '2', id: '2' },
        { name: '未知', gender: '0', id: '3' },
        { name: '未知', gender: '0', id: '4' },
        { name: '未知', gender: '0', id: '5' },
        { name: '未知', gender: '0', id: '6' },
        { name: '未知', gender: '0', id: '7' },
        { name: '未知', gender: '0', id: '8' },
        { name: '未知', gender: '0', id: '9' },
        { name: '未知', gender: '0', id: '10' },
        { name: '未知', gender: '0', id: '11' },
        { name: '未知', gender: '0', id: '12' },
        { name: '未知', gender: '0', id: '13' },
      ],
      multipleSelection: [],
    };
  },

  computed: {
    tableColumns() {
      return [
        {
          type: 'selection',
          fixed: 'left',
          width: 150,
          align: 'center',
        },
        {
          type: 'expand',
          fixed: 'left',
          width: 150,
          align: 'center',
          render:()=>{
            return (
              <span>1234</span>
            )
          }
        },
        {
          fixed: 'left',
          width: 110,
          label: '操作',
          prop: 'option',
          align: 'center',
          render: (record) => {
            return (
              <el-button
                type="text"
                onClick={() => {
                  console.log(record);
                }}
              >
                编辑
              </el-button>
            );
          },
        },
        {
          label: 'id',
          prop: 'id',
          align: 'center',
        },
        {
          label: '姓名',
          prop: 'name',
          align: 'center',
        },
        {
          label: '性别',
          prop: 'gender',
          align: 'center',
          render: ({ row }) => {
            return row.gender === '1' ? '男' : row.gender === '2' ? '女' : '未知';
          },
        },
      ];
    },
  },
  activated() {
    window.onresize = () => {
      let offsetTop = this.proTableRef.$el.offsetTop;
      this.tableHeight = window.innerHeight - offsetTop + this.$refs.tableContainer.offsetTop - 260;
    };
  },
  created() {
    this.$nextTick(() => {
      let offsetTop = this.proTableRef.$el.offsetTop;
      this.tableHeight = window.innerHeight - offsetTop + this.$refs.tableContainer.offsetTop - 260;
    });
  },
  methods: {
    handleAdd() {
      console.log('proTableRef', this.proTableRef);
    },
    handlerest() {},
    sizeChange(pageSize) {
      console.log('pageSize', pageSize);
    },
    currentChange(page) {
      console.log('page', page);
    },
    handleSelectionChange(val) {
      console.log('proTableRef', this.proTableRef);
      this.multipleSelection = val;
    },
  },
};
</script>

<style lang="scss">
.search_content .search-select-container {
  width: 118px !important;
  .el-input {
    width: 118px !important;
  }
}
</style>
