<template>
  <section>
    <el-form ref="formRef" :model="dataSource" :label-width="labelWidth">
      <el-row :gutter="20" justify="space-between" style="margin: 0">
        <template v-for="item in filterFormItems">
          <el-col v-if="item.type" :key="item.prop" v-bind="item.colLayout || colLayout">
            <el-form-item
              :label="item.label"
              :prop="item.prop"
              :label-width="item.labelWidth"
              :required="item.required || false"
              :rules="
                item.rules || [
                  {
                    required: item.required || false,
                    message: `请输入${item.label}`,
                  },
                ]
              "
              :size="item.size || 'mini'"
              v-bind="item.formItemsProps"
            >
              <template v-if="item.customLabel" slot="label">
                <!-- 使用自定义标签 -->
                <slot :name="item.prop" :item="item"></slot>
              </template>
              <el-input
                v-if="item.type === 'input'"
                v-model="dataSource[item.prop]"
                :placeholder="item.placeholder || `请输入${item.label}`"
                clearable
                v-bind="item.attrs"
                v-on="item.listeners"
              />
              <el-select
                v-if="item.type === 'select'"
                v-model="dataSource[item.prop]"
                clearable
                :placeholder="item.placeholder || `请选择${item.label}`"
                :remote="item.remote || false"
                :remote-method="($event) => item.events && item.events.search && item.events.search($event)"
                @focus="item.events && item.events.focus && item.events.focus('')"
                :disabled="item.disabled"
                :value-key="item.valueKey"
                v-bind="item.attrs"
                v-on="item.listeners"
              >
                <el-option
                  v-for="option in item.options"
                  :key="option.value"
                  :label="option.label"
                  :value="item.valueKey ? option : option.value"
                  :disabled="option.disabled"
                  v-bind="option.attrs"
                />
              </el-select>
              <tooltip-select
                v-if="item.type === 'tooltipSelect'"
                v-model="dataSource[item.prop]"
                :options="item.options"
                :params="item.params"
                :placeholder="item.placeholder || '请输入关键词'"
                @search="($event) => item.events && item.events.search && item.events.search($event)"
                @focus="($event) => item.events && item.events.focus && item.events.focus($event)"
                :remote="item.remote"
                :disabled="item.disabled"
                :title="item.title"
                :multiple="item.multiple"
                :valueKey="item.valueKey"
                :clearable="item.clearable"
                :filterable="item.filterable"
                :attrs="item.attrs"
              />
              <el-date-picker v-if="item.type === 'datePicker'" v-model="dataSource[item.prop]" v-bind="item.attrs" />
              <el-autocomplete
                v-if="item.type === 'autocomplete'"
                v-model="dataSource[item.prop]"
                clearable
                :fetch-suggestions="item.querySearchAsync"
                :placeholder="item.placeholder || `请输入${item.label}`"
                v-bind="item.attrs"
                v-on="item.listeners"
              />
              <el-input
                v-if="item.type === 'inputNumber'"
                v-model="dataSource[item.prop]"
                :step="item.step || 0.01"
                :disabled="item.disabled"
                type="number"
                :min="item.min"
                :placeholder="item.placeholder || `请输入${item.label}`"
                @change="item.events && item.events.change && item.events.change($event)"
                @input="item.events && item.events.input && item.events.input($event)"
                v-bind="item.attrs"
                v-on="item.listeners"
              >
                <template slot="append">{{ item.prepend || '￥' }}</template>
              </el-input>
              <el-input-number
                v-if="item.type === 'inputStep'"
                class="input_number_container"
                v-model="dataSource[item.prop]"
                :min="item.min || 0"
                :max="item.min || 999"
                :placeholder="item.placeholder || `请输入${item.label}`"
                v-bind="item.attrs"
                v-on="item.listeners"
              />
              <template v-if="item.type === 'checkbox'">
                <el-checkbox-group v-model="dataSource[item.prop]" v-bind="item.attrs">
                  <el-checkbox
                    v-for="(option, index) in item.options"
                    :key="index"
                    :disabled="option.disabled"
                    :label="option.value"
                    v-bind="option.attrs"
                    v-on="item.listeners"
                  >
                    {{ option.label }}
                  </el-checkbox>
                </el-checkbox-group>
              </template>

              <template v-if="item.type === 'radio'">
                <el-radio-group
                  v-model="dataSource[item.prop]"
                  :disabled="item.disabled"
                  @change="item.events && item.events.change && item.events.change($event)"
                  v-bind="item.attrs"
                  v-on="item.listeners"
                >
                  <el-radio
                    v-for="(option, index) in item.options || getDictOption(item.lookupKey)"
                    :key="index"
                    :disabled="option.disabled"
                    :label="option.value"
                    v-on="item.listeners"
                  >
                    {{ option.label }}
                  </el-radio>
                </el-radio-group>
              </template>

              <template v-if="item.type === 'switch'">
                <el-switch
                  v-model="dataSource[item.prop]"
                  :disabled="item.disabled"
                  @change="item.events && item.events.change && item.events.change($event)"
                  v-bind="item.attrs"
                  v-on="item.listeners"
                />
              </template>
              <template v-if="item.type === 'slider'">
                <el-slider
                  v-model="dataSource[item.prop]"
                  :show-input="item.showInput"
                  :disabled="item.disabled"
                  :min="item.min || 0"
                  :max="item.max || 100"
                  v-bind="item.attrs"
                  v-on="item.listeners"
                />
              </template>
              <template v-if="item.type === 'render'">
                <slot :name="item.prop" :item="item"></slot>
              </template>
            </el-form-item>
          </el-col>
        </template>
        <slot name="buttonGroups"></slot>
      </el-row>
    </el-form>
  </section>
</template>

<script>
import TooltipSelect from './TooltipSelect';
export default {
  name: 'SearchFormPro',
  components: {
    TooltipSelect,
  },
  props: {
    dataSource: {
      type: Object,
      default: () => ({}),
    },
    // 搜索配置项
    formItems: {
      type: Array,
      default: () => [],
    },
    labelWidth: {
      type: String,
      default: '130px',
    },
    // 适配
    colLayout: {
      type: Object,
      default: () => ({
        xl: 8,
        lg: 8,
        md: 12,
        sm: 24,
        xs: 24,
      }),
    },
  },
  watch: {},
  computed: {
    filterFormItems() {
      return this.formItems.filter((item) => !item.hide);
    },
  },
  methods: {
    validate() {
      return new Promise((resolve) => {
        this.$refs.formRef.validate(resolve);
      });
    },
    resetFields() {
      this.$refs.formRef.resetFields();
    },
    clearValidate() {
      this.$refs.formRef.clearValidate();
    },
  },
};
</script>

<style lang="scss">
.input_number_container {
  width: 180px !important;
  input {
    width: 180px !important;
  }
}
</style>
