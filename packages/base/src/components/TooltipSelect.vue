<template>
    <el-select
      ref="tagSelect"
      v-model="selectVal"
      :remote="remote"
      :filterable="filterable"
      :clearable="clearable"
      :multiple="multiple"
      collapse-tags
      :remote-method="(val) => $emit('search', val.trim())"
      :focus="() => $emit('focus', '')"
      :placeholder="placeholder"
      :disabled="disabled"
      :filter-method="dataFilter"
      :value-key="valueKey"
      popper-class="select-popper"
      :v-bind="attrs"
    >
      <div v-if="multiple && tagList.length > 0" class="tagList">
        <div>已选择</div>
        <div class="tagLabel-wrap">
          <span v-for="(e, i) in tagList" :key="i" class="tagLabel">
            <span>{{ e[params.label] }}</span>
            <i @click="deleteTag(i)" class="el-icon-close"></i>
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
        v-for="(item, index) in dataList"
        :key="item[params.value]"
        :value="valueKey ? item : item[params.value]"
        :label="item[params.label]"
        :disabled="item.disabled"
      >
        <tooltip-over
          :content="item[params.label]"
          class="input-value"
          :refName="'tooltipOverProductName' + index"
        />
        <tooltip-over
          :content="item[params.value]"
          class="input-code"
          :refName="'tooltipOverProductCode' + index"
        />
      </el-option>
    </el-select>
</template>

<script>
import TooltipOver from "./TooltipOver";
import _ from "lodash";
export default {
  name: "tooltipSelect",
  components: {
    TooltipOver,
  },
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
    attrs: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      tagList: [],
      inputValue: false,
      dataList: [],
    };
  },
  computed: {
    selectVal: {
      get() {
        return this.value;
      },
      set(val) {
        this.$emit("input", val);
      },
    },
  },
  watch: {
    options: {
      handler(val) {
        this.dataList = val || [];
      },
      immediate: true,
    },
    value: {
      handler(val) {
        if (val && val.length === 0 || !this.multiple) {
        this.tagList = [];
      } else {
        this.tagList = val.map((e) =>
          this.dataList.find((item) => item[this.params.value] === e)
        );
      }
      },
      immediate: true,
    },
  },
  methods: {
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
    deleteTag(i) {
      this.tagList.splice(i, 1);
      this.$refs.tagSelect.selected = this.tagList;
      this.selectVal.splice(i, 1);
      this.$emit("input", [...this.selectVal]);
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
</style>./TooltipSelect.vue
