<!--
  type:                控件类型                  默认date                 String
        date          日期
        time          时间
        datetime      日期时间
  is-range            是否范围选择               默认false                Boolean
  size：               控件的大小                默认small                String
  disabled             禁用                      默认false                Boolean
  prefix-icon：        自定义头部图标的类名      默认el-icon-date         String
  clear-icon：         自定义清空图标的类名      默认el-icon-circle-close String
  clearable：          是否显示清除按钮          默认true                 Boolean
  placeholder：        占位符                    默认-                    String
  rangeSeparator：     占位符                    默认至                   String
  startPlaceholder：   开始时间占位符            默认-                    String
  endPlaceholder：     结束时间占位符            默认-                    String
  selectableRange：    可选时间段                默认00:00:00 - 23:59:59  String
  shortcuts：          设置快捷选项，同element-ui shortcuts      默认-    Array
  arrowControl：       是否使用箭头进行时间选择  默认false                Boolean
  propsUpDisabledTime：  自定义disabled时间范围，比如小于今天不可选
  propsDownDisabledTime：  自定义disabled时间范围，比如大于今天不可选
-->
<template>
  <div class="s-date-picker">
    <div v-if="isRange">
      <div v-if="type=='date'"
           class="el-date-editor el-range-editor el-input__inner el-date-editor--timerange el-range-editor--small"
           :class="'el-range-editor--'+size">
        <i class="el-input__icon el-range__icon" :class="newPrefixIcon"></i>
        <el-date-picker
          v-model="value1"
          type="date"
          prefix-icon="aaa"
          :disabled="disabled"
          :clearable="false"
          :placeholder="startPlaceholder"
          format="yyyy-MM-dd"
          value-format="yyyy-MM-dd"
          :picker-options="{
          disabledDate:rangeDate1,
          shortcuts:shortcuts
        }"
          :size="size"
          @blur="handlerBlur1"
          @change="handlerChange"></el-date-picker>
        <span class="el-range-separator">{{rangeSeparator}}</span>
        <el-date-picker
          v-model="value2"
          type="date"
          prefix-icon="aaa"
          :disabled="disabled"
          :clearable="false"
          :placeholder="endPlaceholder"
          format="yyyy-MM-dd"
          value-format="yyyy-MM-dd"
          :picker-options="{
            disabledDate:rangeDate2,
            shortcuts:shortcuts
          }"
          :size="size"
          @blur="handlerBlur"
          @change="handlerChange"></el-date-picker>
        <i class="el-input__icon el-range__close-icon" :class="clearIcon" v-if="clearable" @click="clearDate"></i>
      </div>
      <div v-if="type=='time'"
           class="el-date-editor el-range-editor el-input__inner el-date-editor--timerange el-range-editor--small"
           :class="'el-range-editor--'+size">
        <i class="el-input__icon el-range__icon el-icon-time"></i>
        <el-time-picker
          v-model="value1"
          prefix-icon="aaa"
          :disabled="disabled"
          :clearable="false"
          :placeholder="startPlaceholder"
          format="HH:mm:ss"
          value-format="HH:mm:ss"
          :arrow-control="arrowControl"
          :picker-options="{
          selectableRange:selectableRange1
          }"
          :size="size"
          @change="handlerChange"></el-time-picker>
        <span class="el-range-separator">{{rangeSeparator}}</span>
        <el-time-picker
          v-model="value2"
          prefix-icon="aaa"
          :disabled="disabled"
          :clearable="false"
          :placeholder="endPlaceholder"
          format="HH:mm:ss"
          value-format="HH:mm:ss"
          :arrow-control="arrowControl"
          :picker-options="{
          selectableRange:selectableRange2
          }"
          :size="size"
          @change="handlerChange"></el-time-picker>
        <i class="el-input__icon el-range__close-icon el-icon-circle-close" v-if="clearable" @click="clearDate"></i>
      </div>
      <div v-if="type=='datetime'"></div>
    </div>
    <div v-else>
      <div v-if="type=='date'">
        <el-date-picker
          v-model="value1"
          type="date"
          :disabled="disabled"
          :prefix-icon="newPrefixIcon"
          :clear-icon="clearIcon"
          :clearable="clearable"
          :placeholder="newPlaceholder"
          format="yyyy-MM-dd"
          value-format="yyyy-MM-dd"
          :size="size"
          @change="handlerChange"/>
      </div>
      <div v-if="type=='time'">
        <el-time-picker
          v-model="value1"
          :disabled="disabled"
          :prefix-icon="newPrefixIcon"
          :clear-icon="clearIcon"
          :clearable="clearable"
          :placeholder="newPlaceholder"
          format="HH:mm:ss"
          value-format="HH:mm:ss"
          :arrow-control="arrowControl"
          :picker-options="{
          selectableRange:selectableRange
        }"
          :size="size"
          @change="handlerChange"/>
      </div>
      <div v-if="type=='datetime'">
        <el-date-picker
          v-model="value1"
          type="datetime"
          :disabled="disabled"
          :prefix-icon="newPrefixIcon"
          :clear-icon="clearIcon"
          :clearable="clearable"
          :placeholder="newPlaceholder"
          format="yyyy-MM-dd HH:mm:ss"
          value-format="yyyy-MM-dd HH:mm:ss"
          :picker-options="{
          disabledDate:rangeDate1,
          shortcuts:shortcuts
        }"
          :size="size"
          @change="handlerChange"/>
      </div>
    </div>
  </div>
</template>

