<template>
  <div class="com-container">
    <select v-model="selectedValue" @change="handleSelectChange">
      <option>2020</option>
      <option>2021</option>
    </select>
    <!-- <button @click="gotolink" class="btn btn-success">返回</button> -->
    <div class="com-chart" ref="stockRef">
      <!-- <button @click="gotolink" class="btn btn-success">返回</button> -->
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
    // this.$socket.send({
    //   action: 'getData',
    //   socketType: 'stockData',
    //   chartName: 'stock',
    //   value: '',
    // })
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

    handleSelectChange() {
      console.log(this.selectedValue)
      // 根据下拉框的值发送请求给后端接口，并获取数据
      this.screenAdapter()
      this.getData()
      // 更新图表
      this.updateChart()
    },

    gotolink(){

      //点击跳转至上次浏览页面
      this.$router.go(-1)
    },


    // 初始化图表的方法
    initChart() {
      this.chartInstance = this.$echarts.init(this.$refs.stockRef, this.theme)
      const initOption = {
        // backgroundColor:"rgb(221, 221, 221)",

        
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
      const { data: res } = await axios.get('http://39.107.97.152:5000/radar',{params:{city_name:this.$route.params.city_name,year:this.selectedValue}})


      this.allData = res

      this.updateChart()
    },



    // 更新图表配置项
    updateChart() {
      // 需要显示的原始数据   包含0，不好含


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

<style lang="less" scoped>
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
  top: 60px; /* 设置相对于父容器的顶部偏移量 */
  right: 400px; /* 设置相对于父容器的右侧偏移量 */
  
  // color: black; /* 设置字体颜色 */
  background-color: #23E5E5; /* 设置背景色 */
}
</style>