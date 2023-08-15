<template>
    <div class="app">
      <div class="image-gallery-container">
        <ImageGallery :images="visibleImages" :currentPage="currentPage" :perPage="perPage" />
        <div class="pagination">
          <button @click="changePage(-1)" :disabled="currentPage === 1" class="pagination-button">上一页</button>
          <span>{{ currentPage }} / {{ totalPages }}</span>
          <button @click="changePage(1)" :disabled="currentPage === totalPages" class="pagination-button">下一页</button>
        </div>
        <button @click="goBack" class="return-button">返回主页</button> <!-- 新添加的返回按钮 -->
        <button @click="goToHome" class="button">检索平台</button>
        <button @click="goToExternalPage" class="button2">标注平台</button>
      </div>
    </div>
  </template>
  
<script>
import ImageGallery from '../report/ImageGallery.vue'
  
  export default {
    // 库存和销量分析
    name: 'Stock',
    components: {
        ImageGallery,
    },
    data() {
        return {
            imageArray: [
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_42_41.637.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_43_10.095.jpeg', description: '古代器物展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_44_23.626.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_42_41.637.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_43_10.095.jpeg', description: '古代器物展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_44_23.626.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_44_23.626.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_43_10.095.jpeg', description: '古代器物展示' },
                
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_42_41.637.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_43_10.095.jpeg', description: '古代器物展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_44_23.626.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_42_41.637.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_43_10.095.jpeg', description: '古代器物展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_44_23.626.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_44_23.626.jpeg', description: '古代铜盘展示' },
                { url: 'http://10.156.8.23:8086/image/wuyuhui/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2/%E6%95%85%E5%AE%AB%E5%8D%9A%E7%89%A9%E9%99%A2%E6%95%B0%E5%AD%97%E6%96%87%E7%89%A9%E5%BA%93-1400-1999/%E5%9B%BE%E7%89%87/2022-10-14%2021_43_10.095.jpeg', description: '古代器物展示' },
            ],
            currentPage: 1,
            perPage: 10, 
        };
    },
    computed: {
        totalPages() {
          return Math.ceil(this.imageArray.length / this.perPage);
        },
        visibleImages() {
          const startIndex = (this.currentPage - 1) * this.perPage;
          const endIndex = startIndex + this.perPage;
          return this.imageArray.slice(startIndex, endIndex);
        },
    },
    methods: {
        changePage(change) {
          this.currentPage += change;
        },
        goBack() {
          history.back(); // 返回上一页
        },
        goToHome() {
          window.location.href = 'http://10.156.8.21:8006/#/home'; 
        },
        goToExternalPage() {
          window.location.href = 'http://10.156.8.21:8894/files/html/newSystem.html?_ijt=u4vfe8ujgs48lgu2be2qvbafsb';
        },
    },
  };
  </script>
  
  <style>
  .app {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background-color: #f0f0f0;
  }
  .image-gallery-container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 100px;
    }

  .pagination-button {
    padding: 10px 20px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    margin: 0 5px; /* 调整按钮之间的水平间距 */
  }

  .return-button{

    padding: 10px 20px;
    background-color: #e97815;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;

    position: fixed;
    bottom: 60px; /* 调整按钮距离底部的距离 */
    left: 90%;
    transform: translateX(-50%);

  }

  .button{

    padding: 10px 20px;
    background-color: #e97815;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;

    position: fixed;
    bottom: 60px; /* 调整按钮距离底部的距离 */
    left: 10%;
    transform: translateX(-50%);

  }
  .button2{

    padding: 10px 20px;
    background-color: #e97815;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;

    position: fixed;
    bottom: 60px; /* 调整按钮距离底部的距离 */
    left: 20%;
    transform: translateX(-50%);

  }

  .pagination-button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
</style>