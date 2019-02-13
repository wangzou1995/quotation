<template>
  <div >
  <el-row id="wish" :gutter="20" v-loading>
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
    <el-col :span="7"><div class="grid-content bg-purple">
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
    <el-col :span="2"><div class="grid-content bg-purple">
      <el-button  type="primary" @click="exportSheet()"  >导出</el-button>
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
export default {
  name: 'sheetExportComponent',
  data () {
    return {
      pickerOptions1: {
        disabledDate (time) {
          return time.getTime() > Date.now()
        },
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
      time: '',
      productOptions: [{productCode: '', productName: ''}],
      productList: [],
      tableData2: [],
      loading: false
    }
  },
  mounted () {
    axios.defaults.baseURL = '/api'
    axios.get('/productList').then(response => {
      this.productOptions = response.data
    }).catch(error => { console.log(error) })
  },
  methods: {
    reset () {
      this.productList = []
      this.time = ''
    },
    exportSheet () {
      const loading = this.$loading({
        lock: true,
        text: 'Loading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)',
        target: document.querySelector('.wish')
      })
      axios.defaults.baseURL = '/wish'
      let postEntity = {
        data: {
          productCodes: this.productList.length === 0 ? null : this.productList,
          effectTime: this.time
        }
      }
      axios.post('/wishpricesheet/getQuotation', postEntity, {
        responseType: 'arraybuffer'
      }).then(function (response) {
        let fileName = window.URL.createObjectURL(new Blob([response])).replace('blob:http://localhost:8077/', '') + '.zip'
        fileDownload(response.data, fileName)
        loading.close()
      }).catch(error => {
        loading.close()
        console.log(error)
      })
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
