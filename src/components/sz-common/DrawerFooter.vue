/*
* @Author: wangqiao
* @Date:
* @Last Modified by: wangqiao
* @Last Modified time:
* @desc: 用于抽屉底部展示
*/
<template>
  <div class="drawer-footer" :style="{width: labelWidth}">
    <slot></slot>
  </div>
</template>
<script>
export default {
  props: {
    labelWidth: {
      type: String,
      default: '80%'
    }
  }

}
</script>

<style scoped lang="scss">
  .drawer-footer {
    width: 80%;
    position: fixed;
    bottom: 0;
    right: 0;
    border-top: 1px solid #e8e8e8;
    padding: 10px 16px;
    text-align: right;
    background: #fff;
    z-index: 99;
  }
</style>
