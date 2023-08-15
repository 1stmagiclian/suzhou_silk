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
        backgroundColor:"rgb(22, 21, 34, 0.75)",

      //   title: {
      //     text: '▎雷达图',
      //     left: 20,
      //     top: 20,
      //   },
      //   name: {
      //       textStyle: {
      //           color: '#fff',
      //           fontSize: 16
      //       },
      //   },
      // backgroundColor: 'rgba(0, 0, 0, 0.3)',

      title: {
        text: '▎丝绸颜色雷达图',
        left: 20,
        top: 20,
        textStyle: {
          color: '#fff',
          fontFamily: 'serif',
          fontSize: 18,
        },
      },

      name: {
        textStyle: {
          color: '#fff',
          fontSize: 14,
          fontFamily: 'serif',
        },
      },
              
        radar: {
          // shape: 'circle',
          indicator: [
            { name: '水墨青', max: 1 },
            { name: '浅碧绿', max: 1 },
            { name: '紫罗兰', max: 1 },
            { name: '古朴紫', max: 1 },
            { name: '砚墨灰', max: 1 },
            { name: '秋叶橙', max: 1 },
            { name: '宫廷红', max: 1 },
            { name: '清雅蓝', max: 1 },
          ],
        
          //雷达图背景的颜色，在这儿随便设置了一个颜色，完全不透明度为0，就实现了透明背景

          axisLine: { //指向外圈文本的分隔线样式
            lineStyle: {
                // color: '#013A3F'
                width:0.1
            }
          },
          splitArea: {
              show: false,
              areaStyle: {
                  color: "rgba(255,0,0,0)", // 图表背景的颜色
              },
          },
          splitLine: {
              show: true,
              lineStyle: {
                  width: 1,  // 分隔线线宽
                  color: "#47EDFC", // 设置网格的颜色【分隔线颜色】
              },
          },


      },

        //todo：格式化输出样式
        tooltip: {
          show: true,
          trigger:'item',
          position: ['75%','18%'],
          // backgroundColor:"#",
          textStyle:{
            // align:'left'
          },
          formatter:(params) => {  // params就是数据，这里可以打印一下看看
              // return 出去什么，鼠标移入就显示什么,marker就是提示前面蓝色的圆点
              return `城市名：&#8194&#8194&#8194${params.data['name']}&#8194&#8194&#8194</br>
                      ${params.marker}水墨青: ${params.data['value'][0]} </br></br>
                      ${params.marker}浅碧绿: ${params.data['value'][1]} </br></br>
                      ${params.marker}紫罗兰: ${params.data['value'][2]} </br></br>
                      ${params.marker}古朴紫: ${params.data['value'][3]} </br></br>
                      ${params.marker}砚墨灰: ${params.data['value'][4]} </br></br>
                      ${params.marker}秋叶橙: ${params.data['value'][5]} </br></br>
                      ${params.marker}宫廷红: ${params.data['value'][6]} </br></br>
                      ${params.marker}清雅蓝: ${params.data['value'][7]} </br></br>
                      `
              }
        },

        //雷达图数据样式图
        series:[
          {
            type:'radar',
            areaStyle: {
              color: "rgba(71,237,252,.3)" 
            },
            lineStyle: {
              color: "#47EDFC",
              // 阴影折线宽度
              width: 5
            },
          }
        ]
        
      }
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
