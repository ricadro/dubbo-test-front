/*
* @Author: wangqiao
* @Date:
* @Last Modified by: wangqiao
* @Last Modified time:
* @desc: 用于展示详情页的信息展示
*/
<template>
  <div class="order-box">
    <h5 v-if="title" :class="{'has-border': ifBorder}">{{title}}<slot name="extra"></slot></h5>
    <slot></slot>
  </div>
</template>
<script>
export default {
  provide () {
    return {
      boxLabelWidth: this.labelWidth
    }
  },
  props: {
    title: {
      type: String,
      default: ''
    },
    labelWidth: {
      type: Number,
      default: 100
    },
    ifBorder: {
      type: Boolean,
      default: true
    }
  }
}
</script>

<style scoped lang="scss">
.order-box {
  h5 {
    line-height: 40px;
    font-size: 14px;
    margin: 10px 0;
    color: #17233d;
    &.has-border{
      border-bottom: 1px solid #eee;
    }
  }
}
</style>
