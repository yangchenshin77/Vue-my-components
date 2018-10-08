<style lang="less">
  @import './my-upload-img-cropper.less';
</style>

<template>
  <div>
    <Upload :name="name"
        ref="upload"
        class="upload"
        :action="action"
        type="drag"
        :accept="accept"
        :format="formet"
        :max-size="max_size"
        :show-upload-list="false"
        :on-format-error="handleFormatError"
        :on-exceeded-size="handleMaxSize"
        :before-upload="handleUpload">

      <div class="upload-inner">
        <Icon type="md-cloud-upload" size="52" style="color: #3399ff"></Icon>
        <p>{{ btnSelectText }}</p>
      </div>
    </Upload>

    <my-modal
        :show.sync="modalShow"
        :title="modalCropperText"
        :closable="false"
        :maskClosable="false"
        :btnOptions="modalBtnOptions"
        @on-save="modalSave"
        @on-cancel="modalCancel">
      <div class="upload-cropimg-container">
        <img :id="v_name + '-cropimg'">
      </div>
    </my-modal>
  </div>
</template>

<script>
import Cropper from 'cropperjs'
import 'cropperjs/dist/cropper.min.css'
import MyModal from './my-modal.vue'

export default {
  props: {
    id: {
      type: String,
      default: 'my'
    },
    // file組件名稱
    name: {
      type: String,
      default: 'file'
    },
    // 大小限制
    maxSize: {
      type: Number,
      default: 1024
    },
    // 其餘欄位
    data: {
      type: Object,
      default: () => {}
    },

    // 文字
    btnSelectText: {
      type: String,
      default: '請選擇上傳檔案'
    },
    modalCropperText: {
      type: String,
      default: '剪裁圖片'
    },

    // 圖片
    imgConfig: {
      type: Object,
      default: () => {}
    },
    // 縮圖
    isMinImg: {
      type: Boolean,
      default: false
    },
    minImgConfig: {
      type: Object,
      default: () => {}
    }
  },
  components: { MyModal },
  data () {
    return {
      v_name: 'my-upload-img-cropper-' + this.id,

      // File
      file: null,
      minImgFile: null,
      imgDataURL: null,
      imgMinDataURL: null,

      // File data
      action: '/upload',
      accept: 'image/png, image/jpeg, image/jpg',
      formet: ['jpg', 'jpeg', 'png'],
      max_size: this.maxSize,

      // Modal
      modalBtnOptions: { show: false },
      modalShow: false,

      // Cropper
      cropper: null,
      cropperConfig: {
        aspectRatio: 16 / 9,
        viewMode: 2
      },

      // Img
      v_imgConfig: {
        width: 1280,
        height: 720
      },
      v_minImgConfig: {
        width: 640,
        height: 360
      }
    }
  },
  computed: {
    max_size_text () {
      // 單位 KB or MB
      const bitNumber = Math.pow(10, String(this.file_max_size).length - 1)
      const sizeKb = Math.round(this.file_max_size / bitNumber) * bitNumber
      const fileMaxSize = sizeKb >= 1000 ? (sizeKb / 1000) : sizeKb
      const fileSizeUnit = sizeKb >= 1000 ? 'MB' : 'KB'
      return fileMaxSize + fileSizeUnit
    }
  },
  methods: {
    // Event methods
    // 驗證檔案是否可上傳
    handleUpload (file) {
      // 判斷檔案格式
      if (!this.formet.some(val => file.type.match(val))) {
        this.handleFormatError(file)
        return false
      }
      // 判斷檔案大小
      if (this.max_size < (file.size / 1024)) {
        this.handleMaxSize(file)
        return false
      }
      this.file = file
      this.modalOpen() // 開啟剪裁圖片視窗
      return false
    },
    // 判斷檔案格式
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
    // 判斷檔案大小
    handleMaxSize (file) {
      this.$Notice.warning({
        title: '檔案大小超過限制',
        desc: `${file.name} 檔案大小不能超過 ${this.max_size_text}`
      })
    },
    // 上傳檔案
    upload () {
      // 將剪裁完成圖片的 Blob檔 及 dataURL 回傳
      let result = { blob: [], dataURL: [] }

      result.blob.push(this.file)
      result.dataURL.push(this.imgDataURL)

      if (this.isMinImg) {
        result.blob.push(this.minImgFile)
        result.dataURL.push(this.imgMinDataURL)
      }

      this.$emit('on-cropper-end', result)
    },

    // 剪裁圖片視窗
    modalOpen () {
      this.modalShow = true
      this.$emit('on-cropper-open')

      // 將圖片讀入剪裁圖片視窗視窗裡
      const img = document.getElementById(this.v_name + '-cropimg')

      setTimeout(() => {
        if (!this.cropper) {
          this.cropper = new Cropper(img, this.cropperConfig)
        }

        let reader = new FileReader()
        reader.onload = () => {
          this.cropper.replace(reader.result)
          reader.onload = null
        }
        reader.readAsDataURL(this.file)
      }, 100)
    },
    modalSave () {
      this.modalShow = false
      this.$emit('on-cropper-close')

      const uploadStart = () => {
        this.upload()
      }

      let cropperImgDataURL = this.cropper.getCroppedCanvas().toDataURL()

      // 剪裁完成的圖片重新設定長寬
      this.makeMinImgDataURI(
        cropperImgDataURL,
        this.v_imgConfig,
        this.file.type,
        imgDataURI => {
        // 剪裁完成的圖片轉成Blob
          this.imgDataURL = imgDataURI
          this.file = this.dataURItoBlob(imgDataURI)

          // 生成縮圖
          if (this.isMinImg) {
            this.makeMinImgDataURI(
              cropperImgDataURL,
              this.v_minImgConfig,
              this.file.type,
              minImgDataURI => {
              // 縮圖轉成Blob
                this.imgMinDataURL = minImgDataURI
                this.minImgFile = this.dataURItoBlob(minImgDataURI)
                uploadStart()
              }
            )
          } else {
            uploadStart()
          }
        })
    },
    modalCancel () {
      this.modalShow = false
      this.$emit('on-cropper-close')
      this.file = null
      this.minImgFile = null
    },

    // dataURI 轉 Blob
    dataURItoBlob (dataURI) {
      let byteString
      if (dataURI.split(',')[0].indexOf('base64') >= 0) {
        byteString = atob(dataURI.split(',')[1])
      } else {
        byteString = unescape(dataURI.split(',')[1])
      }
      const mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]

      let ia = new Uint8Array(byteString.length)
      for (let i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i)
      }
      return new Blob([ia], { type: mimeString })
    },
    // 生成不同大小的圖片(固定比例)
    makeMinImgDataURI (dataURI, imgConfig, type, callback) {
      // 計算固定比例的寬高在指定寬高裡的倍數
      const aspectRatio = (imgW, imgH, maxW, maxH) => {
        return Math.min((maxW / imgW), (maxH / imgH))
      }
      let minImgDataURI = ''
      let img = new Image()
      img.src = dataURI
      img.onload = () => {
        const canvas = document.createElement('canvas')
        const ctx = canvas.getContext('2d')
        canvas.width = imgConfig.width
        canvas.height = imgConfig.height
        const w = img.width
        const h = img.height
        const sizer = aspectRatio(w, h, canvas.width, canvas.height)
        ctx.drawImage(img, 0, 0, w, h, 0, 0, w * sizer, h * sizer)
        minImgDataURI = canvas.toDataURL(type, 1.0)
        if (callback) callback(minImgDataURI)
      }
    }
  },
  mounted () {
    this.file = this.$refs.upload.fileList[0]
    this.v_imgConfig = Object.assign(this.v_imgConfig, this.imgConfig)
    this.v_minImgConfig = Object.assign(this.v_minImgConfig, this.minImgConfig)
  }
}
</script>
