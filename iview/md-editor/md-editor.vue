<template>
  <i-editor :value="value"
    :affix="affix"
    :offset-top="offsetTop"
    :placeholder="placeholder"
    :autosize="autosize"
    :write-name="writeName"
    :change-scroll="changeScroll"
    :show-summary="showSummary"
    :config="config"
    :before-upload="beforeUpload"
    :img-url="imgUrl"
    :highlight="highlight"
    :paste="paste"
    @input="onInput" />
</template>

<script>
import hljs from 'highlightjs/highlight.pack.js'
export default {
  props: {
    value: {
      type: String,
      default: ''
    },
    /**
     * 是否固定，开启将导航条固定顶端
     */
    affix: Boolean,
    /**
     * 固定时，距离顶端的距离
     */
    offsetTop: Number,
    /**
     * 占位
     */
    placeholder: String,
    /**
     * 同 iView Input 组件的 autosize
     */
    autosize: {
      type: Object,
      default () {
        return {
          minRows: 2
        }
      }
    },
    /**
     * 多國語言
     */
    i18n: {
      type: Boolean,
      default: true
    },
    /**
     * 多國語言設定 前綴
     */
    i18nPrefix: {
      type: String,
      default: 'i-ed-'
    },
    /**
     * 写作的标题
     */
    writeName: String,
    /**
     * 点击预览时，是否将页面跳转至预览的顶端
     */
    changeScroll: Boolean,
    /**
     * 显示摘要
     */
    showSummary: {
      type: Boolean,
      default: false
    },
    /**
     * 上传的配置
     */
    config: {
      type: Object,
      default () {
        return {
          action: '/',
          maxSize: 2048
        }
      }
    },
    /**
     * 是否支持粘贴上传
     */
    paste: {
      type: Boolean,
      default: false
    },
    /**
     * 选择图片后，上传前的操作，接收值 file，需返回 Promise，在使用七牛等上传服务时需要在这里获取 token 和 key，并赋值给 config.uploadForm
     */
    beforeUpload: {
      type: Function,
      default () {
        return true
      }
    },
    /**
     * 上传图片成功后，返回 res，需要自己返回一个图片的完整 URL
     */
    imgUrl: {
      type: Function,
      default (res) {
        return res
      }
    },
    /**
     * 自定义预览里的代码块使用的言语解析，接收值 code，如果使用 highlightjs，iView Editor 默认集成了样式
     */
    highlight: {
      type: Function,
      default (code) {
        return hljs.highlightAuto(code).value
      }
    },
    whitelist: {
      type: Array,
      default () {
        return ['iframe']
      }
    },
    renderer: {
      type: Object,
      default () {
        return {}
      }
    }
  },
  methods: {
    onInput (value) {
      this.$emit('input', value)
    }
  }
}
</script>
