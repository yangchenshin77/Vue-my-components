<template>
  <Form ref="form"
    :model="form"
    :rules="rules"
    :label-position="labelPosition"
    :label-width="labelWidthShow && labelPosition ? labelWidth : undefined"
    @submit.native.prevent
    @keydown.enter.native="handleSubmit">

    <ICFormItem v-for="(item, key) in data"
      :key="key"
      v-model="form[key]"
      :data="getData(key)"
      :label-show="labelShow"
      :enterText="enterText"
      :selectText="selectText"
      :endText="endText">
      <template :slot="item.type" slot-scope="{ item }">
        <slot :name="key" :item="item"></slot>
      </template>
    </ICFormItem>

    <slot name="footer" :submit="handleSubmit">
      <FormItem>
        <Button type="primary" @click="handleSubmit">送出</Button>
      </FormItem>
    </slot>
  </Form>
</template>

<script>
import config from '@/config'
import ICFormItem from './ic-form-item.vue'
export default {
  name: 'icForm',
  components: {
    ICFormItem
  },
  props: {
    value: {
      type: Object,
      default () {
        return {}
      }
    },
    data: {
      type: Object,
      default () {
        return {}
      }
    },
    rules: {
      type: Object,
      default () {
        return {}
      }
    },
    labelShow: {
      type: Boolean,
      default: true
    },
    labelPosition: {
      type: String,
      default: 'right'
    },
    labelWidth: {
      type: Number,
      default: 100
    }
  },
  data () {
    return {
      form: this.value
    }
  },
  computed: {
    enterText () {
      return config.useI18n ? this.$t('pleaseEnter') : undefined
    },
    selectText () {
      return config.useI18n ? this.$t('pleaseSelect') : undefined
    },
    endText () {
      return config.useI18n ? this.$t('enterEndText') : undefined
    },
    labelWidthShow () {
      return this.labelShow && this.labelPosition !== 'top'
    }
  },
  watch: {
    value: {
      handler (val) {
        this.form = val
      },
      deep: true
    },
    form: {
      handler (val) {
        this.$emit('input', val)
      },
      deep: true
    }
  },
  methods: {
    handleSubmit () {
      this.$refs.form.validate(valid => {
        if (valid) {
          this.$emit('on-submit', this.form)
        }
      })
    },
    getData (key) {
      return Object.assign(this.data[key], {
        prop: key
      })
    }
  }
}
</script>
