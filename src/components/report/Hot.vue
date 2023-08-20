<template>
  <div class="com-container">
    <!-- <select v-model="selectedValue" @change="handleSelectChange">
      <option>2020</option>
      <option>2021</option>
    </select> -->
    <div class="com-chart" ref="hotRef"></div>
  </div>
</template>


<script>
import { mapState } from 'vuex'
import "echarts-wordcloud"
import "echarts-wordcloud/dist/echarts-wordcloud"
import "echarts-wordcloud/dist/echarts-wordcloud.min"
import axios from 'axios'


export default {
  name: 'Hot',
  data() {
    return {
      selectedValue: 2020, // 设置默认选中的值
      // 图表的实例对象
      chartInstance: null,
      // 从服务器中获取的所有数据
      allData: null,
      // 当前显示的一级分类数据类型
      // currentIndex: 0,
      // 字体响应式大小
      titleFontSize: null,
    }
  },
  created() {
    // this.getData()
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
  mounted() {
    this.initChart()
    this.getData()
    window.addEventListener('resize', this.screenAdapter)
    // 主动触发 响应式配置
    this.screenAdapter()
  },
  destroyed() {
    window.removeEventListener('resize', this.screenAdapter)
    // this.$socket.unRegisterCallBack('hotData')
  },
  methods: {
    handleSelectChange() {
      console.log(this.selectedValue)
      // 根据下拉框的值发送请求给后端接口，并获取数据
      this.screenAdapter()
      this.getData()
      // 更新图表
      this.updateChart()
    },
    // 初始化图表的方法
    initChart() {
      this.chartInstance = this.$echarts.init(this.$refs.hotRef, this.theme)
      const initOption = {
        backgroundColor: "rgb(189,189,189,0.8)", // Creamy beige background
        animationDurationUpdate: 1800,
        animationEasingUpdate: 'quinticInOut',
        title: {
          text: '▎苏州丝绸纹样词云分析',
          left: 20,
          top: 20,
          textStyle: {
            // color: '#fff',
            color: '#6B4226', 
            fontFamily: 'serif',
            fontSize: 23,
          },
        },
        tooltip: {
          show: true,
        },
      }
      this.chartInstance.setOption(initOption)
    },
    // 发送请求，获取数据
    async getData() {
      // const { data: res } = await axios.get('http://39.107.97.152:5000/wordcloud',{params:{year:this.selectedValue}})
      const res = [
            {
              name: "富贵不断",
              value: 450
            },
            {
              name: "江山万代",
              value: 430
            },
            {
              name: "吉祥",
              value: 420
            },
            {
              name: "福",
              value: 400
            },
            {
              name: "禄",
              value: 400
            },
            {
              name: "寿",
              value: 400
            },
            {
              name: "梅兰竹菊",
              value: 380
            },
            {
              name: "团花",
              value: 360
            },
            {
              name: "八吉祥",
              value: 350
            },
            {
              name: "如意盖",
              value: 350
            },
            {
              name: "寿桃",
              value: 350
            },
            {
              name: "云鹤",
              value: 330
            },
            {
              name: "喜鹊",
              value: 330
            },
            {
              name: "牡丹",
              value: 300
            },
            {
              name: "宝剑",
              value: 280
            },
            {
              name: "书卷",
              value: 280
            },
            {
              name: "如意",
              value: 280
            },
            {
              name: "蝴蝶",
              value: 250
            },
            // {
            //   name: "缠枝花",
            //   value: 230
            // },
            // {
            //   name: "折枝花",
            //   value: 230
            // },
            // {
            //   name: "石榴",
            //   value: 200
            // },
            // {
            //   name: "仕女",
            //   value: 200
            // },
          ]
      this.allData = res
      this.updateChart()
      this.screenAdapter()

    },
    // 更新图表配置项
    updateChart() {

      console.log(this.allData)
        
      const dataOption = {
        // legend: {
        //   data: legenDateArr,
        // },
        series: [
          {
            type: "wordCloud",
            gridSize: 20,
            rotationRange: [0, 0],
            shape: 'circle',
            textStyle: {
              normal: {
                fontFamily: 'Impact, sans-serif',
                fontWeight: 'bold',
                // color: function() {
                //   return 'rgb(' +
                //     Math.round(Math.random() * 255) +
                //     ',' +
                //     Math.round(Math.random() * 255) +
                //     ',' +
                //     Math.round(Math.random() * 255) +
                //     ')';
                // }
                color:function(params){
                    if(params.value >=250 && params.value <300){
                      return "#a304d3";
                    }else if(params.value >=300 && params.value<350 ){
                      return "#8E44AD";
                    }
                    else if(params.value >=350 && params.value<400 ){
                      return "#34495E";
                    }
                    else if(params.value >=400 && params.value<450 ){
                      return "#00BCD4";
                    }
                      else if(params.value >=450 && params.value<500 ){
                      return "#2980B9";
                    }
                    return "#2004d3";
                  }
              }
            },
            left: 'center',
            top: '16%',
            // width: '90%',
            // height: '70%',
            drawOutOfBound: true,
            data: this.allData
          }
        ]
      }
      this.chartInstance.setOption(dataOption)
    },
    //不同分辨率的响应式
    screenAdapter() {
      const titleFontSize = (this.$refs.hotRef.offsetWidth / 100) * 4

      const adapterOption = {
        title: {
          textStyle: {
            fontSize: 25,
          },
        },
        series: [
          {
            width: titleFontSize*25,
            // height: titleFontSize*60,
            // gridSize: titleFontSize*0.5,     
            sizeRange: [titleFontSize, titleFontSize*2],    
          }
        ]
      }
      this.chartInstance.setOption(adapterOption)
      this.chartInstance.resize()
    },
  },
}
</script>

<style lang="less" scoped>
select {
  /* 调整下拉框的样式 */
  z-index: 1;
  width: 70px; /* 设置宽度 */
  height: 30px; /* 设置高度 */
  padding: 5px; /* 设置内边距 */
  font-size: 14px; /* 设置字体大小 */
  border: 1px solid #ccc; /* 设置边框样式 */
  border-radius: 4px; /* 设置边框圆角 */
  position: absolute; /* 设置绝对定位 */
  top: 20px; /* 设置相对于父容器的顶部偏移量 */
  right: 60px; /* 设置相对于父容器的右侧偏移量 */
  
  // color: black; /* 设置字体颜色 */
  background-color: #23E5E5; /* 设置背景色 */
}
</style>