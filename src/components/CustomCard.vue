import fileDownload from "js-file-download"
<template>
    <el-form :inline="true" ref="formCustom" :model="formCustom" label-width="80px">
      <el-form-item  label="揽收仓" prop="companyCodes">
        <el-select
          v-model="formCustom.companyCodes"
          multiple
          filterable
          collapse-tags
          placeholder="揽收仓"
          @change="change"
        >
          <el-option
            v-for="item in companyOptions"
            :key="item.companyCode"
            :label="item.companyName"
            :value="item.companyCode"
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
  name: 'CustomCard',
  data () {
    return {
      companyOptions: [{companyCode: '', companyName: ''}],
      formCustom: {
        companyCodes: [],
        effectTime: ''
      },
      oldChooseData: []
    }
  },
  methods: {
    resetForm (formName) {
      this.$refs[formName].resetFields()
    },
    change (val) {
      const allValues = this.companyOptions.map(item => {
        return item.companyCode
      })
      // 用来储存上一次选择的值，可进行对比
      const oldVal = this.oldChooseData.length > 0 ? this.oldChooseData : []

      // 若选择全部
      if (val.includes('ALL_SELECT')) {
        this.formCustom.companyCodes = allValues
      }

      // 取消全部选中， 上次有， 当前没有， 表示取消全选
      if (oldVal.includes('ALL_SELECT') && !val.includes('ALL_SELECT')) {
        this.formCustom.companyCodes = []
      }

      // 点击非全部选中，需要排除全部选中 以及 当前点击的选项
      // 新老数据都有全部选中
      if (oldVal.includes('ALL_SELECT') && val.includes('ALL_SELECT')) {
        const index = val.indexOf('ALL_SELECT')
        val.splice(index, 1) // 排除全选选项
        this.formCustom.companyCodes = val
      }

      // 全选未选，但是其他选项都全部选上了，则全选选上
      if (!oldVal.includes('ALL_SELECT') && !val.includes('ALL_SELECT')) {
        if (val.length === allValues.length - 1) {
          this.formCustom.companyCodes = ['ALL_SELECT'].concat(val)
        }
      }

      // 储存当前选择的最后结果 作为下次的老数据
      this.oldChooseData = this.formCustom.companyCodes
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
          if (temp.companyCodes.includes('ALL_SELECT')) {
            const index = temp.companyCodes.indexOf('ALL_SELECT')
            temp.companyCodes.splice(index, 1)
          }
          axios.post('/sheet/cpc/download', temp, {
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
    axios.get('/sheet/api/companyList').then(response => {
      let temp = [{'companyCode': 'ALL_SELECT', 'companyName': '全公司'}]
      temp.push(...response.data)
      console.log(temp)
      this.companyOptions = temp
    }).catch(error => { console.log(error) })
  }
}
</script>

<style scoped>

</style>
