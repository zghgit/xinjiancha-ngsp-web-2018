<template>
  <div class="tenMonth">
    <el-card class="el-card-table">
      <div slot='header'>
        整体市场近十月资金流动演化
      </div>
      <el-form :inline="true" :model="selectParams" label-width="100px" class="condition">
        <el-card style="overflow: visible">
          <el-form-item label="投资者类别">
            <down-more-select v-model="selectParams.intervalType" @input="getIntervalType"
                              :optionData="orderTypeOption" ref="cancelOrdrtypcod"></down-more-select>
          </el-form-item>
          <el-form-item label='日期设置'>
            <el-date-picker
              v-model='selectParams.beginDate'
              type='date'
              placeholder='请选择开始日期'
              value-format='yyyy-MM-dd'
              @change='getstarttime'
              :picker-options='startpickerOption'
              size='small'>
            </el-date-picker>
            <span style="padding: 0px 10px">-</span>
            <el-date-picker
              v-model='selectParams.endDate'
              type='date'
              placeholder='请选择结束日期'
              value-format='yyyy-MM-dd'
              @change='getendtime'
              :picker-options='endpickerOption'
              size='small'>
            </el-date-picker>
          </el-form-item>
          <el-form-item label="">
            <div style="margin-left: 30px">
              <el-button type="primary" size="small" @click="queryCharts">查询</el-button>
            </div>
          </el-form-item>
          <el-form-item label="" style="float: right">
            <el-button type="primary" size="small" @click="checkDetail">详情</el-button>
          </el-form-item>
        </el-card>
      </el-form>
      <div id="tenMonthFlow" style="height: 400px"></div>
    </el-card>
  </div>
</template>

