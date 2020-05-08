<template>
  <div>
    <Row>
      <Card>
        <p slot="title">find-node</p>
        <div slot="extra">
          <Button type="primary" @click="findNode">查询provider</Button>
        </div>
        <Form :label-width="120" label-position="right" @keydown.native.enter.prevent="save">
          <FormItem label="注册中心" required>
            <Input placeholder="请输入查询的注册中心" v-model.trim="zkAddr" clearable/>
          </FormItem>
          <FormItem label="接口名称" required>
            <Input placeholder="请输入查询的provider接口名称" v-model.trim="serviceName" clearable/>
          </FormItem>
        </Form>
        <order-box>
          <order-item label="applicationName：">{{data.applicationName}}</order-item>
          <order-item label="group：">{{data.group}}</order-item>
          <order-item label="interfaceName：">{{data.interfaceName}}</order-item>
          <order-item label="serverIps：">{{data.serverIps}}</order-item>
          <order-item label="version：">{{data.version}}</order-item>
          <order-item label="methodNames：">
            <Select style="width: 300px;" v-model="methodName" placeholder="请选择方法名称">
              <Option v-for="item in data.methodNames" :value="item" :key="item">{{ item }}</Option>
            </Select>
          </order-item>
        </order-box>
      </Card>
    </Row>

    <Row>
      <Card>
        <p slot="title">test-node</p>
        <div slot="extra">
          <Button type="primary" @click="testNode">测试接口</Button>
        </div>
        <Form :label-width="120" label-position="right" @keydown.native.enter.prevent="save">
          <FormItem label="请求实体类" required>
            <Input v-model="argType" type="text" placeholder="Enter something..." />
          </FormItem>
          <FormItem label="请求参数" required>
            <Input v-model="requestParams" type="textarea" :autosize="{minRows: 2,maxRows: 5}" placeholder="Enter something..." />
          </FormItem>
        </Form>
        <order-item label="响应参数：">
          <Input v-model="responseParams" type="textarea" :autosize="{minRows: 5,maxRows: 20}" placeholder="Enter something..." />
        </order-item>
      </Card>
    </Row>
  </div>
</template>

<script>

  import {ajaxFindNode,testNode} from '@/api/data'
  import OrderBox from './example.vue'
  import OrderItem from "./OrderItem";

  export default {
    name: 'home',
    components: {
      OrderItem,
      OrderBox
    },
    data() {
      return {
        baseParams:{"appId":"SZ_INVOKE","reqId":"SZ_INVOKE_0520200405015124BXKpphE2MGHIm","requestTime":new Date().getTime()},
        requestParams:'',
        serviceName: '', //
        argType:'',
        methodName: '', //
        params:'',
        responseParams:'',
        zkAddr: '47.100.94.134:2181', //
        data:
          {
            applicationName: 'IPaymentDataService',
            group:
              'default',
            interfaceName:
              'com.wayue.payment.api.service.IPaymentDataService',
            methodNames:
              [
                'getPaymentChannelConfig',
                'getRefundOrderInfo',
                'getPaymentBillInfo',
                'getPaymentChannelConfigForCashier',
                'getDealInfoAllStatus',
                'getDealInfo'
              ],
            serverIps:
              [
                '127.0.0.1:20162'
              ],
            serviceAddr:
              'dubbo://127.0.0.1:20162/com.wayue.payment.api.service.IPaymentDataService?anyhost=true&application=app-sz-payment-service&dubbo=2.6.0&generic=false&interface=com.wayue.payment.api.service.IPaymentDataService&methods=getPaymentChannelConfig,getRefundOrderInfo,getPaymentBillInfo,getPaymentChannelConfigForCashier,getDealInfoAllStatus,getDealInfo&pid=7841&revision=1.0.0&service.filter=dubboLogFilter&side=provider×tamp=1587455552825&version=1.0.0',
            version:
              '1.0.0'
          }
      }
    },
    mounted() {

      //
    },

    /* 三.内部功能函数 */
    methods: {
      /* ----------------------事件调用函数------------------------ */
      // 点击保存
      findNode() {
        this.ajaxFindNode()
      },

      /* ----------------------内部功能函数------------------------ */

      /* ----------------------服务请求函数------------------------ */
      ajaxFindNode() {
        if (!this.serviceName || !this.zkAddr) {
          this.$Message.warning('请填写接口名称|注册中心地址')
          return
        }
        ajaxFindNode({serviceName: this.serviceName,zkAddr:this.zkAddr}).then(res => {
          console.log(res);
          this.data = res.data
          this.methodName = ''
        })
      },
      testNode() {
        if (!this.serviceName || !this.zkAddr) {
          this.$Message.warning('请填写接口名称|注册中心地址')
          return
        }
        const temp =`{${this.requestParams}}`

        Object.assign(this.baseParams,JSON.parse(temp));
        console.log(this.baseParams);
        testNode({
          interfaceName:this.serviceName,
          methodName:this.methodName,
          argType:this.argType,
          argObject:this.baseParams,
          version:this.data.version
        }).then(res => {

          console.log(res);
          this.responseParams = JSON.stringify(res.data,null,4)
        })
      }
    }
  }
</script>

<style lang="less">
  .count-style {
    font-size: 50px;
  }
</style>
