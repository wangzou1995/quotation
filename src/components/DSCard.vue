<template>
  <el-form :inline="true" ref="formCustom" :rules="rules" :model="formCustom" label-width="80px">
    <el-form-item label="产品名称" prop="productCodes">
      <el-select
        v-model="formCustom.productCodes"
        multiple
        collapse-tags
        filterable
        style="margin-left: 20px; "
        size="medium"
        placeholder="产品名称">
        <el-option
          v-for="item in productOptions"
          :key="item.productCode"
          :label="item.productName"
          :value="item.productCode"
        >
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="生效时间" prop="effectTime">
      <el-date-picker v-model="formCustom.effectTime" type="date"  value-format="yyyy-MM-dd" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit('formCustom')">下载</el-button>
      <el-button @click="resetForm('formCustom')">重置</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import axios from 'axios'
import fileDownload from 'js-file-download'
export default {
  name: 'WishCard',
  data () {
    return {
      productOptions: [],
      formCustom: {
        productCodes: null,
        effectTime: null
      },
      oldChooseData: [],
      rules: {
        productCodes: [
          {required: true, message: '请选择产品名称', trigger: 'blur'}
        ],
        effectTime: [
          {required: true, message: '请选择生效时间', trigger: 'blur'}
        ]
      }
    }
  },
  methods: {
    resetForm (formName) {
      this.$refs[formName].resetFields()
    },
    onSubmit (formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          const loading = this.$loading({
            lock: true,
            text: 'Loading',
            spinner: 'el-icon-loading',
            background: 'rgba(0, 0, 0, 0.7)',
            target: document.querySelector('.wish')
          })
          let temp = JSON.parse(JSON.stringify(this.formCustom))
          axios.post('/cpc/dsCard/download', temp, {
            responseType: 'arraybuffer'
          }).then(function (response) {
            let fileName = 'download.zip'
            fileDownload(response.data, fileName)
            loading.close()
          }).catch(error => {
            loading.close()
            console.log(error)
            this.$message({
              message: '选项必填',
              type: 'warning'
            })
          })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    }
  },
  created () {
    axios.get('/cpc/dsCard/allProduct').then(response => {
      this.productOptions = response.data.data
    }).catch(error => { console.log(error) })
  }
}
</script>

<style scoped>

</style>
