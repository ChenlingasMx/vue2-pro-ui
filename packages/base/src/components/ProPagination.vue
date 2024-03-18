<template>
  <el-pagination
    @current-change="currentChage"
    @size-change="sizeChange"
    :current-page="currentPage"
    :page-size="pageSize"
    :page-sizes="[10, 20, 50, 100]"
    layout="total, sizes, prev, pager, next, jumper"
    :total="pagination.total"
    :hide-on-single-page="pagination.show || false"
    ref="paginationRef"
  >
  </el-pagination>
</template>

<script>
export default {
  name: 'pro-pagination',
  props: {
    pagination: { default: () => ({ pageSize: 20, currentPage: 1 }), required: false },
    resetPagination: { default: () => {}, required: true },
  },
  data() {
    return {
      pageSize: this.pagination.pageSize,
      currentPage: this.pagination.currentPage,
    };
  },
  watch: {
    pagination: {
      handler: function (newVal) {
        if (newVal.currentPage !== 1 && Math.ceil(newVal.total / newVal.pageSize) < newVal.currentPage) {
          this.resetPagination();
          setTimeout(() => {
            this.reSearch();
          }, 1);
        }
      },
    },
  },
  methods: {
    currentChage(val) {
      this.pagination.currentChange && this.pagination.currentChange(val);
    },
    sizeChange(val) {
      this.pagination.sizeChange && this.pagination.sizeChange(val);
    },
  },
};
</script>
