<template>
    <div>
      <el-button
        v-for="(item, i) in button"
        :key="i"
        :type="item.type || 'primary'"
        @click="item.click && item.click"
        v-bind="item"
      >
        {{ item.label || "-" }}
      </el-button>
    </div>
  </template>
  
  <script>
  export default {
    name: "buttonGroupPro",
    props: {
      button: {
        type: Array,
        default: () => [],
      },
    },
  };
  </script>
  
  <style lang="scss" scoped></style>
  