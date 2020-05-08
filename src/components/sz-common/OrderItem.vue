/*
* @Author: wangqiao
* @Date:
* @Last Modified by: wangqiao
* @Last Modified time:
* @desc: 用于展示详情页的信息展示
*/
<template>
  <p class="order-info">
    <label v-if="label" :style="{width: boxLabelWidth + 'px', 'font-weight': fontWeight}" >
      <i v-if="required" style="color: red;"> * </i>
      {{label}}
    </label>
    <span>
      <slot></slot>
    </span>
  </p>
</template>
<script>
export default {
  inject: {
    boxLabelWidth: {
      type: Number,
      default: 100
    }
  },
  props: {
    label: {
      type: String,
      default: ''
    },
    required: {
      type: Boolean,
      default: false
    },
    fontWeight: {
      type: String,
      default: 'bold'
    }
  }
}
</script>

<style scoped lang="scss">
  .order-info {
    display: flex;
    padding: 5px 0;
    line-height: 25px;
    label {
      display: inline-block;
      width: 100px;
      margin-right: 10px;
      text-align: right;
    }
    span{
      display: inline-block;
      width: 90%;
      word-wrap: break-word;
    }
  }
</style>
