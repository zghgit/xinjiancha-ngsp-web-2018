<template>
  <div class='overMonitorSecurity'>
    <breadcrumb :breadcrumbList='breadcrumbItems'></breadcrumb>
    <el-card class='el-card-table'>
      <el-steps :active='activeName' style='padding:15px' finish-status="success">
        <el-step :title='processStatusName[0]' :description='processinfo[0]'></el-step>
        <el-step :title='processStatusName[1]' :description='processinfo[1]'></el-step>
        <el-step :title='processStatusName[2]' :description='processinfo[2]'></el-step>
        <el-step :title='processStatusName[3]' :description='processinfo[3]'></el-step>
      </el-steps>
    </el-card>
    <div>
      <el-card class='el-card-table'>
        <div slot='header'>
          监控证券信息
        </div>
        <el-form :inline='true' :model='selectParams' ref='selectParams'>
          <el-row :gutter='20'>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='监控编号：'>
                <el-input size='small' v-model='selectParams.monitorNumber' :disabled='true'></el-input>
              </el-form-item>
            </el-col>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='监控原因：'>
                <el-select v-model='selectParams.monitorReason' placeholder='请选择' size='small' :disabled='true'>
                  <el-option
                    v-for='item in monitorReasonOptions'
                    :key='item.value'
                    :label='item.label'
                    :value='item.value'
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='证券代码及简称：'>
                <stock-code-query :commonData='submitData' :clearable='false'
                                  @getStockCode='getStockCode'></stock-code-query>
              </el-form-item>
            </el-col>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='创建者：'>
                <el-input size='small' v-model='selectParams.creator' :disabled='true'></el-input>
              </el-form-item>
            </el-col>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='线索单号：'>
                <el-button type='text' size='middle' style='border: none' @click='jumpClueReport'>
                  {{selectParams.clewReport}}
                </el-button>
              </el-form-item>
            </el-col>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='监控级别：'>
                <el-select v-model='selectParams.monitorLevel' placeholder='请选择' size='small' :disabled='true'>
                  <el-option
                    v-for='item in monitorLevelOptions'
                    :key='item.value'
                    :label='item.label'
                    :value='item.value'
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='监控起始日期：'>
                <el-date-picker
                  v-model='selectParams.startDate'
                  type='date'
                  size='small'
                  :disabled='true'
                  value-format='yyyy-MM-dd'
                  placeholder='开始日期'>
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :xl='6' :lg='8' :md='12' :sm='24'>
              <el-form-item label='监控结束日期：'>
                <el-date-picker
                  v-model='selectParams.endDate'
                  type='date'
                  size='small'
                  :disabled='true'
                  value-format='yyyy-MM-dd'
                  placeholder='结束日期'>
                </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :xl='24'>
              <el-form-item label='重点监控描述：' class='describe'>
                <el-input v-model='selectParams.monitorDescribe' type='textarea' :disabled='true'></el-input>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>
      </el-card>
    </div>
    <el-card class='el-card-table'>
      <div slot='header'>
        涉及监控账户组
      </div>
      <el-table
        :data='monitorGroupsData'
        border
        tooltip-effect='dark'
        size='small'
        highlight-current-row
        max-height='400'
        :reserve-selection='true'
        style='width: 100%'>
        <el-table-column
          header-align='center'
          align='center'
          type='index'
          label='序号'
          width='50'>
        </el-table-column>
        <el-table-column
          v-for='column in monitorGroupsColumns'
          :key='column.field'
          :align='column.align'
          :prop='column.field'
          :label='column.label'
          :min-width='column.minWidth'>
        </el-table-column>
      </el-table>
    </el-card>
    <el-card class='el-card-table'>
      <div slot='header'>
        历史变更记录
      </div>
      <el-table
        :data='historyChangeData'
        border
        tooltip-effect='dark'
        size='small'
        highlight-current-row
        max-height='400'
        :reserve-selection='true'
        style='width: 100%'>
        <el-table-column
          header-align='center'
          align='center'
          type='index'
          label='序号'
          width='50'>
        </el-table-column>
        <el-table-column
          v-for='column in historyChangeColumns'
          :key='column.field'
          :align='column.align'
          :prop='column.field'
          :label='column.label'
          :min-width='column.minWidth'>
        </el-table-column>
      </el-table>
    </el-card>
    <el-card class='el-card-table'>
      <div slot='header'>
        审核意见
      </div>
      <el-table
        :data='checkSuggestionData'
        border
        tooltip-effect='dark'
        size='small'
        highlight-current-row
        max-height='400'
        :reserve-selection='true'
        style='width: 100%'>
        <el-table-column
          header-align='center'
          align='center'
          type='index'
          label='序号'
          width='50'>
        </el-table-column>
        <el-table-column
          v-for='column in checkSuggestionColumns'
          :key='column.field'
          :align='column.align'
          :prop='column.field'
          :label='column.label'
          :min-width='column.minWidth'>
        </el-table-column>
      </el-table>
    </el-card>
    <div style='text-align: right;padding: 15px'>
      <el-button type='info' size='middle' @click='goBack'>返回</el-button>
    </div>
  </div>