<script>
  import moment from 'moment'

  export default {
    model: {
      prop: 'value',
      event: 'change'
    },
    name: 's-date-picker',
    props: {
      disabled: {
        type: Boolean,
        default: false
      },
      value: [String, Array, Date],
      type: {
        type: String,
        default () {
          return 'date'
        }
      },
      isRange: {
        type: Boolean,
        default () {
          return false
        }
      },
      size: {
        type: String,
        default () {
          return 'small'
        }
      },
      prefixIcon: [String],
      clearIcon: {
        type: String,
        default () {
          return 'el-icon-circle-close'
        }
      },
      clearable: {
        type: Boolean,
        default () {
          return true
        }
      },
      placeholder: [String],
      rangeSeparator: {
        type: String,
        default () {
          return '至'
        }
      },
      startPlaceholder: {
        type: String,
        default: '开始时间'
      },
      endPlaceholder: {
        type: String,
        default: '结束时间'
      },
      selectableRange: {
        type: String,
        default () {
          return '00:00:00 - 23:59:59'
        }
      },
      arrowControl: {
        type: Boolean,
        default () {
          return false
        }
      },
      shortcuts: [Array],
      propsUpDisabledTime: {
        type: [Number, String],
        default: 0
      },
      propsDownDisabledTime: {
        type: [Number, String],
        default: 0
      }
    },
    components: {},
    mixins: [],
    data () {
      return {
        value1: null,
        value2: null,
        selectableRange1: null,
        selectableRange2: null
      }
    },
    computed: {
      newPlaceholder () {
        let placeholder = {
          'time': '请输入时间',
          'date': '请输入日期',
          'datetime': '请输入日期时间'
        }
        return this.placeholder ? this.placeholder : placeholder[this.type]
      },
      newPrefixIcon () {
        let prefixIcon = 'el-icon-time'
        if (this.type != 'time') {
          prefixIcon = 'el-icon-date'
        }
        return this.prefixIcon ? this.prefixIcon : prefixIcon
      }
    },
    watch: {
      value: {
        handler () {
          if (Array.isArray(this.value) && this.isRange) {
            this.value1 = this.value[0] ? this.value[0] : null
            this.value2 = this.value[1] ? this.value[1] : null
          } else {
            this.value1 = this.value
          }
          if (!Array.isArray(this.value) && this.isRange && this.type == 'time') {
            this.value1 = '09:30:00'
            this.value2 = '15:00:00'
          }
        },
        deep: true,
        immediate: true
      },
      selectableRange: {
        handler () {
          this.selectableRange1 = this.selectableRange
          this.selectableRange2 = this.selectableRange
        },
        immediate: true
      },
      value1: {
        handler () {
          if (this.type == 'time' && this.isRange) {
            let arr = this.selectableRange2.split(' - ')
            arr[0] = this.value1
            this.selectableRange2 = arr.join(' - ')
          }
        },
        immediate: true
      },
      value2: {
        handler () {
          if (this.type == 'time' && this.isRange) {
            let arr = this.selectableRange1.split(' - ')
            arr[1] = this.value2
            this.selectableRange1 = arr.join(' - ')
          }
        },
        immediate: true
      }
    },
    created () {
    },
    mounted () {
    },
    beforeDestroy () {
    },
    methods: {
      clearDate () {
        this.value1 = null
        this.value2 = null
        if (this.type == 'time' && this.isRange) {
          this.selectableRange1 = '00:00:00 - 23:59:59'
          this.selectableRange2 = '00:00:00 - 23:59:59'
        }
        this.$emit('change', null)
      },
      rangeDate1 (time) {
        let defaultDisabledFlag = time.getTime() > moment(this.value2)._d
        let propsUpDisabledTime = time.getTime() > this.propsUpDisabledTime
        let propsDownDisabledTime = time.getTime() < this.propsDownDisabledTime
        return this.propsUpDisabledTime ? (defaultDisabledFlag || propsUpDisabledTime) : this.propsDownDisabledTime ? (defaultDisabledFlag || propsDownDisabledTime) : defaultDisabledFlag
      },
      rangeDate2 (time) {
        let defaultDisabledFlag = time.getTime() < moment(this.value1)._d
        let propsUpDisabledTime = time.getTime() > this.propsUpDisabledTime
        let propsDownDisabledTime = time.getTime() < this.propsDownDisabledTime
        return this.propsUpDisabledTime ? (defaultDisabledFlag || propsUpDisabledTime) : this.propsDownDisabledTime ? (defaultDisabledFlag || propsDownDisabledTime) : defaultDisabledFlag
      },
      handlerChange () {
        if (this.isRange) {
          this.$emit('change', [this.value1, this.value2])
        } else {
          this.$emit('change', this.value1)
        }
      },
      handlerBlur (val) {
        if (val.userInput && !moment(val.userInput).isAfter(this.value1)) {
          this.value2 = this.value1
          val.userInput = null
        }
      },
      handlerBlur1 (val) {
        if (val.userInput && !moment(val.userInput).isBefore(this.value2)) {
          this.value1 = this.value2
          val.userInput = null
        }
      }
    }
  }
</script>

<style lang="less">
  .s-date-picker {
    .el-date-editor {
      .el-range__icon {
        margin-left: 0px;
      }
      .el-range__close-icon {
        margin-right: 5px;
        opacity: 0;
        &:hover {
          opacity: 1;
        }
      }
      .el-date-editor {
        .el-input__inner {
          text-align: center;
          border-width: 0px !important;
          padding: 0px;
        }
      }
    }
  }
</style>
