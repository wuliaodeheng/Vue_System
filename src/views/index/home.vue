<template>
  <div class="container">
    <div class="subTitle" @clear="list()"><span></span>基本概况</div>
    <div class="box">
      <div class="card">
        <div class="item">
          <img src="@/views/index/components/用户.png" />
          <div class="right">
            <div>用户</div>
            <div>{{ statistics.person }}</div>
          </div>
        </div>
      </div>
      <div class="card">
        <div class="item">
          <img src="@/views/index/components/订单.png" />
          <div class="right">
            <div>订单数量</div>
            <div>{{ statistics.order }}</div>
          </div>
        </div>
      </div>
      <div class="card">
        <div class="item">
          <img src="@/views/index/components/管理员.png" />
          <div class="right">
            <div>管理员</div>
            <div>{{ statistics.admin }}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="subTitle"><span></span>统计图</div>
    <div class="echartsBox">
      <!-- 折线图 -->
      <div class="pieEcharts" ref="pieRef">
        <v-chart class="chart" :option="option1" />
      </div>
      <!-- 饼图 -->
      <div class="barEcharts" ref="barRef">
        <v-chart class="chart" :option="option2" />
      </div>
    </div>
  </div>
</template>
<script>
import * as echarts from 'echarts'
import { getCurrentInstance, defineComponent, reactive, ref, onMounted } from 'vue'
import { ElMessage } from 'element-plus'
import VChart from 'vue-echarts'

export default {
  components: {
    VChart
  },
  data() {
    return {
      statistics: {
        person: 7,
        order: 7,
        admin: 1
      },
      option1: {
        title: {
          text: '销量统计'
        },
        tooltip: {
          trigger: 'axis'
        },
        legend: {
          data: ['数码', '家居', '穿搭', '美食', '母婴']
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        toolbox: {
          feature: {
            saveAsImage: {}
          }
        },
        xAxis: {
          type: 'category',
          boundaryGap: false,
          data: ['星期一', '星期二', '星期三', '星期四', '星期五', '星期六', '星期天']
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            name: '数码',
            type: 'line',
            stack: 'Total',
            data: [120, 132, 101, 134, 90, 230, 210]
          },
          {
            name: '家居',
            type: 'line',
            stack: 'Total',
            data: [220, 182, 191, 234, 290, 330, 310]
          },
          {
            name: '穿搭',
            type: 'line',
            stack: 'Total',
            data: [150, 232, 201, 154, 190, 330, 410]
          },
          {
            name: '美食',
            type: 'line',
            stack: 'Total',
            data: [320, 332, 301, 334, 390, 330, 320]
          },
          {
            name: '母婴',
            type: 'line',
            stack: 'Total',
            data: [820, 932, 901, 934, 1290, 1330, 1320]
          }
        ]
      },
      option2: {
        title: {
          text: '订单统计',
          left: 'center'
        },
        tooltip: {
          trigger: 'item'
        },
        legend: {
          orient: 'vertical',
          left: 'left'
        },
        series: [
          {
            name: '订单数量',
            type: 'pie',
            radius: '50%',
            data: [
              { value: 1048, name: '数码' },
              { value: 735, name: '家居' },
              { value: 580, name: '穿搭' },
              { value: 484, name: '美食' },
              { value: 300, name: '母婴' }
            ],
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              }
            }
          }
        ]
      }
    }
  }
}
</script>
<style lang="scss" scoped>
.container {
  background-color: #fff;
  min-width: 660px;
  .subTitle {
    display: flex;
    align-items: center;
    height: 60px;
    font-weight: bold;
    font-size: 16px;
    padding: 0 20px;
    span {
      background-color: #409eff;
      width: 4px;
      height: 18px;
      border-radius: 4px;
      display: inline-block;
      margin: 0 10px 0 0;
    }
    .el-button {
      margin: 0 0 0 10px;
    }
  }
  .box {
    display: flex;
    align-items: center;
    justify-content: space-evenly;
    padding: 0 20px;
    .card {
      background: #ffffff;
      box-shadow: 0px 3px 17px 0px rgba(235, 240, 247, 0.9);
      border-radius: 14px;
      margin: 0 10px 10px 0;
      min-width: 160px;
      padding: 0 20px;
      box-sizing: border-box;
      height: 90px;
      width: 100%;
      .item {
        display: flex;
        align-items: center;
        height: 90px;
        img {
          height: 44px;
        }
        .right {
          margin: 0 0 0 10px;
          height: 42px;
          display: flex;
          flex-direction: column;
          justify-content: space-between;
          div:nth-child {
            font-size: 14px;
            color: #666;
          }
          div:last-child {
            font-weight: bold;
            font-size: 18px;
          }
        }
      }
    }
  }
  .echartsBox {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: space-between;
    overflow-x: hidden;
    div {
      text-align: center;
      width: 50%;
      height: 400px;
      min-width: 320px;
      padding: 10px;
      box-sizing: border-box;
    }
  }
}
</style>
