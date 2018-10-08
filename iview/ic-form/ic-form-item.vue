<style>
.ivu-form-item-remark {
  display: inline-block;
  margin-top: 6px;
  color: #999999;
  line-height: 1;
}
</style>

<template>
  <FormItem :label="labelCanShow ? d.label : undefined" :prop="d.prop">

    <!-- Input -->
    <Input v-if="isInput"
      :type="type"
      v-model="content"
      :disabled="d.disabled"
      :readonly="d.readonly"
      :maxlength="d.maxlength"
      :placeholder="placeholder"
      :prefix="d.prefix"
      :suffix="d.suffix"
      @on-change="handleInput" />

    <!-- Number -->
    <InputNumber v-else-if="isNumber"
      v-model="content"
      :disabled="d.disabled"
      :readonly="d.readonly"
      :max="d.max"
      :min="d.min"
      :formatter="formatter"
      :parser="parser"
      @on-change="handleInput" />

    <!-- Date -->
    <DatePicker v-else-if="isDate"
      :type="type"
      v-model="content"
      :placeholder="placeholder"
      @on-change="handleInput">
    </DatePicker>

    <!-- Time -->
    <TimePicker v-else-if="isTime"
      :type="type"
      v-model="content"
      :placeholder="placeholder"
      @on-change="handleInput">
    </TimePicker>

    <!-- Select -->
    <Select v-else-if="isSelect"
      v-model="content"
      @on-change="handleInput">
      <Option v-for="v in list"
        :key="optionVal(v)"
        :value="optionVal(v)">
        {{ optionLabel(v) }}
      </Option>
    </Select>

    <!-- Checkbox -->
    <CheckboxGroup v-else-if="isCheckbox"
      v-model="content"
      :placeholder="placeholder"
      @on-change="handleInput">
      <Checkbox v-for="v in list"
        :key="optionVal(v)"
        :label="optionVal(v)">
        <slot :name="type" :item="v"></slot>
      </Checkbox>
    </CheckboxGroup>

    <!-- Radio -->
    <RadioGroup v-else-if="isRadio"
      v-model="content"
      :placeholder="placeholder"
      @on-change="handleInput">
      <Radio v-for="v in list"
        :key="optionVal(v)"
        :label="optionVal(v)">
        <slot :name="type" :item="v"></slot>
      </Radio>
    </RadioGroup>

    <!-- Rate -->
    <Rate v-else-if="isRate"
      v-model="content"
      :allow-half="d['allow-half']"
      @on-change="handleInput" />

    <!-- Switch -->
    <i-switch v-else-if="isSwitch"
      v-model="content"
      :disabled="d.disabled"
      @on-change="handleInput" />

    <!-- Custom -->
    <slot v-else-if="isCustom" :name="type"></slot>

    <!-- Remark -->
    <small :class="remarkClass" v-html="d.remark"></small>
  </FormItem>
</template>

<script>
export default {
  name: 'icFormItem',
  props: {
    data: {
      type: Object,
      default () {
        return {}
      }
    },
    value: null,
    enterText: {
      type: String,
      default: 'Enter '
    },
    selectText: {
      type: String,
      default: 'Select '
    },
    endText: {
      type: String,
      default: '.'
    },
    labelShow: {
      type: Boolean,
      default: true
    },

    // Class
    remarkClass: {
      type: String,
      default: 'ivu-form-item-remark'
    }
  },
  data () {
    return {
      innerData: Object.assign({
        prop: 'label',
        label: 'Label',
        type: 'text',
        placeholder: ''
      }, this.data),
      content: this.value,
      selectComponents: [
        'date',
        'daterange',
        'time',
        'select',
        'checkbox',
        'radio',
        'rate'
      ]
    }
  },
  watch: {
    value (val) {
      this.content = val
    }
  },
  computed: {
    d () {
      return this.innerData
    },
    type () {
      return this.d.type
    },
    placeholder () {
      const startText = this.typeExist(this.selectComponents)
        ? this.selectText : this.enterText
      const defaultText = `${startText}${this.d.label}${this.endText}`
      return this.d.placeholder || defaultText
    },
    list () {
      return this.d.list || []
    },
    labelCanShow () {
      return this.d.labelShow !== undefined
        ? this.d.labelShow
        : this.labelShow
    },
    isInput () {
      return this.typeExist(['text', 'password', 'textarea', 'url', 'email'])
    },
    isNumber () {
      return this.typeExist('number')
    },
    isDate () {
      return this.typeExist(['date', 'daterange'])
    },
    isTime () {
      return this.typeExist(['time', 'timerange'])
    },
    isSelect () {
      return this.typeExist('select')
    },
    isCheckbox () {
      return this.typeExist('checkbox')
    },
    isRadio () {
      return this.typeExist('radio')
    },
    isRate () {
      return this.typeExist('rate')
    },
    isSwitch () {
      return this.typeExist('switch')
    },
    isCustom () {
      return this.typeExist('custom')
    }
  },
  methods: {
    handleInput (e) {
      this.$emit('input', this.content)
    },
    typeExist (param) {
      if (Array.isArray(param)) {
        return param.some(v => this.type === v)
      } else {
        return this.type === param
      }
    },
    optionVal (data) {
      return typeof data === 'string'
        ? data : data.value
    },
    optionLabel (data) {
      if (typeof data === 'string') {
        return data
      } else {
        return data.label || data.value
      }
    }
  }
}
</script>
