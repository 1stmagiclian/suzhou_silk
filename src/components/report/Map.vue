<template>
    <div v-if="!showMap" class="start-image-container">
      <img class="start-image" src="../../assets/images/silk.jpg" />
      <button class="start-button" @click="enterMap">进入地图</button>
    </div>
    <div v-else class="com-container" @dblclick="chinaMap">
      <div class="com-chart" ref="mapRef"></div>
    </div>
</template>

<script>
import { getProvinceMapInfo } from '@/utils/map_utils'//中文城市名转成拼音
import { mapState } from 'vuex'
import axios from 'axios'


export default {
  name: 'Map',
  data() {
    return {
      showMap: true,
      // axios实例对象
      axiosInstance: null,
      // 图表的实例对象
      chartInstance: null,
      // 中国地图数据
      chinaMapData: null,
      // 从服务器中获取的所有数据
      allData: null,
      // 获取省份矢量地图数据缓存
      cityMapData: {},
    }
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
  created() {
    this.getData()
  },


  //mounted挂载 执行内容 创建实例 请求数据 渲染图表 添加窗口尺寸变化的监听
  mounted() {
    this.initChart()
    this.getData()
    window.addEventListener('resize', this.screenAdapter)
    // 主动触发 响应式配置
    this.screenAdapter()
  },
  destroyed() {
    window.removeEventListener('resize', this.screenAdapter)
  },


  methods: {
    
    enterMap() {
      // 初始化地图并设置 showMap 为 true，确保地图容器能够正常渲染
      this.showMap = true;
    },
    // 初始化图表的方法
    async initChart() {
      this.chartInstance = this.$echarts.init(this.$refs.mapRef, this.theme)
      // const ret = await axios.get('http://39.107.97.152:8077/static/map/china.json')
      const ret = await axios.get('http://localhost:8999/static/map/china.json')


      // 注册地图数据
      // this.$echarts.registerMap('china', this.chinaMapData)
      this.$echarts.registerMap('china', ret.data)
      

      // 初始化配置项
      const initOption = {
        // backgroundColor:"rgb(22, 21, 34, 0.75)",
        backgroundColor: "rgb(255, 248, 220,0.9)", // Creamy beige background


        title: {
          text: '▎城市分布',
          left: 20,
          top: 20,
          textStyle: {
            // color: '#fff',
            color: '#6B4226', 
            fontFamily: 'serif',
            fontSize: 23,
          },
        },

        geo: {  
          type: 'map',
          map: 'china',
          top: '5%',
          bottom: '5%',
          roam: true,
          label: {
            show: true,
            color: 'black', // 使用黑色字体
            fontWeight: 'bold', // 加粗字体
            // fontSize: 16,
            formatter: '{a}', // 显示区域名称
          },
          itemStyle: {         
            areaColor: {
              type: 'radial',
              x: 0.5,
              y: 0.5,
              r: 0.8,
              colorStops: [
                {
                  offset: 0,
                  color: 'rgba(255, 240, 200, 1)', // 淡黄色作为底色
                },
                {
                  offset: 1,
                  color: 'rgba(255, 210, 90, 1)', // 淡橙色作为顶色
                },
              ],
              globalCoord: false,
            },
            borderWidth: 1,
            borderColor: 'rgba(160, 120, 50, 1)', // 使用棕色边框
            emphasis: {
              label: {
                color: 'black', // 加强文字对比度
              },
              itemStyle: {
                areaColor: 'rgba(230, 180, 90, 1)', // 强调区域使用淡棕色
              },
            },
          },
          emphasis: {
            label: {
              show: true,
              color: 'black',
              fontWeight: 'bold',
            },
            itemStyle: {
              areaColor: 'rgba(230, 180, 90, 1)',
            },
          },
        }

      }
      this.chartInstance.setOption(initOption)

      // 进入省份事件函数
      this.chartInstance.on('click', async e => {
        
        // 通过工具函数拿到点击的地图对应的中文拼音(key),和拼接出需要的文件路径(path)
        const ProvinceInfo = getProvinceMapInfo(e.name)

        // 先判断是否已经存在需要请求的数据
        if (!this.cityMapData[ProvinceInfo.key]) {
          // 不存在： 发送请求,获取点击的地图的矢量数据
          const { data: res } = await this.$http.get(ProvinceInfo.path)
          // 把请求到的数据保存下来
          this.cityMapData[ProvinceInfo.key] = res
          // 注册点击的地图
          this.$echarts.registerMap(ProvinceInfo.key, res)
        }

        // 设置最新的配置项
        const changeOption = {
          geo: {
            map: ProvinceInfo.key,
            center: undefined,
          },
        }
        // 赋值给 echarts实例
        this.chartInstance.setOption(changeOption)
      })

      
      this.chartInstance.on('click',arg=>{
        //console.log(arg)
        if(arg.name=='济南'){
          window.location.href='http://localhost:8999/#/show/"济南"'
        }
        if(arg.name=='南京'){
          window.location.href='http://localhost:8999/#/show/"南京"'
        }
        if(arg.name=='苏州'){
          window.location.href='http://localhost:8999/#/show/"苏州"'
        }
        if(arg.name=='昆明'){
          window.location.href='http://localhost:8999/#/show/"昆明"'
        }
        if(arg.name=='西安'){
          window.location.href='http://localhost:8999/#/show/"西安"'
        }
  
      })


    },


    // 发送请求，获取数据
    async getData() {
      // http://101.34.160.195:8888/api/map
      // const { data: res } = await this.$http.get('/map')

      const res =      [
                          {
                              // "name": "南方城市",
                              "children": [
                                  {
                                      "name": "苏州",
                                      "value": [120.619585, 31.299379]
                                  },
                                  // {
                                  //     "name": "南京",
                                  //     "value": [118.767413, 32.041544]
                                  // },{
                                  //     "name": "昆明",
                                  //     "value": [102.73,25.04]
                                  // }
                                  
                              ]
                          },
                          {
                              // "name": "北方城市",
                              // "children": [
                              //     {
                              //         "name": "济南",
                              //         "value": [117,36.65]
                              //     },{                                  
                              //         "name": "西安",
                              //         "value": [108.948024,34.263161]
                              //     }
                                  
                              // ]
                          }
                      ]

      //console.log(res)
      this.allData = res

      this.updateChart()
    },


    // 更新图表配置项
    updateChart() {
      // 图例的数据
      const legendArr = this.allData.map(item => {
        return item.name
      })
      // 散点图的数据
      const seriesArr = this.allData.map(item => {
        // return 一个类别下的所有散点数据
        return {
          type: 'effectScatter',
          // 图例的name需要与series的name相同
          name: item.name,   //黄金用户、白金用户
          data: item.children,

          // 让散点图使用地图坐标系统
          coordinateSystem: 'geo',
          // 涟漪动画效果配置
          rippleEffect: {
            // 涟漪效果直径
            scale: 6,
            // 空心的涟漪动画效果
            brushType: 'stroke',
          },
        }
      })

      // 数据配置项
      const dataOption = {

        color:['#e705ef', '#550cde'],
        //左下角的图例效果
        legend: {
          left: '2%',
          bottom: '5%',
          // 图例的方向
          orient: 'verticle',
          data: legendArr.reverse(),
        },
        
        series: seriesArr
      }

      this.chartInstance.setOption(dataOption)
    },


    // 不同分辨率的响应式
    screenAdapter() {
      // 当前比较合适的字体大小
      const titleFontSize = (this.$refs.mapRef.offsetWidth / 100) * 3.6

      // 响应式的配置项
      const adapterOption = {
        title: {
          textStyle: {
            fontSize: 25,
          },
        },
        legend: {
          // 图例宽度
          itemWidth: titleFontSize / 2,
          // 图例高度
          itemHeight: titleFontSize / 2,
          // 间隔
          itemGap: titleFontSize / 2,
          textStyle: {
            fontSize: titleFontSize / 2,
          },
        },
      }

      this.chartInstance.setOption(adapterOption)
      this.chartInstance.resize()
    },

    // 回到中国地图
    chinaMap() {
      const chinaMapOption = {
        geo: {
          map: 'china',
        },
      }
      this.chartInstance.setOption(chinaMapOption)
    },
  },
}
</script>

<style lang="less" scoped>
.start-image-container {
  text-align: center;
  position: relative; /* 添加相对定位 */
}
.start-image {
  max-width: 85%;
  height: 0 auto;
  display: block;
  margin: 10 auto;
  margin-left: 50px;
}
.start-button {
  position: absolute; /* 绝对定位 */
  top: 20%; /* 将按钮垂直居中 */
  left: 95%; /* 将按钮水平居中 */
  transform: translate(-50%, -50%); /* 水平和垂直都居中 */
  padding: 10px 20px;
  background-color: #007bff;
  color: red;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>