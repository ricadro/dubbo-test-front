/*
* @Author: wangqiao
* @Date: 2018/12/19
* @Last Modified by: wangqiao
* @Last Modified time: 2018/12/19
* @desc: 查询机构列表
*/
<template>
  <!--选择机构抽屉-->
  <Drawer :closable="false" scrollable width="80%" v-model="institutionShow" class-name="instit-drawer" @keydown.native.enter.prevent="saveSelectedInstit">
    <p :style="pStyle">选择机构</p>
    <Divider/>
    <Form :label-width="100" label-position="right" inline>
      <FormItem label="选择省份" style="width: 100%;" required>
        <Select v-model="provinceCode" @on-change="changeProvince" @on-clear="clearProvince" clearable>
          <Option v-for="item in provinces" :value="item.regionCode" :key="'province' + item.regionCode">{{ item.regionChnName }}</Option>
        </Select>
      </FormItem>
      <FormItem label="选择城市" style="width: 100%;">
        <Select v-model="regionCode" clearable>
          <Option v-for="item in cities" :value="item.regionCode" :key="'city' + item.regionCode">{{ item.regionChnName }}</Option>
        </Select>
      </FormItem>
      <!--<FormItem label="品牌" style="width: 100%;">-->
      <!--<Input class="cell-input" placeholder="请输入" v-model="brand" clearable/>-->
      <!--</FormItem>-->
      <FormItem label="机构关键字" style="width: 100%;">
        <Input class="cell-input" placeholder="请输入" v-model="institName" clearable/>
      </FormItem>
      <Row type="flex" justify="end">
        <Button type="primary" style=" height: 32px;" @click="searchList">搜索</Button>
      </Row>
    </Form>
    <div style="margin: 20px; overflow: hidden" class="page-box">
      <div style="float: right;">
        <Page :total="totalData" :current="goPage" :page-size="pageSize" :page-size-opts="pageSizeOpts" @on-change="changePage" @on-page-size-change="changePageSize" ref="page" show-sizer show-total/>
      </div>
    </div>
    <Table highlight-row :columns="institutionDrawerColumns" :data="institList" stripe border :loading="loading" style="margin: 10px 0;"></Table>
    <drawer-footer>
      <Button style="margin-right: 10px;" @click="institutionShow = false">取消</Button>
      <Button type="primary" @click="saveSelectedInstit">保存</Button>
    </drawer-footer>
  </Drawer>
</template>

<script>
import { getRegionList, getInstitList } from '@/api/operate_center/order_manage'
import DrawerFooter from '_c/sz-common/DrawerFooter'
export default {
  name: 'instit_comp',
  components: {
    DrawerFooter
  },
  props: {
    orderCode: {
      type: String
    }
  },
  data () {
    return {
      pStyle: {
        fontSize: '16px',
        color: 'rgba(0,0,0,0.85)',
        lineHeight: '36px',
        display: 'block'
      },
      institutionShow: false,
      provinces: [], // 省份列表
      provinceCode: '',
      l2Cities: [], // 所有支持的城市列表
      cities: [], // 城市列表
      regionCode: '',
      institName: '',
      brand: '',
      goPage: 1,
      pageSize: 10,
      pageSizeOpts: [10, 50, 100, 500],
      loading: false,
      institList: [], // 机构列表
      totalData: 0,
      institCode: '',
      selectedInstitData: null,
      institutionDrawerColumns: [
        {
          title: '选择',
          width: 90,
          align: 'center',
          render: (h, params) => {
            let defaultSelect = this.institCode === params.row.institCode
            return h('div', [
              h('Radio', {
                props: {
                  value: defaultSelect
                },
                on: {
                  input: () => {
                    this.institCode = params.row.institCode
                    this.selectedInstitData = params.row
                  }
                }
              })
            ])
          }
        },
        { title: '机构编号', key: 'institCode', align: 'center' },
        { title: '机构名称', key: 'institName', align: 'center' },
        { title: '机构地址', key: 'institAddr', align: 'center' }
      ] // 机构表头
    }
  },
  created () {

  },
  computed: {},
  methods: {

    // 获取省市列表
    getRegionList () {
      this.institutionShow = true
      getRegionList({
        orderCode: this.orderCode
      }).then(res => {
        if (res.data.SZ_HEAD.RESP_CODE === 'S0000') {
          let SZ_BODY = res.data.SZ_BODY
          this.provinces = SZ_BODY.l1Cities
          this.l2Cities = SZ_BODY.l2Cities
        } else {
          this.$Message.error(res.data.SZ_HEAD.RESP_MSG)
        }
      })
    },

    searchList () {
      if (!this.provinceCode) {
        this.$Message.warning('请先选择省份')
        return
      }
      this.goPage = 1
      this.getInstitList()
    },

    // 获取机构列表
    getInstitList () {
      this.loading = true
      getInstitList({
        provinceCode: this.provinceCode,
        regionCode: this.regionCode,
        orderCode: this.orderCode,
        institName: this.institName,
        brand: this.brand,
        goPage: this.goPage,
        pageSize: this.pageSize
      }).then(res => {
        if (res.data.SZ_HEAD.RESP_CODE === 'S0000') {
          let SZ_BODY = res.data.SZ_BODY
          this.institList = SZ_BODY.institList
          this.totalData = SZ_BODY.totalData
        } else {
          this.$Message.error(res.data.SZ_HEAD.RESP_MSG)
        }
        this.loading = false
      })
    },

    // 选择 || 修改省份
    changeProvince () {
      this.regionCode = ''
      this.cities = this.l2Cities[this.provinceCode]
    },

    // 清空省份
    clearProvince () {
      this.provinceCode = ''
      this.cities = []
    },

    // 切换页码
    changePage () {
      this.goPage = this.$refs['page'].currentPage
      this.getInstitList()
    },
    // 切换每页条数
    changePageSize () {
      this.goPage = 1
      this.pageSize = this.$refs['page'].currentPageSize
      this.getInstitList()
    },

    // 保存选中的机构信息
    saveSelectedInstit () {
      if (!this.selectedInstitData) {
        this.$Message.warning('请选择一个机构')
        return
      }
      this.institutionShow = false
      this.$emit('saveInstit', this.selectedInstitData)
    }
  }

}
</script>

<style lang="scss">
.instit-drawer {
  .ivu-drawer-body {
    padding-bottom: 100px;
  }
}
</style>
