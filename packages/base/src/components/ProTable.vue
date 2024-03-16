<script>
import ProPagination from './ProPagination.vue';
export default {
  name: 'Protable',
  components: { ProPagination },
  props: {
    tableColumns: { default: () => [], required: true }, // protable的表单列配置，同时生效于表单
    tableData: { default: () => [], required: true }, // 表格数据
    tableProps: { default: () => ({}), required: false }, // table的其他属性都可以往里塞
    loading: { default: false, required: false },
    paginationProps: { default: () => ({}), required: true }, // 分页的其他属性都可以往里塞
    proTableRef: { default: '' }, // 绑定父级 data 中的 ref,注意传入string，是 data 中你第一的那个key的string类型
    tableStyle: { default: () => ({}) }, // 绑定父级 data 中的 ref,注意传入string，是 data 中你第一的那个key的string类型
  },
  data() {
    return {
      pagination: {
        pageSize: 20,
        currentPage: 1,
      },
      proTableFormRef: {},
    };
  },
  watch: {},
  mounted() {
    this.$parent[this?.proTableRef] = this.$refs.elTable;
  },
  methods: {
    renderFun(columns) {
      return columns.map((item) => {
        if (item.show === false || item.hideInTable) {
          return;
        } else {
          return (
            <el-table-column attrs={{ ...item }}>
              {item.children ? this.renderFun(item.children) : item.render}
            </el-table-column>
          );
        }
      });
    },
  },
  render() {
    const on = {
      ...this.$listeners,
    };
    return (
      <div>
        <el-table
          data={this.tableData}
          tooltipEffect="dark"
          stripe
          attrs={{ ...this.tableProps }}
          ref="elTable"
          v-loading={this.loading}
          stripe
          style={{ ...this.tableStyle }}
          {...{ on }}
        >
          {this.renderFun(this.tableColumns)}
        </el-table>
        <div class="page_container">
          <pro-pagination
            pagination={{
              currentChange: (val) => {
                this.pagination = { ...this.pagination, currentPage: val };
                if (this.paginationProps.currentChange) this.paginationProps.currentChange(val);
              },
              sizeChange: (val) => {
                this.pagination = { ...this.pagination, pageSize: val };
                if (this.paginationProps.sizeChange) this.paginationProps.sizeChange(val);
              },
              ...this.pagination,
              ...this.paginationProps,
            }}
            resetPagination={() => {
              this.pagination = { ...this.pagination, currentPage: 1 };
              if (this.paginationProps.currentChange) this.paginationProps.currentChange(1);
            }}
          />
        </div>
      </div>
    );
  },
};
</script>

<style lang="scss" scoped></style>
