/*
* @Author: wangqiao
* @Date:
* @Last Modified by: wangqiao
* @Last Modified time:
* @desc:
*/
<template>
  <div class="">
    <Upload
      style="margin: 0 10px 10px 0; display: flex; align-items: flex-end;"
      :action="importAction"
      :show-upload-list="showUploadList"
      :data="dataParams"
      :format="requireTypeArray"
      :before-upload="handleBeforeUpload"
      :on-success="uploadSuccess"
      :on-error="uploadError"
      :max-size="51200"
      :on-exceeded-size="handleMaxSize"
      multiple
    >
      <Button
        icon="ios-cloud-upload-outline"
        :type="color"
      >{{importText}}</Button>
    </Upload>
  </div>
</template>

<script>
import config from '@/config'
import excel from '@/libs/excel'
import { getUserToken, getUserId } from '@/libs/util'
export default {
  name: '',
  components: {},
  props: {
    showUploadList: {
      type: Boolean,
      default: true
    },
    importText: {
      type: String,
      default: '导入'
    },
    actionUrl: {
      type: String,
      default: ''
    },
    params: {
      type: Object,
      default () {
        return {}
      }
    },
    // 对传入的文件做校验，默认是xls和xlsx
    requireTypeArray: {
      type: Array,
      default () {
        return ['xlsx', 'xls']
      }
    },
    color: {
      type: String,
      default: 'default'
    }
  },
  data () {
    return {
      dataParams: {},
      uploadLoading: false,
      progressPercent: 0,
      showProgress: false,
      showRemoveFile: false,
      file: null,
      reachLimt: true,
      addNum: 0
    }
  },
  computed: {
    importAction () {
      return `${config.baseUrl.pro}/${this.actionUrl}`
    }
  },
  created () {
    this.dataParams = {
      ...this.params,
      szUserId: getUserId(),
      szUserToken: getUserToken(),
      enctype: 'multipart/form-data;charset=utf-8' // 规定对表单元素如何进行编码
    }
  },
  methods: {
    // 检验文件类型
    handleBeforeUpload (file) {
      const fileExt = file.name
        .split('.')
        .pop()
        .toLocaleLowerCase()
      if (this.requireTypeArray.includes(fileExt)) {
        this.readFile(file)
        this.file = file
        this.$emit('beforeUpload', file)
      } else {
        this.$Notice.warning({
          title: '文件类型错误',
          desc: `文件：${file.name}与要求的格式不一致，请选择对应后缀的文件。`
        })
      }
    },

    handleMaxSize (file) {
      this.$Notice.warning({
        title: '文件太大了',
        desc: `文件：${file.name} 超过了50M`
      })
    },

    uploadSuccess (res, file) {
      if (res.SZ_HEAD.RESP_CODE === 'S0000') {
        this.$Message.success(file.name + '导入成功')
        this.$emit('updateData', res)
      } else if (res.SZ_HEAD.RESP_CODE === 'B0205') {
        this.$Message.warning('登录状态超时，请重试')
        setTimeout(() => {
          location.reload()
        }, 1500)
      } else {
        this.$Modal.error({
          title: '导入数据有误',
          content: res.SZ_HEAD.RESP_MSG
        })
      }
    },
    //
    uploadError (error, file) {
      console.log('params\n', this.params)
      // 上传失败
      console.log(error)
      this.$Message.error(file.name + '导入失败')
    },
    // 读取文件
    readFile (file) {
      const reader = new FileReader()
      reader.readAsArrayBuffer(file)
      reader.onloadstart = e => {
        this.uploadLoading = true
        this.showProgress = true
      }
      reader.onprogress = e => {
        this.progressPercent = Math.round((e.loaded / e.total) * 100)
      }
      reader.onerror = e => {
        this.$Message.error('文件读取出错')
      }
      reader.onload = e => {
        const data = e.target.result
        console.log(data)
        const { header, results } = excel.read(data, 'array')
        const tableTitle = header.map(item => {
          return { title: item, key: item }
        })
        this.uploadLoading = false
        console.log(results)
        console.log(tableTitle)
      }
    }
  }
}
</script>

<style scoped lang="scss">
</style>
