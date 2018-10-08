<template>
  <div>
    <Button
      v-if="v_btn.show"
      :type="v_btn.type"
      :icon="v_btn.icon"
      @click="v_open">
      {{ v_btn.text }}
    </Button>

    <Modal v-model="v_show" :title="title" :closable="closable" :mask-closable="maskClosable">
      <div @keydown.enter="v_save">
        <slot>對話框內容</slot>
      </div>

      <div slot="footer">
        <Button type="text" @click="v_cancel">取消</Button>
        <Button type="primary"
          :disabled="v_desabled"
          :loading="v_loading"
          @click="v_save">確定</Button>
      </div>
    </Modal>
  </div>
</template>

<script>
export default {
  props: {
    // Modal
    show: {
      type: Boolean,
      default: false
    },
    closable: {
      type: Boolean,
      default: true
    },
    maskClosable: {
      type: Boolean,
      default: true
    },

    // Modal footer buttons
    loading: {
      type: Boolean,
      default: false
    },
    desabled: {
      type: Boolean,
      default: false
    },

    // Text
    title: {
      type: String,
      default: '對話框'
    },
    btnOptions: Object
  },
  data () {
    return {
      v_show: this.show,
      v_loading: this.loading,
      v_desabled: this.desabled,
      v_btn: {
        show: true,
        text: '開啟對話框',
        type: 'primary',
        icon: ''
      }
    }
  },
  watch: {
    show (val) { this.v_show = val },
    v_show (val) { this.$emit('update:show', val) },
    loading (val) { this.v_loading = val },
    v_loading (val) { this.$emit('update:loading', val) },
    desabled (val) { this.v_desabled = val },
    v_desabled (val) { this.$emit('update:desabled', val) }
  },
  methods: {
    v_open () {
      this.v_show = true
      this.$emit('on-open')
    },
    v_save () {
      // this.v_show = false;
      this.$emit('on-save')
    },
    v_cancel () {
      this.v_show = false
      this.$emit('on-cancel')
    }
  },
  mounted () {
    this.v_btn = Object.assign(this.v_btn, this.btnOptions || {})
  }
}
</script>
