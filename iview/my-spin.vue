<style lang="less">
  @keyframes ani-spin {
    from {
      transform: rotate(0deg);
    }
    50%  {
      transform: rotate(180deg);
    }
    to   {
      transform: rotate(360deg);
    }
  }
  .my-spin {
    cursor: default;
    user-select: none;
  }
  .my-spin-icon-load {
    animation: ani-spin 1s linear infinite;
  }
</style>

<template>
  <Spin fix class="my-spin">
    <Icon type="ios-loading" size="32" class="my-spin-icon-load"></Icon>
    <div>
      <slot></slot>
    </div>
  </Spin>
</template>

<script>
export default {

}
</script>
