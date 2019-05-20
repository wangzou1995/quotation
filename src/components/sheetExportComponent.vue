<template>
  <div>
  <el-row :gutter="20">
    <el-col :span="6"><div class="grid-content bg-purple">
      <div class="block" >
        <el-date-picker
          v-model="time"
          align="right"
          type="date"
          placeholder="生效日期"
          value-format="yyyy-MM-dd"
          :picker-options="pickerOptions1">
        </el-date-picker>
      </div>
    </div></el-col>
    <el-col :span="4"><div class="grid-content bg-purple">
      <el-select
        v-model="companyList"
        multiple
        filterable
        collapse-tags
        style="margin-left: 20px;"
        placeholder="揽收仓"
        multiple-limit= 5
      >
        <el-option
          v-for="item in companyOptions"
          :key="item.companyCode"
          :label="item.companyName"
          :value="item.companyCode"
        >
        </el-option>
      </el-select>
    </div></el-col>
  </el-row>
    <el-row :gutter="17">
      <el-col :span="5"><div class="grid-content bg-purple">
        <el-select
          v-model="productType"
          collapse-tags
          filterable
          style="margin-left: 20px;"
          value="1"
          placeholder="产品类型">
          <el-option
            v-for="item in productTypeOptions"
            :key="item.code"
            :label="item.name"
            :value="item.code">
          </el-option>
        </el-select>
      </div></el-col>
      <el-col :span="7"><div class="grid-content bg-purple" style="margin-left: -20px">
        <el-select
          v-model="productList"
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
            :value="item.productCode">
          </el-option>
        </el-select>
      </div></el-col>
      <el-col :span="3" style="font-size: 12px; text-align: left; margin-top: 10px">
        优惠报价:
        <el-switch
          v-model="discount"
        >
        </el-switch>
      </el-col>
      <el-col :span="2"><div class="grid-content bg-purple">
        <el-button type="primary" @click="exportSheet()">导出</el-button>
      </div></el-col>
      <el-col :span="2"><div class="grid-content bg-purple">
        <el-button type="primary" @click="reset()">重置</el-button>
      </div></el-col>
    </el-row>
  <el-row v-if="tableData2.length !== 0">
    <el-col :span="24">
      <div>
      <el-table
        :data="tableData2"
        :row-class-name="tableRowClassName"
        style="width: 100%"
        height="250"
        max-height="100%">
        <el-table-column
          prop="companyCodeFrom"
          label="揽收仓"
          width="180">
        </el-table-column>
        <el-table-column
          prop="companyCodeTo"
          label="口岸仓"
          width="180">
        </el-table-column>
        <el-table-column
          prop="productCode"
          label="产品">
        </el-table-column>
      </el-table>
        </div>
    </el-col>
  </el-row>
  </div>
</template>

<script>
import axios from 'axios'
import fileDownload from 'js-file-download'
let isPro = process.env.NODE_ENV === 'production'
if (isPro) {
  axios.defaults.baseURL = 'http://124.251.104.88:8079'
} else {
  axios.defaults.baseURL = '/api'
}
export default {
  name: 'sheetExportComponent',
  data () {
    return {
      pickerOptions1: {
        shortcuts: [{
          text: '今天',
          onClick (picker) {
            picker.$emit('pick', new Date())
          }
        }, {
          text: '昨天',
          onClick (picker) {
            const date = new Date()
            date.setTime(date.getTime() - 3600 * 1000 * 24)
            picker.$emit('pick', date)
          }
        }, {
          text: '一周前',
          onClick (picker) {
            const date = new Date()
            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7)
            picker.$emit('pick', date)
          }
        }]
      },
      value1: '',
      time: null,
      productTypeOptions: [{
        code: '',
        name: ''
      }],
      companyOptions: [{companyCode: '', companyName: ''}],
      productOptions: [{productCode: '', productName: ''}],
      productType: '1',
      companyList: [],
      productList: [],
      tableData2: [],
      discount: false
    }
  },
  mounted () {
    axios.get('/companyList').then(response => {
      this.companyOptions = response.data
    }).catch(error => { console.log(error) })
    axios.get('/productTypeList').then(response => {
      this.productTypeOptions = response.data
    }).catch(error => { console.log(error) })
    axios.get('/productList/0').then(response => {
      this.productOptions = response.data
    }).catch(error => { console.log(error) })
  },
  methods: {
    reset () {
      this.companyList = []
      this.productList = []
      this.productType = {}
      this.time = null
    },
    exportSheet () {
      let postEntity = {
        companyCodes: this.companyList,
        productCodes: this.productList.length === 0 ? null : this.productList,
        type: this.productType,
        effectiveTime: this.time,
        isLine: this.productType === '1' ? 1 : 0,
        discount: this.discount
      }
      if (postEntity.companyCodes.length === 0 || postEntity.effectiveTime === null) {
        this.$alert('请选择条件！', '警告', {
          confirmButtonText: '确定'
        })
      } else {
        const loading = this.$loading({
          lock: true,
          text: 'Loading',
          spinner: 'el-icon-loading',
          background: 'rgba(0, 0, 0, 0.7)',
          target: document.querySelector('.wish')
        })
        axios.post('/download', postEntity, {
          responseType: 'arraybuffer'
        }).then(function (response) {
          let fileName = 'download.zip'
          fileDownload(response.data, fileName)
          loading.close()
        }).catch(error => {
          loading.close()
          console.log(error)
        })
      }
      // let _this = this
      // axios.post('/export', postEntity).then(function (response) {
      //   _this.tableData2 = response.data
      // }).catch(error => {
      //   console.log(error)
      // })
    },
    tableRowClassName ({row, rowIndex}) {
      if (rowIndex === 1) {
        return 'warning-row'
      } else if (rowIndex === 3) {
        return 'success-row'
      }
      return ''
    }
  }
}
</script>

<style scoped>
  .el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    border-radius: 4px;
    text-align: center;
  }
  .bg-purple-dark {
  }
  .bg-purple {
  }
  .bg-purple-light {
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
  }
</style>
