<template>
  <el-card class="chart-monitor el-card-table">
    <el-radio-group class="stockRadioBtn" v-model="chartTabsName" size="small">
      <el-radio-button label="ShhStockMonitorTime">分时</el-radio-button>
      <el-radio-button label="ShhStockMonitorK">日K</el-radio-button>
    </el-radio-group>
    <el-row class="chart-title">
      <el-col :span="5">
        <el-col :span="6" class="input-label">指数 :</el-col>
        <el-col :span="18">
          <el-select v-model="indexCode" placeholder="请选择" size="small" @change="changeCodes">
            <el-option
              v-for="item in indexCodeOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </el-col>
      </el-col>
      <el-col v-if="chartTabsName === 'ShhStockMonitorTime'" :span="8">
        <el-col :span="6" class="input-label">时间设置 :</el-col>
        <el-col :span="18">
          <el-time-picker
            is-range
            v-model="timeRange"
            :selectableRange="selectableRange"
            range-separator="至"
            start-placeholder="开始时间"
            end-placeholder="结束时间"
            size="small"
            @change="changeTime">
          </el-time-picker>
        </el-col>
      </el-col>
      <el-col v-if="chartTabsName === 'ShhStockMonitorTime'" :span="11">
        <el-col :span="6" class="input-label">日期设置 :</el-col>
        <el-col :span="18">
          <el-date-picker
            is-range
            v-model="tradeDate"
            placeholder="请选择日期"
            size="small"
            @change="changeParams">
          </el-date-picker>
        </el-col>
      </el-col>
      <el-col v-if="chartTabsName === 'ShhStockMonitorK'" :span="8">
        <el-col :span="6" class="input-label">日期设置 :</el-col>
        <el-col :span="18">
          <el-date-picker
            v-model="dateRange"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            size="small"
            :unlink-panels="true"
            @change="changeKChartBrush">
          </el-date-picker>
        </el-col>
      </el-col>
    </el-row>
    <div class="addLineBtn" v-if="chartTabsName === 'ShhStockMonitorTime'">
      叠加对象：
      <el-button type='primary' :class="isRedActive ? '': 'buttonCheck'" size='mini' @click="addLineActive('red')" :disabled="isNotTDay"><i class='fa5555'></i> 实时流动性监测</el-button>
      <el-button type='primary' :class="isOrangeActive ? '': 'buttonCheck'" size='mini' @click="addLineActive('orange')"><i class='eb9e05'></i> 大小盘乖离率</el-button>
      <el-tag class="tagBtn" :key="tag" v-for="(tag, index) in addList" closable @close="handleClose(tag)"><i :class="'tag' + index"></i>{{tag}}</el-tag>
      <el-button @click="addMore" type="primary" icon="el-icon-plus" size="mini" style="margin-left: 10px">更多</el-button>
      <el-button @click="clearMore" type="info" size="mini">清除叠加</el-button>
    </div>
    <add-item-box @refreshIsShow="refreshIsShowBox" @refreshAddItems="refreshAddItemList" :addTags="addList" :codeNameData="addCodeName" class="addItemBox" v-if="isMore"></add-item-box>
    <shh-stock-market-time-chart ref="monitorTime" @refreshAddItems="refreshAddItemList" v-show="chartTabsName === 'ShhStockMonitorTime'"></shh-stock-market-time-chart>
    <shh-stock-market-k-chart @tabsName="changeChartTabsName" ref="monitorDay" v-show="chartTabsName === 'ShhStockMonitorK'"></shh-stock-market-k-chart>
  </el-card>
</template>

