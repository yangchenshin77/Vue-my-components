<style lang="less" scoped>
  .ivu-empty-placeholder {
    position: relative;
    margin: auto;
    cursor: default;
    user-select: none;
    &::before {
      content: '';
      display: block;
      padding-top: 56.25%;
    }
  }
  .ivu-empty {
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #999999 !important;

    &-bg {
      background-color: #f6f6f6 !important;
      border: 1px solid #e0e0e0 !important;
      border-radius: 10px;
    }
  }
  .ivu-empty-inner {
    text-align: center;
  }
  .ivu-empty-text {
    margin-top: 8px !important;
  }
</style>

<template>
  <div class="ivu-empty-placeholder">
    <div :class="{ 'ivu-empty': true, 'ivu-empty-bg': bg }">
      <div class="ivu-empty-inner">
        <Icon :type="icon_list[type]" :size="size_list[size]"></Icon>
        <div class="ivu-empty-text" v-if="text">{{ text }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    type: String,
    size: {
      type: String,
      default: 'md'
    },
    text: {
      type: String,
      default: ''
    },
    bg: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      icon_list: {
        image: 'image',
        images: 'images'
      },
      size_list: {
        lg: 100,
        md: 60,
        sm: 40
      }
    }
  }
}
</script>
