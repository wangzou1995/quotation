<template>
  <el-container>
    <el-header>
      <el-dropdown style="display: block;text-align: right">
        <i class="el-icon-info" style="margin-right: 25px"> admin</i>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>编辑信息</el-dropdown-item>
          <el-dropdown-item>修改密码</el-dropdown-item>
          <el-dropdown-item>注销</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </el-header>
    <el-container>
      <el-aside id="aside" width="200px" v-bind:style="{height: myHeight}">
        <el-menu>
          <el-submenu index="1">
            <template slot="title"><i class="el-icon-menu"></i>报价单管理</template>
            <el-menu-item-group>
              <el-menu-item index="1-1" @click="addTab('销售报价单导出')">销售报价单导出</el-menu-item>
              <el-menu-item index="1-2" @click="addTab('销售报价单预览')">销售报价单预览</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
          <el-submenu index="2">
            <template slot="title"><i class="el-icon-menu"></i>价卡管理</template>
            <el-menu-item-group>
              <el-menu-item index="2-1" @click="addTab('WISH价卡导出')">WISH价卡导出</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-container>
        <el-tabs style="height: 100%" v-model="editableTabsValue" type="card" colsable @tab-remove="removeTab">
          <el-tab-pane
                       :key="item.name"
                       v-for="item in editableTabs"
                       :label="item.title"
                       :name="item.name"
                       :closable="item.close"
          >
            <component :is="item.content"></component>
          </el-tab-pane>
        </el-tabs>
        <el-footer height="30px" style="font-size: 14px">&copy; 燕文物流</el-footer>
      </el-container>
    </el-container>
  </el-container>
</template>
<script>
import Hello from './components/Hello'
import sheetExportComponent from './components/sheetExportComponent'
import WishSheetExportComponent from './components/WishSheetExportComponent'
let tabNames = []
const components = [{
  name: '销售报价单导出',
  componentName: sheetExportComponent
}, {
  name: '销售报价单预览',
  componentName: Hello
}, {
  name: 'WISH价卡导出',
  componentName: WishSheetExportComponent
}
]
export default {
  name: 'App',
  data () {
    return {
      editableTabsValue: '1',
      editableTabs: [{
        title: '主页',
        name: '1',
        content: Hello,
        close: false
      }],
      tabIndex: 1,
      myHeight: '200px'
    }
  },
  mounted () {
    // 注：window.onresize只能在项目内触发1次
    window.onresize = function windowResize () {
      // 通过捕获系统的onresize事件触发我们需要执行的事件
      this.myHeight = (window.innerHeight - 110) + 'px'
      document.querySelector('#aside').style.height = this.myHeight
    }
  },
  created () {
    this.myHeight = (window.innerHeight - 110) + 'px'
  },
  methods: {
    addTab (targetName) {
      // document.querySelector('#aside').style.height = defaultHeight
      if (tabNames.length === 0 || tabNames.indexOf(targetName) < 0) {
        let newTabName = ++this.tabIndex + ''
        this.editableTabs.push({
          title: targetName,
          name: newTabName,
          content: components.filter(component => component.name === targetName)[0].componentName,
          close: true
        })
        this.editableTabsValue = newTabName
        tabNames.push(targetName)
      } else {
        let tabs = this.editableTabs
        let position
        tabs.forEach((tab) => {
          if (tab.title === targetName) {
            position = tab.name
          }
        })
        this.editableTabsValue = position
      }
    },
    removeTab (targetName) {
      let tabs = this.editableTabs
      let tabLength = tabs.length - 1
      let activeTitle
      if (tabs[tabLength].name === targetName) {
        this.editableTabsValue = tabs[tabLength - 1].name
      }
      tabs.forEach((tab) => {
        if (tab.name === targetName) {
          activeTitle = tab.title
        }
      })
      this.editableTabs = tabs.filter(tab => tab.name !== targetName)
      tabNames = tabNames.filter(tabName => tabName !== activeTitle)
    }
  }
}
</script>
<style>
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 30px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: left;
    line-height: 200px;
    height: 100%;
  }

  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
  }

  body > .el-container {
    margin-bottom: 40px;
  }

  .el-container:nth-child(5) .el-aside,
  .el-container:nth-child(6) .el-aside {
    line-height: 260px;
  }

  .el-container:nth-child(7) .el-aside {
    line-height: 320px;
  }
</style>
