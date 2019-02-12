<template>
  <el-row :gutter="20">
    <el-col :span="6"><div class="grid-content bg-purple">
      <div class="block" >
        <el-date-picker
          v-model="value2"
          align="right"
          type="date"
          placeholder="生效日期"
          :picker-options="pickerOptions1">
        </el-date-picker>
      </div>
    </div></el-col>
    <el-col :span="4"><div class="grid-content bg-purple">
      <el-select
        v-model="companyList"
        multiple
        collapse-tags
        style="margin-left: 20px;"
        placeholder="揽收仓">
        <el-option
          v-for="item in companyOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </div></el-col>
    <el-col :span="4"><div class="grid-content bg-purple">
      <el-select
        v-model="productTypeList"
        multiple
        collapse-tags
        style="margin-left: 20px;"
        placeholder="产品类型">
        <el-option
          v-for="item in productTypeOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </div></el-col>
    <el-col :span="4"><div class="grid-content bg-purple">
      <el-select
        v-model="productTypeList"
        multiple
        collapse-tags
        style="margin-left: 20px;"
        placeholder="产品类型">
        <el-option
          v-for="item in productTypeOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </div></el-col>
    <el-col :span="3"><div class="grid-content bg-purple">
      <el-button type="primary">导出</el-button>
    </div></el-col>
    <el-col :span="3"><div class="grid-content bg-purple">
      <el-button type="primary">重置</el-button>
    </div></el-col>
  </el-row>
</template>

<script>
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
      value2: '',
      companyOptions: [{
        value: '选项1',
        label: '黄金糕'
      }, {
        value: '选项2',
        label: '双皮奶'
      }, {
        value: '选项3',
        label: '蚵仔煎'
      }, {
        value: '选项4',
        label: '龙须面'
      }, {
        value: '选项5',
        label: '北京烤鸭'
      }],
      productTypeOptions: [{
        value: '选项1',
        label: '黄金糕'
      }, {
        value: '选项2',
        label: '双皮奶'
      }, {
        value: '选项3',
        label: '蚵仔煎'
      }, {
        value: '选项4',
        label: '龙须面'
      }, {
        value: '选项5',
        label: '北京烤鸭'
      }],
      productTypeList: [],
      companyList: []
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
