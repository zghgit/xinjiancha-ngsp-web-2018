<template>
  <div class='waveAnalysisDaily'>
    <el-card class='el-card-table' style='margin-top: 0px;'>
      <div slot='header' class='clearfix'>
        {{reportDate}}【市场概览】日报
      </div>
      <div class='textContent'>
        <pdf :src='requestPrefix'></pdf>
      </div>
      <div class='btn'>
        <word-upload @getTxtCon='getWordData' class='wordUpload'></word-upload>
        <el-button type='text' size='mini' @click='recoverReport'>恢复为系统报告</el-button>
      </div>
    </el-card>
  </div>

</template>

<script>
  import WordUpload from '../../common/WordUpload'
  import {
    postUploadReport,
    getReportShow,
    recoverReport
  } from '../../../service/marketSupervise/marketFluctuationAnalysis/index'
  import pdf from 'vue-pdf'

  export default {
    components: {
      WordUpload,
      pdf
    },
    name: 'overview-waveAnalysisDaily',
    data() {
      return {
        reportDate: '',
        requestPrefix: '/ngsp/fluct/report/v1/displayPDF?type=1'
      }
    },
    mounted() {
      this.getReportData()
    },
    methods: {
      getWordData(fileId, fileName) {
        let params = {fileId: fileId, fileName: fileName, type: '1'}
        postUploadReport(JSON.stringify(params)).then(resp => {
          this.$message.success('上传成功！')
          this.$emit('getWaveRefesh')
          this.getReportData()
        })
      },
      getReportData() {
        getReportShow('1').then(resp => {
          this.textContent = resp.text
          this.reportDate = resp.currentDate
        })
      },
      // 重新上传
      againUpload() {
        this.gfnGetUploadFileUrl()
      },
      // 恢复报告
      recoverReport() {
        let params = {type: '1'}
        recoverReport(JSON.stringify(params)).then(resp => {
          this.$emit('getWaveRefesh');
          this.$message.success('恢复成功！')
          this.getReportData()
        })
      }
    }
  }
</script>

<style lang='less'>
  .waveAnalysisDaily {
    .el-card__header {
      position: relative;
      .icon {
        cursor: pointer;
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
        right: 20px;
      }
    }
    .textContent {
      max-height: 350px;
      overflow: auto;
    }
    .btn {
      padding: 10px;
      float: right;
      .el-button {
        border: none;
        margin-left: 0px;
      }
      .wordUpload {
        display: inline-block;
      }
    }
  }
</style>
