<template>
  <div>
    <breadcrumb :breadcrumbList="breadcrumbItems"></breadcrumb>
    <el-card style="height:57px">
      <el-tabs v-model="activename"  @tab-click="handleClick">
        <el-tab-pane label="账户维度" name="one">
        </el-tab-pane>
        <el-tab-pane label="营业部维度" name="two">
        </el-tab-pane>
      </el-tabs>
    </el-card>
    <accountDimensionlist v-show="tabdispaly=='one'" @changetab='changetab'></accountDimensionlist>
    <departmentDimensionlist v-show="tabdispaly=='two'"></departmentDimensionlist>
  </div>
</template>

<script>
  import moment from 'moment'
  //面包屑
  import breadcrumb from '../../../components/common/Breadcrumb'
  import accountDimensionlist from './accountDimensionlist'
  import departmentDimensionlist from './departmentDimensionlist'

  export default {
    components: {
      breadcrumb,
      accountDimensionlist,
      departmentDimensionlist,
    },
    data() {
      return {
        breadcrumbItems: [
          {
            router: '/insiderTradAnalysis',
            label: '分析工具'
          }, {
            router: '/insiderTradAnalysis',
            label: '特定工具'
          }, {
            router: '/insiderTradAnalysis',
            label: '账户分析'
          }
        ],
        activename:'one',
        tabdispaly:'one',
        urlstrs:[],
      }
    },
    mounted() {
    },
    methods: {
      changetab(data){
        this.tabdispaly=data
        this.activename='two'
      },
      handleClick(tab,event){
        this.$store.commit('changedepartmentcode', "");
        if(tab.name=="one"){
          this.tabdispaly='one'
          this.$store.commit('savebranchCode', "");
        }else{
          this.tabdispaly='two'
        }
      },
    }
  }
</script>

<style lang="less">
</style>