<script>
  import DownMoreSelect from '../../common/downMoreSelect'
  import echarts from 'echarts'
  import {
    getInvestorTypeData
  } from '@/service/capitalFlow/index'

  export default {
    components: {
      DownMoreSelect
    },
    data() {
      return {
        selectParams: {
          intervalType: '',
          beginDate: '',
          endDate: ''
        },
        orderTypeOption: [], // 传递给子组件的投资者类型数据
        startpickerOption: {
          disabledDate: (time) => {
            return time.getTime() >= new Date(this.selectParams.endDate).getTime();
          }
        },
        endpickerOption: {
          disabledDate: (time) => {
            return time.getTime() <= new Date(this.selectParams.beginDate).getTime() - 3600 * 1000 * 24;
          }
        }
      }
    },
    mounted() {
      // 获取投资者类型
      getInvestorTypeData('investorType', 'all').then(resp => {
        resp.forEach((el) => {
          this.orderTypeOption.push({value: el.value, label: el.text})
        })
      })
      this.drawLineChart('tenMonthFlow')
    },
    methods: {
      getIntervalType(val) {
        console.log(val);
      },
      queryCharts() {

      },
      getstarttime(val) {
        console.log(val);
      },
      getendtime(val) {
        console.log(val);
      },
      drawLineChart(id) {
        let productline = echarts.init(document.getElementById(id), this.gfnGetTheme());
        let dataMap = {};

        function dataFormatter(obj) {
          let pList = ['北京', '天津', '河北', '山西', '内蒙古', '辽宁', '吉林', '黑龙江', '上海', '江苏', '浙江', '安徽', '福建', '江西', '山东', '河南', '湖北', '湖南', '广东', '广西', '海南', '重庆', '四川', '贵州', '云南', '西藏', '陕西', '甘肃', '青海', '宁夏', '新疆'];
          let temp;
          for (let year = 2002; year <= 2011; year++) {
            let max = 0;
            // let sum = 0;
            temp = obj[year];
            for (let i = 0, l = temp.length; i < l; i++) {
              max = Math.max(max, temp[i]);
              // sum += temp[i];
              obj[year][i] = {
                name: pList[i],
                value: temp[i]
              }
            }
          }
          console.log(obj);
          return obj;
        }

        dataMap.dataGDP = dataFormatter({
          2011: [16251.93, -11307.28, 24515.76, 11237.55, 14359.88, -22226.7, 10568.83, 12582, 19195.69, 49110.27, 32318.85, 15300.65, 17560.18, 11702.82, -45361.85, -26931.03, 19632.26, 19669.56, 53210.28, -11720.87, 2522.66, 10011.37, 21026.68, 5701.84, 8893.12, 605.83, 12512.3, -5020.37, -1670.44, -2102.21, 6610.05],
          2010: [14113.58, 9224.46, 20394.26, -9200.86, 11672, 18457.27, 8667.58, 10368.6, 17165.98, 41425.48, 27722.31, 12359.33, 14737.12, -9451.26, 39169.92, -23092.36, 15967.61, -16037.96, 46013.06, 9569.85, 2064.5, 7925.58, 17185.48, 4602.16, 7224.18, -507.46, -10123.48, 4120.75, 1350.43, -1689.65, 5437.47],
          2009: [12153.03, -7521.85, 17235.48, 7358.31, -9740.25, 15212.49, 7278.75, 8587, 15046.45, 34457.3, 22990.35, 10062.82, 12236.53, 7655.18, 33896.65, 19480.46, 12961.1, 13059.69, 39482.56, -7759.16, -1654.21, -6530.01, 14151.28, 3912.68, 6169.75, 441.36, -8169.8, 3387.56, 1081.27, 1353.31, 4277.05],
          2008: [11115, 6719.01, -16011.97, 7315.4, 8496.2, 13668.58, 6426.1, 8314.37, 14069.87, 30981.98, 21462.69, 8851.66, 10823.01, 6971.05, 30933.28, 18018.53, 11328.92, 11555, 36796.71, 7021, -1503.06, 5793.66, 12601.23, -3561.56, 5692.12, 394.85, 7314.58, -3166.82, -1018.62, -1203.92, -4183.21],
          2007: [9846.81, 5252.76, 13607.32, -6024.45, 6423.18, 11164.3, 5284.69, 7104, 12494.01, 26018.48, 18753.73, 7360.92, 9248.53, 5800.25, 25776.91, 15012.46, 9333.4, -9439.6, 31777.01, -5823.41, -1254.17, 4676.13, 10562.39, 2884.11, 4772.52, 341.43, 5757.29, 2703.98, 797.35, -919.11, -3523.16],
          2006: [8117.78, 4462.74, -11467.6, 4878.61, 4944.25, 9304.52, -4275.12, 6211.8, 10572.24, 21742.05, 15718.47, 6112.5, 7583.85, 4820.53, 21900.19, 12362.79, -7617.47, 7688.67, -26587.76, -4746.16, 1065.67, 3907.23, 8690.24, 2338.98, 3988.14, 290.76, 4743.61, 2277.35, 648.5, -725.9, -3045.26],
          2005: [6969.52, 3905.64, 10012.11, 4230.53, 3905.03, 8047.26, 3620.27, 5513.7, 9247.66, 18598.69, 13417.68, 5350.17, 6554.69, 4056.76, 18366.87, 10587.42, 6590.19, 6596.1, 22557.37, 3984.1, 918.75, 3467.72, 7385.1, 2005.42, 3462.73, 248.8, 3933.72, 1933.98, 543.32, 612.61, 2604.19],
          2004: [6033.21, 3110.97, 8477.63, 3571.37, 3041.07, 6672, 3122.01, 4750.6, 8072.83, 15003.6, 11648.7, 4759.3, 5763.35, 3456.7, 15021.84, 8553.79, 5633.24, 5641.94, 18864.62, 3433.5, 819.66, 3034.58, 6379.63, 1677.8, 3081.91, 220.34, 3175.58, 1688.49, 466.1, 537.11, 2209.09],
          2003: [5007.21, 2578.03, 6921.29, 2855.23, 2388.38, 6002.54, 2662.08, 4057.4, 6694.23, 12442.87, 9705.02, 3923.11, 4983.67, 2807.41, 12078.15, 6867.7, 4757.45, 4659.99, 15844.64, 2821.11, 713.96, 2555.72, 5333.09, 1426.34, 2556.02, 185.09, 2587.72, 1399.83, 390.2, 445.36, 1886.35],
          2002: [4315, 2150.76, 6018.28, 2324.8, 1940.94, 5458.22, 2348.54, 3637.2, 5741.03, 10606.85, 8003.67, 3519.72, 4467.55, 2450.48, 10275.5, 6035.48, 4212.82, 4151.54, 13502.42, 2523.73, 642.73, 2232.86, 4725.01, 1243.43, 2312.82, 162.04, 2253.39, 1232.03, 340.65, 377.16, 1612.6]
        });
        let option = {
          baseOption: {
            timeline: {
              axisType: 'category',
              autoPlay: true,
              playInterval: 300 * 1000,
              data: [
                '2002-01-01', '2003-01-01', '2004-01-01', '2005-01-01',
                '2006-01-01', '2007-01-01', '2008-01-01', '2009-01-01', '2010-01-01', '2011-01-01'
              ],
              label: {
                formatter: function (s) {
                  return (new Date(s)).getFullYear();
                }
              }
            },
            title: {},
            tooltip: {},
            legend: {show: false},
            calculable: true,
            grid: {
              top: 50,
              bottom: 100,
              tooltip: {
                trigger: 'axis',
                axisPointer: {
                  type: 'shadow',
                  label: {
                    show: true,
                    formatter: function (params) {
                      return params.value.replace('\n', '');
                    }
                  }
                }
              }
            },
            xAxis: [
              {
                type: 'category',
                axisLabel: {
                  interval: 0,
                  rotate: 45,
                  color: '#97a4d9'
                },
                axisLine: {
                  lineStyle: {
                    color: '#334164'
                  }
                },
                data: [
                  '北京', '天津', '河北', '山西', '内蒙古', '辽宁', '吉林', '黑龙江',
                  '上海', '江苏', '浙江', '安徽', '福建', '江西', '山东', '河南',
                  '湖北', '湖南', '广东', '广西', '海南', '重庆', '四川', '贵州',
                  '云南', '西藏', '陕西', '甘肃', '青海', '宁夏', '新疆'
                ],
                splitLine: {show: false}
              }
            ],
            yAxis: [
              {
                axisLabel: {
                  color: '#97a4d9'
                },
                axisLine: {
                  lineStyle: {
                    color: '#334164'
                  }
                },
                type: 'value',
                name: '单位（亿）',
                splitArea: {
                  show: true
                  // areaStyle: {
                  //   color: ['rgba(0,0,0,0.4)', 'rgba(0,0,0,0.2)']
                  // }
                },
                splitLine: {
                  show: true,
                  lineStyle: {
                    color: 'rgba(0,0,0,0.1)'
                  }
                },
                splitNumber: 3
              }
            ],
            series: [
              {name: '资金净流入', type: 'bar'}
            ]
          },
          options: [
            {
              series: [
                {
                  data: dataMap.dataGDP['2002'],
                  itemStyle: {
                    normal: {
                      color: function (params) {
                        if (params.value > 20000) {
                          return '#b01e0f'
                        } else if (params.value > 10000) {
                          return '#007c26'
                        } else {
                          return '#c28d28'
                        }
                      }
                    }
                  }
                }
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2003']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2004']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2005']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2006']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2007']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2008']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2009']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2010']}
              ]
            },
            {
              series: [
                {data: dataMap.dataGDP['2011']}
              ]
            }
          ]
        };
        productline.setOption(option);
        productline.on('click', (params) => {
          if (params.componentType == 'series') {
            this.$emit('getClickTime', params.name)
          }
        })
      },
      checkDetail() {
        window.open(`#/capitalFlowDetail`)
      }
    }

  }
</script>

<style lang="less">
  .tenMonth {
  }
</style>