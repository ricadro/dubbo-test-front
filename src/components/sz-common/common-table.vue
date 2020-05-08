
<template>
  <div class="sp-table">
    <Table
      :columns="columns"
      :data="orderList"
      ref="tables"
      stripe
      border
      :loading="loading"
      @on-selection-change="handleSelectionChange"
    ></Table>
    <div style="margin: 20px;overflow: hidden">
      <div style="float: right;">
        <Page
          v-if="showPage"
          class="page_wrapper"
          :total="total"
          :current="goPage"
          :page-size="spPageSize"
          :page-size-opts="pageSizeOpts"
          @on-change="handleChangePage"
          @on-page-size-change="handleChangePageSize"
          show-sizer
          show-total
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'common_table',
  data () {
    return {
      spGoPage: 1,
      spPageSize: 10
      // pageSizeOpts: [10, 50, 100, 500]
    }
  },
  props: {
    showPage: {
      type: Boolean,
      default: true
    },
    orderList: {
      type: Array,
      default () {
        return []
      }
    },
    loading: {
      type: Boolean,
      default () {

      }
    },
    // 为啥传递个数组需要一个方法执行返回呢？
    // iview是如何关联table和page的？
    columns: {
      type: Array,
      default () {
        return []
      }
    },
    total: {
      type: Number,
      default: 0
    },
    goPage: {
      type: Number,
      default: 1
    },
    pageSize: {
      type: Number,
      default: 10
    },
    pageSizeOpts: {
      type: Array,
      default () {
        return [10, 50, 100, 500]
      }
    },
    changePageName: {
      type: String,
      default: ''
    }
  },
  created () {

  },
  mounted () {
    this.spPageSize = this.pageSize
    this.spGoPage = this.goPage
  },
  methods: {
    handleChangePage (value) {
      this.spGoPage = value
      this.$emit('handleChangePage', this.spGoPage)
    },
    handleChangePageSize (value) {
      this.spGoPage = 1
      this.spPageSize = value
      this.$emit('handleChangePageSize', this.spPageSize)
    },
    handleSelectionChange (value) {
      this.$emit('handleSelectionChange', value)
    }
  }
}
</script>

<style lang="scss">
</style>
