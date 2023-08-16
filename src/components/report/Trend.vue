<template>
  <div class="com-container">
    <div class="com-chart" ref="trendRef"></div>
    <!-- <i class="iconfont icon-left" @click="toLeft" :style="themeStyle">&#xe6ef;</i> -->
    <!-- <i class="iconfont icon-right" @click="toRight" :style="themeStyle">&#xe6ed;</i> -->
    <span class="cate-name" :style="themeStyle">{{ cateName }}</span>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { getThemeValue } from 'utils/theme_utils'
import axios from 'axios'

export default {
  // 地区销量排行
  name: 'Trend',
  data() {
    return {
      selectedValue: '经济规模', // 设置默认选中的值
      // 图表的实例对象
      chartInstance: null,
      // 从服务器中获取的所有数据
      currentIndex:0,
      allData: null,
      // 柱形图 区域缩放起点值
      startValue: 0,
      // 柱形图结 区域缩放终点值
      endValue: 9,
      // 定时器
      timerId: null,
      // PointerColor: this.axisPointerColor,
    }
  },
  created() {
    // this.$socket.registerCallBack('rankData', this.getData)
  },
  computed: {
    ...mapState(['theme']),
    // axisPointerColor() {
    //   return getThemeValue(this.theme).sellerAxisPointerColor
    // },
    //展示2020年份
    cateName() {
      if (!this.allData) return ''
      console.log(this.startValue)
      console.log(this.endValue)
      console.log(this.allData[this.currentIndex].data.length)
      return "年份："+this.allData[this.currentIndex].year
    },
    themeStyle() {
      if (!this.titleFontSize) {
        return { color: getThemeValue(this.theme).titleColor }
      }
      return {
        fontSize: this.titleFontSize + 'px',
        color: getThemeValue(this.theme).titleColor,
      }
    },
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
    }
  },
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
    // this.$socket.unRegisterCallBack('rankData')
  },
  methods: {
    handleSelectChange() {
      // 根据下拉框的值发送请求给后端接口，并获取数据
      this.screenAdapter()
      this.getData()
      // 更新图表
      this.updateChart()
    },
    // 初始化图表的方法
    initChart() { 
      this.chartInstance = this.$echarts.init(this.$refs.trendRef, this.theme)

      const initOption = {

        backgroundColor:"rgb(22, 21, 34, 0.75)",
        title: {
          text: '▎纹样演化史',
          left: 20,
          top: 20
        },
        tooltip: {},
        animationDurationUpdate: 1500,
        animationEasingUpdate: 'quinticInOut',
        series: [
          {
            type: 'graph',
            layout: 'none',
            symbolSize: 50,
            roam: true,
            label: {
              show: true
            },
            edgeSymbol: ['circle', 'arrow'],
            edgeSymbolSize: [4, 10],
            edgeLabel: {
              fontSize: 20
            },
            data: [
              {
                name: '纹样1',
                x: 300,
                y: 100
              },
              {
                name: '纹样2',
                x: 400,
                y: 100
              },
              {
                name: '纹样3',
                x: 500,
                y: 100
              },
              {
                name: '纹样4',
                x: 600,
                y: 100
              }
            ],
            links: [
              // {
              //   source: 0,
              //   target: 1,
              //   symbolSize: [5, 20],
              //   label: {
              //     show: true
              //   },
              //   lineStyle: {
              //     width: 5,
              //     curveness: 0.2
              //   }
              // },
              // {
              //   source: 'Node 2',
              //   target: 'Node 1',
              //   label: {
              //     show: true
              //   },
              //   lineStyle: {
              //     curveness: 0.2
              //   }
              // },
              {
                source: '纹样1',
                target: '纹样2'
              },
              {
                source: '纹样2',
                target: '纹样3'
              },
              {
                source: '纹样3',
                target: '纹样4'
              },
            ],
            lineStyle: {
              opacity: 0.9,
              width: 2,
              curveness: 0
            }
          }
        ]
      }
      this.chartInstance.setOption(initOption)
      this.chartInstance.on('click', function(params) {
        if (params.name === '纹样1') {window.open('www.baidu1.com')}
        if (params.name === '纹样2') {window.open('www.baidu2.com')}
        if (params.name === '纹样3') {window.open('www.baidu3.com')}
        if (params.name === '纹样4') {window.open('www.baidu4.com')}
      });
      // // 鼠标经过关闭 动画效果
      // this.chartInstance.on('mouseover', () => {
      //   clearInterval(this.timerId)
      // })
      // // 鼠标离开 开启动画效果
      // this.chartInstance.on('mouseout', () => {
      //   this.startInterval()
      // })
    },
    // 发送请求，获取数据
    async getData() {
      
      const { data: res } = await axios.get('http://39.107.97.152:5000/softpower',{params:{data_index:this.selectedValue}})

      this.allData = res
      // 对数据进行排序(大到小)
      // this.allData.sort((a, b) => b.value - a.value)

      
      this.updateChart()

      //额外添加
      this.screenAdapter()

      // 开始自动切换
      this.startInterval()
      
    },
    // 更新图表配置项
    updateChart() {
      // 渐变色数组
      const colorArr = [
        ['#0BA82C', '#4FF778'],
        ['#2E72BF', '#23E5E5'],
        ['#5052EE', '#AB6EE5']
      ]
    
      const showData=this.allData[this.currentIndex]
      // 所有省份组成的数组
      const provinceInfo = showData.data.map(item => item.name)
      // 所有省份对应的销售金额
      const dataArr = showData.data.map(item => item.data)
      const rankArr = showData.data.map(item => item.rank)


      const dataOption = {
        xAxis: {
          data: provinceInfo
        },
        dataZoom: {
          // 区域缩放组件
          show: false,
          startValue: this.startValue,
          endValue: this.endValue
        },
        series: [
          {
            name:'data',
            type:'bar',
            data: dataArr,
            itemStyle: {
              color: arg => {
                let targetColorArr = null

                if (arg.value > 80) {
                  targetColorArr = colorArr[0]
                } else if (arg.value > 50) {
                  targetColorArr = colorArr[1]
                } else {
                  targetColorArr = colorArr[2]
                }

                return new this.$echarts.graphic.LinearGradient(0, 0, 0, 1, [
                  // 0%
                  { offset: 0, color: targetColorArr[0] },
                  // 100%
                  { offset: 1, color: targetColorArr[1] }
                ])
              }
            }
          },
          {
            name:'rank',
            type:'line',
            smooth:false,
            lineStyle:{
              color:'#23E5E5'
            },
            yAxisIndex: 1,
            data: rankArr,
            Symbol:'circle',
            tooltip: {
              valueFormatter: function (value) {
                return value + ' 名';
              }
            },
          }
        ]
      }
      this.chartInstance.setOption(dataOption)
    },


    
    // 根据图标容器的宽度 计算各属性、标签、元素的大小
    screenAdapter() {
      const titleFontSzie = (this.$refs.trendRef.offsetWidth / 100) * 2.5

      const adapterOption = {
        title: {
          textStyle: {
            fontSize: titleFontSzie
          }
        },
        series: [
          {
            barWidth: titleFontSzie,
            itemStyle: {
              barBorderRadius: [titleFontSzie / 2, titleFontSzie / 2, 0, 0]
            }
          }
        ]
      }
      this.chartInstance.setOption(adapterOption)
      this.chartInstance.resize()
    },
    // 点击左侧按钮
    toLeft() {
      this.currentIndex--
      // 已到达最左边
      if (this.currentIndex < 0) this.currentIndex = this.allData.length - 1
      this.updateChart()
    },
    // 点击右侧按钮
    toRight() {
      this.currentIndex++
      // 已到达最右边
      if (this.currentIndex > this.allData.length - 1) this.currentIndex = 0
      this.updateChart()
    },
    // 改变柱形图 区域缩放起始与终点值的函数
    startInterval() {
      // 如果存在则关闭
      this.timerId && clearInterval(this.timerId)

      this.timerId = setInterval(() => {
        this.startValue++
        this.endValue++
        if (this.endValue > this.allData[this.currentIndex].data.length - 1) {
          this.startValue = 0
          this.endValue = 9
        }
        this.updateChart()
      }, 3000)
    },
  }
}
</script>

<!-- <style lang="less" scoped>
.com-container {
  i {
    z-index: 999;
    position: absolute;
    transform: translateY(-50%);
    top: 50%;
    cursor: pointer;
  }
  i.icon-left {
    left: 1%;
  }
  i.icon-right {
    right: 1%;
  }
  .cate-name {
    position: absolute;
    right: 39%;
    top: 25px;
    z-index: 999;
  }
}

select {
  /* 调整下拉框的样式 */
  z-index: 1;
  width: 100px; /* 设置宽度 */
  height: 30px; /* 设置高度 */
  padding: 5px; /* 设置内边距 */
  font-size: 14px; /* 设置字体大小 */
  border: 1px solid #ccc; /* 设置边框样式 */
  border-radius: 4px; /* 设置边框圆角 */
  position: absolute; /* 设置绝对定位 */
  top: 20px; /* 设置相对于父容器的顶部偏移量 */
  right: 100px; /* 设置相对于父容器的右侧偏移量 */
  
  // color: black; /* 设置字体颜色 */
  background-color: #23E5E5; /* 设置背景色 */
}
</style> -->