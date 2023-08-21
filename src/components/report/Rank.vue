<template>
  <div class="com-container">
    <div class="com-chart" ref="stockRef">
    </div>
  </div>
</template>   

<script>
import { mapState } from 'vuex'
import axios from 'axios'


export default {
  // 库存和销量分析
  name: 'Stock',
  data() {
    return {
      selectedValue: 2020, // 设置默认选中的值
      // 图表的实例对象
      chartInstance: null,
      // 从服务器中获取的所有数据
      allData: null,
      // 当前显示数据的页数
      currentIndex: 1,
      // 定时器标识
      timerId: null,
      
    }
  },

  created() {
    // this.$socket.registerCallBack('stockData', this.getData)
  },

  computed: {
    ...mapState(['theme']),
  },

  watch: {
    theme() {
      // 销毁当前的图表
      this.chartInstance.dispose()
      // 以最新主题初始化图表对象
      this.initChart()
      // 屏幕适配
      this.screenAdapter()
      // 渲染数据
      this.updateChart()
    },
  },

  //用于页面初始化数据
  mounted() {
    this.initChart()
    this.getData()

    window.addEventListener('resize', this.screenAdapter)
    // 主动触发 响应式配置
    this.screenAdapter()
  },

  destroyed() {
    window.removeEventListener('resize', this.screenAdapter)
    clearInterval(this.timerId)
    // this.$socket.unRegisterCallBack('stockData')
  },

  methods: {
    // 初始化图表的方法
    initChart() {
      this.chartInstance = this.$echarts.init(this.$refs.stockRef, this.theme)
     
      const initOption = {
        backgroundColor: "rgb(235,235,215,0.8)", // Creamy beige background
        animationDurationUpdate: 1500,
        animationEasingUpdate: 'quinticInOut',
        title: {
          text: '▎苏州丝绸纹样雷达图', // Adjusted title
          left: 20,
          top: 20,
          textStyle: {
            color: '#6B4226', // Dark brown text color
            fontFamily: 'serif',
            fontSize: 18, // Slightly larger font size
          },
        },

        radar: {
          shape: 'circle',
          indicator: [
            { name: '水墨青', max: 1, color: '#405860', axisLine:{lineStyle:{color:'#405860',width: 2.5}}},
            { name: '浅碧绿', max: 1, color: '#90EE90', axisLine:{lineStyle:{color:'#90EE90',width: 2.5}}},
            { name: '紫罗兰', max: 1, color: '#8A2BE2', axisLine:{lineStyle:{color:'#8A2BE2',width: 2.5}}},
            { name: '古朴紫', max: 1, color: '#800080', axisLine:{lineStyle:{color:'#800080',width: 2.5}}},
            { name: '秋叶橙', max: 1, color: '#FFA500', axisLine:{lineStyle:{color:'#FFA500',width: 2.5}}},
            { name: '宫廷红', max: 1, color: '#800000', axisLine:{lineStyle:{color:'#800000',width: 2.5}}},
            { name: '砚墨灰', max: 1, color: '#808080', axisLine:{lineStyle:{color:'#808080',width: 2.5}}},
            { name: '清雅蓝', max: 1, color: '#87CEEB', axisLine:{lineStyle:{color:'#87CEEB',width: 2.5}}},
          ],

          // splitArea: {
          //   show: false,
          //   areaStyle: {
          //     color: "black",
          //   },
          // },
          splitLine: {
            show: true,
            lineStyle: {
              width: 1,
              color: ['#aaa', '#ddd'],// Dark brown grid lines
            },
          },
        },

        tooltip: {
          show: true,
          trigger: 'item',
          position: ['75%', '18%'],
          // textStyle: {
          //   fontSize:6
          // },
          formatter: (params) => {
            const colorBlock = (color) => `<span style="display:inline-block;margin-right:5px;border-radius:10px;width:10px;height:10px;background-color:${color};"></span>`;
            const data = params.data.value.map((val, index) => `${colorBlock(initOption.radar.indicator[index].color)}${initOption.radar.indicator[index].name}: ${val}&nbsp;&nbsp;&nbsp`).join('<br/><br/>');
            return `城市名：&nbsp;&nbsp;&nbsp;${params.data.name}<br/>${data}`;
          }
        },

        series: [
          {
            type: 'radar',
            areaStyle: {
              color: "rgba(107, 66, 38, 0.3)", // Light brown area color
            },
            lineStyle: {
              color: "#6B4226", // Dark brown line color
              width: 3, // Slightly thinner line
            },
          }
        ]
      };

      this.chartInstance.setOption(initOption)

      this.chartInstance.on('mouseover', () => {
        clearInterval(this.timerId)
      })
      this.chartInstance.on('mouseout', this.startInterval)
    },


    // 发送请求，获取数据
    async getData() {

      console.log(this.$route.params.city_name)
      // const { data: res } = await axios.get('http://127.0.0.1:5000/radar',{params:{city_name:this.$route.params.city_name,year:this.selectedValue}})
      // console.log(data)

      
      //后端返回数据注意格式
      const res=[{"name":"苏州",
                  "value":['0.32','0.66','0.8','0.35','0.56','0.69','0.38','0.7'],
                  },            
      ]

      this.allData = res

      this.updateChart()
    },



    // 更新图表配置项
    updateChart() {
      const dataOption = {
        // tooltip: {
        //   // 这里为item 可以为内部的数据开启 单独的 tooltip
        //   trigger: 'item',
        // },
        series: [
          {
            data:this.allData
          }
        ]
      }

      this.chartInstance.setOption(dataOption)

      // 开启定时切换
      this.startInterval()
    },


    // 不同分辨率的响应式
    screenAdapter() {
      const titleFontSize = (this.$refs.stockRef.offsetWidth / 100) * 3.6

      const adapterOption = {
        title: {
          textStyle: {
            fontSize: titleFontSize,
          },
        },
        tooltip: {
          textStyle: {
            fontSize:titleFontSize*0.4
          },
        },
        series: [
          {
            barWidth: titleFontSize,
            itemStyle: {
              barBorderRadius: [titleFontSize / 2, titleFontSize / 2, 0, 0]
            }
          }
        ]
      }
      this.chartInstance.setOption(adapterOption)
      this.chartInstance.resize()
    },
    // 定时器不断切换当前页数
    startInterval() {
      this.timerId && clearInterval(this.timerId)

      this.timerId = setInterval(() => {
        this.currentIndex++
        if (this.currentIndex > 2) this.currentIndex = 1
        // 在更新完数据后，需要更新页面
        this.updateChart()
      }, 5000)
    },
  },
}
</script>
