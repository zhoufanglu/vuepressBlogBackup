<template>
  <div>
    <div class="p-charts" ref="chartRef"></div>
  </div>
</template>
<script>
export default {
  name: '',
  components: {},
  props: ['name'],
  data() {
    return {
      options: [],
    }
  },
  created() {
  },
  mounted() {
    console.log(20, this.name)
    const options = {
      title: {
        text: '收人/支出统计',
        textStyle: {
          color: 'white' // 设置标题文字颜色为白色
        }
      },
      tooltip: {
        trigger: 'axis'
      },
      legend: {
        data: ['收入', '支出', '理财'],
        textStyle: {
          color: 'white' // 设置图例文字颜色为白色
        }
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
        data: ['1', '2', '3', '4', '5', '6', '7','8', '9', '10', '11', '12'],
        axisLabel: {
          color: 'white' // 设置 x 轴文字颜色为白色
        },
      },
      yAxis: {
        type: 'value',
        axisLabel: {
          color: 'white'
        },
        splitLine: {
          show: true,       // 显示 x 轴的分割线
          lineStyle: {
            type: 'dashed', // 设置分割线为虚线
            color: `rgba(255, 255, 255, .2)`, // 设置分割线颜色为白色
            width: 1        // 设置分割线宽度
          }
        }
      },
      series: [
        {
          name: '收入',
          type: 'line',
          stack: 'Total',
          data: []
        },
        {
          name: '支出',
          type: 'line',
          stack: 'Total',
          data: [15080, 901, 8758, 12894, 9814, 4869, 3740],
          label: {
            show: true,      // 显示数据标签
            position: 'top', // 数据标签显示在折线顶部
            color: 'yellow'   // 数据标签文字颜色为白色
          },
          lineStyle: {
            color: 'white',  // 设置折线颜色为白色
            width: 2,        // 设置折线宽度为2
            type: 'solid'    // 设置折线为实线，可选的值还有 'dashed' 虚线和 'dotted' 点线
          },
        },
        {
          name: '理财',
          type: 'line',
          stack: 'Total',
          data: []
        }
      ]
    };

    echarts.init(this.$refs.chartRef).setOption(options)
  },
  methods: {}
}
</script>
<style>
.p-charts{
  height: 500px;
  width: 100%;
}
</style>
