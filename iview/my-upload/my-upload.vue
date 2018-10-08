<style lang="less">
  @import './my-upload.less';
</style>

<template>
  <div>
    <ul class="upload-list" v-if="isListShow">
      <li v-for="(item, index) in uploadList" :key="item.name" class="list-item">
        <div class="list-item-inner">
          <span>{{ item.name }}</span>
          <Button type="text" icon="md-trash" @click="uploadListItemRemove(index)"></Button>
        </div>
      </li>
    </ul>

    <Upload :name="name"
        ref="upload"
        class="upload"
        :action="action"
        type="drag"
        :accept="accept"
        :format="formet"
        :max-size="max_size"
        :show-upload-list="false"
        :default-file-list="defaultList"
        :on-format-error="handleFormatError"
        :on-exceeded-size="handleMaxSize"
        :before-upload="handleUpload">

      <div class="upload-inner">
        <Icon type="md-cloud-upload" size="52" style="color: #3399ff"></Icon>
        <p>{{ btnSelectText }}</p>
      </div>
    </Upload>
  </div>
</template>

<script>
import originalAxios from 'axios'

export default {
  props: {
    // file組件名稱
    name: {
      type: String,
      default: 'file'
    },
    // 上傳目標網址
    action: {
      type: String,
      default: '/upload'
    },
    // 其餘欄位
    data: {
      type: Object,
      default: () => {}
    },
    // 接受上傳的文件類型(MIME types)
    accept: {
      type: String,
      default: '' // 'image/png, image/jpeg, image/jpg'
    },
    // 接受上傳的文件類型(副檔名)
    formet: {
      type: Array,
      default: () => {
        return ['jpg', 'jpeg', 'png']
      }
    },

    // 上傳檔案列表
    isListShow: {
      type: Boolean,
      default: true
    },
    defaultList: {
      type: Array,
      default: () => []
    },

    // 文字
    btnSelectText: {
      type: String,
      default: '請選擇上傳檔案'
    }
  },
  data () {
    return {
      file: null,
      uploadList: [],
      v_defaultList: this.defaultList,
      max_size: 512
    }
  },
  methods: {
    // Api
    post_file (formData, callback, error) {
      originalAxios.post(this.action, formData).then(res => {
        if (callback) callback(res.data)
      }).catch(() => {
        if (error) error()
      })
    },

    // Event methods
    handleUpload (file) {
      // 判斷檔案格式
      if (!this.formet.some(val => file.type.match(val)) && this.formet.length) {
        this.handleFormatError(file)
        return false
      }

      // 判斷檔案大小
      if (this.max_size < (file.size / 1000)) {
        this.handleMaxSize(file)
        return false
      }

      this.file = file
      this.upload(() => {
        this.file = null
      })
      return false
    },
    upload () {
      // 一次只可上傳一張圖片，再次上傳的將覆蓋掉(可變更)
      const check = this.uploadList.length !== 0
      this.uploadList.splice(0, check ? 1 : 0, this.file)

      let fileDatas = new FormData()
      // 一次只可上傳一張圖片(可變更)
      fileDatas.append(this.name, this.uploadList[0])
      for (const key in this.data) {
        fileDatas.append(key, this.data[key])
      }

      const callFunc = () => {
        this.uploadList.length = 0
        this.file = null
      }
      this.post_file(fileDatas, fileSrc => {
        callFunc()
        this.$emit('on-success', fileSrc)
      }, () => {
        callFunc()
        this.$emit('on-error')
      })
    },
    uploadListItemRemove (id) {
      this.uploadList.splice(id, 1)
    },

    // Function
    handleFormatError (file) {
      const formetStr = this.formet
        .filter(val => val !== 'jpeg')
        .map(val => `${val}檔`)
        .join('、')
      this.$Notice.warning({
        title: '檔案格式不正確',
        desc: `${file.name} 檔案格式不正確，只能上傳${formetStr}`
      })
    },
    handleMaxSize (file) {
      const fileMaxSize = Math.round(this.max_size / 100) * 100
      this.$Notice.warning({
        title: '檔案大小超過限制',
        desc: `${file.name} 檔案大小不能超過 ${fileMaxSize}kb`
      })
    }
  },
  mounted () {
    this.uploadList = this.$refs.upload.fileList
  }
}
</script>
