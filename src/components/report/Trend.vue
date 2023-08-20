<template>
  <div class="com-container">
    <div class="left-bar"> 
        <div class="com-chart" ref="trendRef"></div>
    </div>
    <div class="right-bar">
      <div class="image-container" v-if="currentImage" :style="imageContainerStyle">
          <img :src="currentImage" alt="Current Image" class="responsive-image">
      </div>
    </div>
  </div>
</template>



<style>
.left-bar {
  margin-top: 0px;
  float: left;
  width: 70%;
  height: 115%;
  position: absolute;
  overflow: hidden;
}

.right-bar {
  margin-top: 0px;
  float: right;
  right: 5%;
  width: 25%;
  height: 100%;
  position: absolute;
  overflow: hidden;
}

.responsive-image {
  margin-top: 10%;
  max-width: 100%; /* 限制最大宽度 */
  max-height: 90%; /* 限制最大高度 */
  width: auto; /* 自动调整宽度 */
  height: auto; /* 自动调整高度 */
  object-fit: contain; /* 可根据需要使用不同的适应方式 */
}

</style>

<script>
import { mapState } from 'vuex'
import { getThemeValue } from 'utils/theme_utils'
import axios from 'axios'

export default {
  // 地区销量排行
  name: 'Trend',
  data() {
    return {
      currentImage: null
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
    imageContainerStyle() {
      return {
        position: 'absolute',
        top: `${this.hoveredPatternPosition.y}px`,
        left: `${this.hoveredPatternPosition.x}px` // 可根据需要调整偏移量
      }
    },
    imageStyle() {
      return {
        // width: '300px', // 设置图片宽度
        // height: '200px', // 自动保持图片高度与宽度的比例
        // zIndex: -1,
        // /* 可根据需要进行其他样式设置 */
      };
    },
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
    this.
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
      var node1={name:'鹿纹',label:'鹿纹(狩猎纹锦，东周复制件)'}
      var node2={name:'舞人动物纹锦',label:'舞人动物纹锦(战国复制件)'}
      var node3={name:'五星锦',label:'五星锦(汉晋复制件)'}
      var node4={name:'花鸟纹锦',label:'花鸟纹锦(唐代复制件)'}
      var node5={name:'团龙团凤纹宋锦',label:'团龙团凤纹宋锦(清代复制件)'}
      var node6={name:'孔雀纹补',label:'孔雀纹补(明代)'}
      var node7={name:'石青缂织衮织成',label:'石青缂织衮织成(清)'}
      this.chartInstance = this.$echarts.init(this.$refs.trendRef, this.theme)
      const initOption = {

        backgroundColor: "rgb(189,189,189,0.3)", // Creamy beige background
        title: {
          text: '▎苏州丝绸纹样演化史',
          left: 20,
          top: 20,
          textStyle: {
            // color: '#fff',
            color: '#6B4226', 
            fontFamily: 'serif',
            fontSize: 23,
          },
        },
        tooltip: {},
        animationDurationUpdate: 1800,
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
                name: node1.name,
                tooltip: {
                  show: true,
                  formatter: node1.label, // 在这里设置字符串信息
                },
                // value:node1.value,
                x: 0,
                y: 0
              },
              {
                name: node2.name,
                tooltip: {
                  show: true,
                  formatter: node2.label, // 在这里设置字符串信息
                },
                x: 5,
                y: 3
              },
              {
                name: node3.name,
                tooltip: {
                  show: true,
                  formatter: node3.label, // 在这里设置字符串信息
                },
                x: 10,
                y: 0
              },
              {
                name: node4.name,
                tooltip: {
                  show: true,
                  formatter: node4.label, // 在这里设置字符串信息
                },
                x: 15,
                y: 3
              },
              {
                name: node5.name,
                tooltip: {
                  show: true,
                  formatter: node5.label, // 在这里设置字符串信息
                },
                x: 20,
                y: 0
              },
              {
                name: node6.name,
                tooltip: {
                  show: true,
                  formatter: node6.label, // 在这里设置字符串信息
                },
                x: 25,
                y: 3
              },
              {
                name: node7.name,
                tooltip: {
                  show: true,
                  formatter: node7.label, // 在这里设置字符串信息
                },
                x: 30,
                y: 0
              },
            ],
            links: [
              {
                source: node1.name,
                target: node2.name,
                
              },
              {
                source: node2.name,
                target: node3.name
              },
              {
                source: node3.name,
                target: node4.name
              },
              {
                source: node4.name,
                target: node5.name
              },
              {
                source: node5.name,
                target: node6.name
              },
              {
                source: node6.name,
                target: node7.name
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
      // params.data.name='纹样1'
      this.chartInstance.setOption(initOption)
      // 鼠标经过关闭 动画效果
      this.chartInstance.on('mouseover', (params) => {
        const name = params.data.name
        // console.log(name)
        var currentImage1= 'http://39.107.97.152:9016/silk_picture/%E5%8A%A8%E7%89%A9%E7%BA%B9/%E9%B9%BF%E7%BA%B9%28%E7%8B%A9%E7%8C%8E%E7%BA%B9%E9%94%A6%EF%BC%8C%E4%B8%9C%E5%91%A8%E5%A4%8D%E5%88%B6%E4%BB%B6%29.png'
        var currentImage2= 'http://39.107.97.152:9016/silk_picture/%E5%8A%A8%E7%89%A9%E7%BA%B9/%E8%88%9E%E4%BA%BA%E5%8A%A8%E7%89%A9%E7%BA%B9%E9%94%A6%28%E6%88%98%E5%9B%BD%E5%A4%8D%E5%88%B6%E4%BB%B6%29.png'
        var currentImage3= 'http://39.107.97.152:9016/silk_picture/%E5%8A%A8%E7%89%A9%E7%BA%B9/%E4%BA%94%E6%98%9F%E9%94%A6%28%E6%B1%89%E6%99%8B%E5%A4%8D%E5%88%B6%E4%BB%B6%29.png'
        var currentImage4= 'http://39.107.97.152:9016/silk_picture/%E5%8A%A8%E7%89%A9%E7%BA%B9/%E8%8A%B1%E9%B8%9F%E7%BA%B9%E9%94%A6%28%E5%94%90%E4%BB%A3%E5%A4%8D%E5%88%B6%E4%BB%B6%29.png'
        var currentImage5= 'http://39.107.97.152:9016/silk_picture/%E5%8A%A8%E7%89%A9%E7%BA%B9/%E5%9B%A2%E9%BE%99%E5%9B%A2%E5%87%A4%E7%BA%B9%E5%AE%8B%E9%94%A6%28%E6%B8%85%E4%BB%A3%E5%A4%8D%E5%88%B6%E4%BB%B6%29.JPG'
        var currentImage6= 'http://39.107.97.152:9016/silk_picture/%E5%8A%A8%E7%89%A9%E7%BA%B9/%E5%AD%94%E9%9B%80%E7%BA%B9%E8%A1%A5%28%E6%98%8E%E4%BB%A3%29.png'
        var currentImage7= 'http://39.107.97.152:9016/silk_picture/%E5%8A%A8%E7%89%A9%E7%BA%B9/%E7%9F%B3%E9%9D%92%E7%BC%82%E7%BB%87%E8%A1%AE%E7%BB%87%E6%88%90%28%E6%B8%85%29.png'
        if (params.data && params.name === node1.name) {this.currentImage = currentImage1;this.hoveredPatternPosition = { x: 0, y: 0 };}
        if (params.data && params.name === node2.name) {this.currentImage = currentImage2;this.hoveredPatternPosition = { x: 0, y: 0 };}
        if (params.data && params.name === node3.name) {this.currentImage = currentImage3;this.hoveredPatternPosition = { x: 0, y: 0 };}
        if (params.data && params.name === node4.name) {this.currentImage = currentImage4;this.hoveredPatternPosition = { x: 0, y: 0 };}
        if (params.data && params.name === node5.name) {this.currentImage = currentImage5;this.hoveredPatternPosition = { x: 0, y: 0 };}
        if (params.data && params.name === node6.name) {this.currentImage = currentImage6;this.hoveredPatternPosition = { x: 0, y: 0 };}
        if (params.data && params.name === node7.name) {this.currentImage = currentImage7;this.hoveredPatternPosition = { x: 0, y: 0 };}
        console.log(params.data.x)
        console.log(params.data.y)
        // var model  = this.getModel().getSeriesByIndex(0).getData()._itemLayouts;
        // console.log('model',model);
      })
      // // 鼠标离开 开启动画效果
      // this.chartInstance.on('mouseout', () => {
      //   this.currentImage = null;
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