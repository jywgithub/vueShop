<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>数据统计</el-breadcrumb-item>
      <el-breadcrumb-item>数据报表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区域 -->
    <el-card>
      <div id="myEcharts" style="width: 60%;height:500px;"></div>
    </el-card>
  </div>
</template>
<script>
var echarts = require("echarts")
import _ from 'lodash'
export default {
  data() {
    return {
      options: {
        title: {
          text: "用户来源"
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#E9EEF3"
            }
          }
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        xAxis: [
          {
            boundaryGap: false
          }
        ],
        yAxis: [
          {
            type: "value"
          }
        ]
      },
      reportData: {}
    }
  },
  created() {
  },
  //   此时页面上的元素一渲染完成
  async mounted() {
    var myChart = echarts.init(document.getElementById("myEcharts"))
    const { data: res } = await this.$http.get("reports/type/1")
    console.log(res)
    if (res.meta.status !== 200)
      return this.$message.error("获取统计数据失败！")
    this.reportData = res.data
   const result =  _.merge(res.data,this.options)
    myChart.setOption(result)
  },
  methods: {}
}
</script>
<style lang="less" scoped>
</style>