<script>
  import moment from 'moment'
  import ShhStockMarketTimeChart from './chartMonitor/shhStockMarketTimeChart'
  import ShhStockMarketKChart from './chartMonitor/shhStockMarketKChart'
  import AddItemBox from '../../common/addItemBox'
  import * as types from '../../../store/mutation-types'
  import { getComposeChart } from '../../../service/marketSupervise/stockOperationMonitor'
  import { getCurTradeDate } from '../../../service/common/commonFunc'

  moment.locale('zh-cn')
  export default {
    components: {
      ShhStockMarketTimeChart,
      ShhStockMarketKChart,
      AddItemBox
    },
    data () {
      return {
        chartTabsName: 'ShhStockMonitorTime',
        isRedActive: false,
        isOrangeActive: false,
        isMore: false,
        isNotTDay: false,
        CMTimeOut: null,
        curTradeDate: '',
        addList: [],
        addCodeName: [],
        composeMeta: [],
        selectableRange: ['09:30:00 - 11:30:00', '13:00:00 - 15:00:00'],
        indexCodeOptions: [
          {
            value: '000001',
            label: '上证综指'
          }, {
            value: '000300',
            label: '沪深300'
          }, {
            value: '399006',
            label: '创业板指'
          }, {
            value: '399005',
            label: '中小板指'
          }, {
            value: '000016',
            label: '上证50'
          }, {
            value: '000010',
            label: '上证180'
          }, {
            value: '000009',
            label: '上证380'
          }, {
            value: '000905',
            label: '中证500'
          }]
      }
    },
    computed: {
      indexCode: {
        get () {
          return this.$store.state.marketSupervise.currentIndexCode
        },
        set (value) {
          this.$store.commit(types.M_CHANGE_INDEX_CODE, value)
        }
      },
      tradeDate: {
        get () {
          return this.$store.state.marketSupervise.tradeDate
        },
        set (value) {
          this.$store.commit(types.M_CHANGE_TRADE_DATE, value)
        }
      },
      timeRange: {
        get () {
          return this.$store.getters.getStockTimeRange
        },
        set (value) {
          this.$store.commit(types.M_CHANGE_TIME_RANGE, value)
        }
      },
      dateRange: {
        get () {
          return this.$store.getters.getStockDateRange
        },
        set (value) {
          this.$store.commit(types.M_CHANGE_DATE_RANGE, value)
        }
      }
    },
    watch: {
      chartTabsName: function (val) {
        this.$store.commit(types.M_CHANGE_DATE_RANGE, null)
        if (val === 'ShhStockMonitorK') {
          let startTime = new Date(2018, 1, 1, 9, 30)
          let endTime = new Date(2018, 1, 1, 15, 0)
          this.$store.commit(types.M_CHANGE_TIME_RANGE, [startTime, endTime])
        }
        this.getMonitorChartData()
      },
      tradeDate: function (val) {
        if (moment(this.curTradeDate).format('YYYY-MM-DD') !== moment(val).format('YYYY-MM-DD')) {
          this.isNotTDay = true
          if (this.isRedActive) {
            this.isRedActive = false
            this.$store.commit(types.M_LIQUIDITY_MONITOR, this.isRedActive)
            this.$refs.monitorTime.getLiquidityMonitorData()
          }
        } else {
          this.isNotTDay = false
        }
      },
      dateRange: function () {
        let range = this.$store.getters.getStockDateRange
        if (range == null || range[0] === '' || range[1] === '') {
          this.$refs.monitorDay.changeCandleChartBrush()
        }
      }
    },
    methods: {
      changeCodes () {
        this.$store.commit(types.M_CHANGE_DATE_RANGE, ['', ''])
        this.getMonitorChartData()
      },
      changeParams () {
        this.getMonitorChartData()
      },
      changeTime () {
        this.$refs.monitorTime.changeLineBrush()
      },
      changeKChartBrush () {
        this.$refs.monitorDay.changeCandleChartBrush()
      },
      changeChartTabsName () {
        this.chartTabsName = 'ShhStockMonitorTime'
      },
      addLineActive (btn) {
        if (btn === 'red') {
          this.isRedActive = !this.isRedActive
          this.$store.commit(types.M_LIQUIDITY_MONITOR, this.isRedActive)
          this.$refs.monitorTime.getLiquidityMonitorData()
        } else {
          this.isOrangeActive = !this.isOrangeActive
          this.$store.commit(types.M_DEVIATION_RATE, this.isOrangeActive)
          this.$refs.monitorTime.getDeviationRateData()
        }
      },
      addMore () {
        this.isMore = !this.isMore
      },
      clearMore () {
        this.$confirm('是否清除全部叠加？', '提示', {
          confirmButtonText: '是',
          cancelButtonText: '否'
        }).then(() => {
          this.addList = []
          this.addCodeName = []
          this.isRedActive = false
          this.$store.commit(types.M_LIQUIDITY_MONITOR, this.isRedActive)
          this.$refs.monitorTime.getLiquidityMonitorData()
          this.isOrangeActive = false
          this.$store.commit(types.M_DEVIATION_RATE, this.isOrangeActive)
          this.$refs.monitorTime.getDeviationRateData()
          this.refreshAddItemList()
        }).catch(() => {})
      },
      refreshIsShowBox (state) {
        this.isMore = state
      },
      refreshAddItemList (list, obj) {
        if (list !== undefined && obj !== undefined) {
          this.isMore = false
          this.addList = list
          this.addCodeName = obj
        }
        if (this.addCodeName.length < 1) {
          if (this.chartTabsName === 'ShhStockMonitorTime') {
            this.$store.commit(types.M_CHANGE_DATE_RANGE, null)
            this.$refs.monitorTime.marketLineCompose()
          } else {
            // this.$refs.monitorDay.marketDayCompose(resp);
          }
          return
        }
        let codes = ''
        let swVocs = ''
        let wdVocs = ''
        let cusVocs = ''
        let codeList = ''
        this.addCodeName.forEach(addCode => {
          codeList += addCode.code + ','
          if (addCode.type === 'index' || addCode.type === 'stock') {
            codes += addCode.code + ','
          } else if (addCode.type === 'block' || addCode.type === 'board') {
            if (addCode.code.indexOf('SW') > -1) {
              swVocs += addCode.code + ','
            } else if (addCode.code.indexOf('WD') > -1) {
              wdVocs += addCode.code + ','
            } else {
              cusVocs += addCode.code + ','
            }
          }
        })
        let params = {
          'type': this.chartTabsName === 'ShhStockMonitorTime' ? 'time' : 'day',
          'sort': codeList.substring(0, codeList.length - 1),
          'codes': codes.substring(0, codes.length - 1),
          'swVocs': swVocs.substring(0, swVocs.length - 1),
          'wdVocs': wdVocs.substring(0, wdVocs.length - 1),
          'cusVocs': cusVocs.substring(0, cusVocs.length - 1)
        }
        if (this.chartTabsName === 'ShhStockMonitorTime') {
          let tradeDate = this.$store.state.marketSupervise.tradeDate
          params.startTime = '0930'
          params.endTime = '1500'
          params.tradeDate = tradeDate === '' ? '' : moment(tradeDate).format('YYYYMMDD')
        } else {
          params.startDate = moment(this.$store.state.marketSupervise.startDate).format('YYYYMMDD')
          params.endDate = moment(this.$store.state.marketSupervise.endDate).format('YYYYMMDD')
        }
        getComposeChart(JSON.stringify(params)).then(resp => {
          if (this.chartTabsName === 'ShhStockMonitorTime') {
            this.$refs.monitorTime.marketLineCompose(resp)
          } else {
            // this.$refs.monitorDay.marketDayCompose(resp);
          }
        })
      },
      handleClose (tag) {
        let tagIndex = this.addList.indexOf(tag)
        this.addList.splice(tagIndex, 1)
        this.addCodeName.splice(tagIndex, 1)
        this.refreshAddItemList()
      },
      getMonitorChartData () {
        clearTimeout(this.CMTimeOut)
        if (this.chartTabsName === 'ShhStockMonitorTime') {
          this.$refs.monitorTime.getMarketLineData()
          if (this.addList.length > 0) {
            this.refreshAddItemList()
          }
          if (this.isRedActive) {
            this.$refs.monitorTime.getLiquidityMonitorData()
          }
          if (this.isOrangeActive) {
            this.$refs.monitorTime.getDeviationRateData()
          }
          this.CMTimeOut = setTimeout(this.getMonitorChartData, 60000)
        } else if (this.chartTabsName === 'ShhStockMonitorK') {
          this.$refs.monitorDay.getMarketKData()
        }
      }
    },
    beforeCreate () {
      getCurTradeDate().then(val => {
        this.curTradeDate = new Date(moment(val, 'YYYYMMDD').format('YYYY,MM,DD'))
        this.$store.commit(types.M_CHANGE_TRADE_DATE, this.curTradeDate)
      })
    },
    mounted () {
      this.getMonitorChartData()
    },
    beforeDestroy () {
      // 离开页面时销毁轮询时间
      if (this.CMTimeOut != null) {
        clearTimeout(this.CMTimeOut)
      }
    }
  }
