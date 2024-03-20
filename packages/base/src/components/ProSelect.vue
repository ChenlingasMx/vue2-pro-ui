<template>
  <el-tooltip
    class="item"
    effect="dark"
    placement="top"
    :disabled="!tagList || tagList.length == 0"
  >
    <div slot="content">
      <p v-for="(item, key) in tagList" :key="key">{{ item[params.label] }}</p>
    </div>
    <el-select
      ref="tagSelect"
      v-model="defaultValue"
      :remote="remote"
      :filterable="filterable"
      :clearable="clearable"
      :multiple="multiple"
      collapse-tags
      :remote-method="(val) => $emit('search', val.trim())"
      @focus="() => handleFocus('')"
      :placeholder="placeholder"
      :disabled="disabled"
      :filter-method="dataFilter"
      :value-key="valueKey"
      popper-class="select-popper"
      reserve-keyword
      @change="handleChange"
      @visible-change="visibleChange"
      :loading="loading"
    >
      <div v-if="multiple && tagList.length > 0" class="tagList">
        <div>已选择</div>
        <div class="tagLabel-wrap">
          <span v-for="(e, i) in tagList" :key="i" class="tagLabel">
            <span>{{ e[params.label] }}</span>
            <i @click="deleteTag(e[params.value])" class="el-icon-close"></i>
          </span>
        </div>
      </div>

      <div class="title_content">
        <div class="title_item">
          <p class="title_text">{{ title.label || "-" }}</p>
        </div>
        <div class="title_item2">
          <p class="title_text">{{ title.value || "-" }}</p>
        </div>
      </div>

      <el-option
        style="max-width: 350px"
        v-for="item in dataList"
        :key="item[params.value]"
        :value="valueKey ? item : item[params.value]"
        :label="item[params.label]"
        :disabled="item.disabled"
      >
        <span style="float: left">{{ item[params.label] }}</span>
        <span style="float: right">{{ item[params.value] }}</span>
      </el-option>
    </el-select>
  </el-tooltip>
</template>

<script>
import _ from "lodash";
export default {
  name: "ProSelect",
  components: {},
  props: {
    value: {
      required: true,
    },
    options: {
      type: Array,
      default: () => [],
    },
    placeholder: {
      type: String,
      default: "",
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    remote: {
      type: Boolean,
      default: false,
    },
    params: {
      type: Object,
      default: () => {
        return { label: "label", value: "value" };
      },
    },
    title: {
      type: Object,
      default: () => ({
        label: "产品名称",
        value: "产品编号",
      }),
    },
    multiple: {
      type: Boolean,
      default: true,
    },
    clearable: {
      type: Boolean,
      default: true,
    },
    filterable: {
      type: Boolean,
      default: true,
    },
    valueKey: {
      type: String,
      default: "",
    },
    loading: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      defaultValue: null,
      tagList: [],
      dataList: [],
      isFirstFocus: true, // 是否触发focus
      dataTagList: [],
    };
  },
  computed: {},
  watch: {
    options: {
      handler(newValue) {
        this.dataList = newValue || [];
        // 记录每次传入的options，去重保证tagList可以显示
        this.dataTagList = _.uniqBy(
          [...this.dataTagList, ...(newValue || [])],
          this.params["value"]
        );
      },
      immediate: true,
    },
    // 监听value的变化，获取tagList
    value: {
      handler(val) {
        // 不是多选 || 多选但是没有值，清空tagList
        if (!this.multiple || (this.multiple && (!val || val.length === 0))) {
          this.tagList = [];
          // 多选传入 value-key
        } else if (this.valueKey) {
          this.tagList = val;
          // 多选没有传入value-key
        } else {
          const arr = val.map((e) =>
            (this.dataTagList || []).find(
              (item) => item[this.params.value] === e
            )
          );
          this.tagList = arr;
        }
        this.defaultValue = val;
      },
      immediate: true,
      deep: true,
    },
  },
  methods: {
    // 1.select打开弹窗获取焦点
    // 2.select关闭弹窗取消焦点
    // 防止多次change事件多次触发focus
    visibleChange(show) {
      this.isFirstFocus = show;
    },
    handleFocus(value) {
      this.$nextTick(() => {
        if (this.isFirstFocus) {
          this.$emit("focus", value);
          this.isFirstFocus = false;
        }
      });
    },
    handleChange(value) {
      this.$emit("change", value);
      this.$emit("input", value);
    },
    dataFilter(val) {
      let dataListBackup = _.cloneDeep(this.options);
      if (val) {
        //val存在
        this.dataList = dataListBackup.filter((item) => {
          if (
            !!~item[this.params.label].indexOf(val) ||
            !!~item[this.params.label]
              .toUpperCase()
              .indexOf(val.toUpperCase()) ||
            !!~item[this.params.value].indexOf(val) ||
            !!~item[this.params.value].toUpperCase().indexOf(val.toUpperCase())
          ) {
            return true;
          }
        });
      } else {
        //val为空时，还原数组
        this.dataList = dataListBackup;
      }
    },
    // 删除tags
    deleteTag(value) {
      // 找到要删除的元素在 tagList 中的索引
      const index = this.tagList.findIndex(
        (item) => item[this.params.value] === value
      );
      if (index !== -1) {
        this.tagList.splice(index, 1);
        this.defaultValue = this.defaultValue.filter(
          (item) => item[this.params.value] !== value
        );
        this.$refs.tagSelect.selected = this.tagList.map(
          (item) => item[this.params.value]
        );
        this.$emit("input", this.defaultValue);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.tagList {
  height: initial;
  border-bottom: solid 1px #e4e7ed;
  margin-bottom: 6px;
  padding: 0 6px;
  div:first-child {
    font-size: 14px;
    color: rgba(0, 0, 0, 1);
    margin-bottom: 6px;
    margin-top: 2px;
  }
  .tagLabel-wrap {
    display: flex;
    flex-wrap: wrap;
    width: 300px;
  }
  .tagLabel {
    flex: none;
    margin-right: 6px;
    margin-bottom: 6px;
    background-color: rgba(234, 234, 234, 1);
    padding: 0 6px;
    height: 22px;
    font-size: 12px;
    color: rgba(90, 94, 102, 1);
    line-height: 22px;
    span {
      cursor: pointer;
    }
    i {
      margin-left: 8px;
      border-radius: 50%;
      cursor: pointer;
    }
  }
}

::v-deep .el-select__tags {
  max-width: none !important;
  input:not(:first-child) {
    margin-left: 5px;
  }
  .el-tag {
    margin-top: 1px;
    line-height: 20px;
    height: 20px;
    padding-right: 5px;
    padding-left: 5px;
  }
  & > span {
    display: flex;
    flex-wrap: nowrap;
  }
  .el-tag:first-of-type {
    display: flex;
    .el-select__tags-text {
      max-width: 67px;
      overflow: hidden;
      display: flex;
      justify-content: flex-end;
      white-space: nowrap;
    }
    .el-tag__close {
      right: -2px;
    }
  }
}
::v-deep .el-select__tags .el-tag:first-of-type .el-select__tags-text {
  display: inline-block !important;
  overflow: hidden !important;
  text-overflow: ellipsis !important;
  white-space: nowrap !important;
}
.title_content {
  padding: 0 20px;
  position: relative;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  .title_item {
    float: left;
    width: 140px;
    height: 34px;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .title_item2 {
    float: right;
    width: 80px;
    height: 34px;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .title_text {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    color: #4c8cff;
    font-size: 14px;
    font-weight: 700;
    line-height: 34px;
  }
}
</style>