</template>

<script>
  import Breadcrumb from '../../common/Breadcrumb'
  import StockCodeQuery from '../../common/StockCodeQuery';
  import MinxinVue from '../MixIn'
  import {
    getFormInfo,
    getHistorySuggest,
    getStockGroupInfo
  } from '../../../service/superviseTask/focusMonitor/index'
  import moment from 'moment'

  export default {
    name: 'focus-monitor-security',
    components: {
      Breadcrumb,
      StockCodeQuery
    },
    mixins: [MinxinVue],
    data() {
      return {
        processStatusName: ['创建中', '审核中', '确认中', '办结'],
        breadcrumbItems: [
          {
            router: '',
            label: '监察任务'
          },
          {
            router: '/focusMonitorSecurity',
            label: '重点监控证券'
          },
          {
            router: '',
            label: '重点监控证券表单'
          }
        ],
        submitData: {disabled: true, stockCode: ''},
        activeName: 4,
        processinfo: ['', '', '', ''],
        selectParams: {
          monitorNumber: '',
          monitorReason: '',
          monitorLevel: '',
          startDate: '',
          endDate: '',
          clewReport: '',
          creator: '',
          monitorDescribe: '',
          stockCode: '',
          stockName: ''
        },
        monitorReasonOptions: [
          {value: '1', label: '涉嫌跨期操纵'},
          {value: '2', label: '涉嫌短线操纵'},
          {value: '3', label: '异动股'},
          {value: '4', label: '其他'}
        ],
        monitorLevelOptions: [
          {value: '0', label: '0级'},
          {value: '1', label: '1级'},
          {value: '2', label: '2级'},
          {value: '3', label: '3级'}
        ],
        monitorGroupsData: [],  // 账户组表格
        monitorGroupsColumns: [
          {label: '监控账户组编号', field: 'monitorNo', align: 'center', minWidth: '6%'},
          {label: '监控原因', field: 'reason', align: 'center', minWidth: '6%'},
          {label: '监控级别', field: 'level', align: 'center', minWidth: '6%'},
          {label: '创建者', field: 'createPerson', align: 'center', minWidth: '6%'},
          {label: '涉及股票', field: 'relatedSecIds', align: 'center', minWidth: '6%'},
          {label: '监控开始时间', field: 'startDate', align: 'center', minWidth: '6%'},
          {label: '监控结束时间', field: 'endDate', align: 'center', minWidth: '6%'}
        ],
        historyChangeData: [],  // 历史变更表格
        historyChangeColumns: [
          {label: '变更日期', field: 'altTime', align: 'center', minWidth: '5%'},
          {label: '变更内容', field: 'altDetail', align: 'left', minWidth: '15%'},
          {label: '处理人', field: 'altPerson', align: 'center', minWidth: '4%'},
          {label: '审核人', field: 'exmPerson', align: 'center', minWidth: '4%'}
        ],
        checkSuggestionData: [],
        checkSuggestionColumns: [
          {label: '流程状态', field: 'proceStatus', align: 'center', minWidth: '6%'},
          {label: '审核意见', field: 'comment', align: 'center', minWidth: '6%'},
          {label: '处理人', field: 'procePerson', align: 'center', minWidth: '6%'},
          {label: '审核日期', field: 'createTime', align: 'center', minWidth: '6%'}
        ],
        CurrentPage: 1,
        PageTotal: 0,
        clueInfoParams: '' // 请求线索报告信息参数
      }
    },
    created() {
      this.getHistorySuggestion()
    },
    mounted() {
      this.getFormInfo()
    },
    methods: {
      // 获取历史审核意见
      getHistorySuggestion() {
        getHistorySuggest(this.$route.query.instanceId).then(resp => {
          let res = resp.value
          res.forEach((el, index) => {
            el.createTime = moment(el.createTime).format('YYYY/MM/DD HH:mm:ss')
            this.processinfo[el.proceStatus] = el.procePerson + ' ' + el.createTime
            el.proceStatus = this.processStatusName[el.proceStatus]
          })
          this.checkSuggestionData = res
        })
      },
      // 获取表单信息
      getFormInfo() {
        getFormInfo(this.$route.query.instanceId).then(resp => {
          let res = resp.MscMain
          this.MscMainParams = resp.MscMain
          this.selectParams.monitorNumber = res.monitorNo.toString()
          this.selectParams.monitorReason = res.reason.toString()
          this.oldReason = res.reason.toString()
          this.selectParams.monitorLevel = res.level + '级'
          this.oldLevel = res.level
          this.selectParams.startDate = res.startTime
          this.selectParams.endDate = res.endTime
          this.oldDate = [res.startTime, res.endTime]
          this.selectParams.creator = res.createPerson
          this.selectParams.monitorDescribe = res.description
          this.selectParams.stockCode = res.securityId
          this.selectParams.stockName = res.securityName
          this.submitData.stockCode = res.securityId + ' ' + res.securityName
          this.clueInfoParams = res.clueId
          this.selectParams.clewReport = res.extend2
          this.historyChangeData = resp.MscAlthistory
          getStockGroupInfo(res.securityId).then(resp => {
            resp.RelatedMagRec.forEach((el) => {
              if (el.level == '1') {
                el.level = '1级'
              } else if (el.level == '2') {
                el.level = '2级'
              } else if (el.level == '3') {
                el.level = '3级'
              } else {
                el.level = '0级'
              }
              if (el.reason == '1') {
                el.reason = '涉嫌跨期操纵'
              } else if (el.reason == '2') {
                el.reason = '涉嫌短线操纵'
              } else if (el.reason == '3') {
                el.reason = '异动股'
              } else {
                el.reason = '其他'
              }
            })
            this.monitorGroupsData = resp.RelatedMagRec
          })
        })
      },
      getStockCode(stockCodeData) {
        console.log(stockCodeData);
      },
      // 跳线索上报流程
      jumpClueReport() {
        window.open(`#/clueReportForm?operate=1&instanceId=${this.clueInfoParams}&isNewTab=true`)
        // this.$router.push({name: 'clueReportForm', query: {operate: '1', taskId: '', instanceId: this.clueInfoParams}});
      }
    }
  }
</script>

<style lang='less'>
  .overMonitorSecurity {
    .el-step__title.is-process {
      color: #fff
    }
    .el-form {
      padding: 0px 20px;
      .el-form-item {
        height: 63px;
        margin-bottom: 0px;
        padding: 10px 0px;
      }
    }
    .describe {
      width: 100%;
      .el-form-item__content {
        width: 88%;
      }
    }
  }
</style>