</script>

<style scoped lang="less">
  .chart-monitor {
    position: relative;
    overflow: initial;
    padding-top: 10px;
    .el-card__body {
      position: relative;
      padding: 0 !important;
    }
    .el-tabs {
      .el-tabs__header {
        margin: 0 0 15px;
        .el-tabs__nav-scroll {
          padding-left: 20px;
        }
      }
    }
    .chart-title {
      padding-bottom: 10px;
      margin-top: 10px;
      margin-left: 150px;
    }
    .addLineBtn {
      position: absolute;
      top: 70px;
      left: 20px;
      z-index: 2;
      .buttonCheck {
        background-color: #263e69;
        color: #aec0e6;
      }
      .tagBtn {
        height: 26px;
        line-height: 26px;
        border: none;
        padding: 0 16px;
        margin-left: 10px;
        margin-right: 2px;
        background-color: rgba(51, 113, 245, 0.5);
        color: #ffffff;
        i {
          left: -4px;
        }
      }
      i {
        width: 10px;
        height: 10px;
        display: inline-block;
        position: relative;
        top: 1px;
        left: -2px;
      }
      .fa5555 {
        background-color: #fa5555;
      }

      .eb9e05 {
        background-color: #eb9e05;
      }

      .tag0 {
        background-color: #339933;
      }

      .tag1 {
        background-color: #1b5eed;
      }

      .tag2 {
        background-color: #288783;
      }

      .tag3 {
        background-color: #1de3c0;
      }

      .tag4 {
        background-color: #6766cc;
      }

      .tag5 {
        background-color: #ea6e0d;
      }

      .tag6 {
        background-color: #ca6796;
      }
    }
    .addItemBox {
      position: absolute;
      top: 110px;
      left: 0;
      z-index: 4;
    }
  }

  .el-radio-button:first-child .el-radio-button__inner {
    border-left: none;
  }

  .stockRadioBtn {
    position: absolute;
    left: 15px;
    z-index: 2;
    top: 5px;
  }
</style>