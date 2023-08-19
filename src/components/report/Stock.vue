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
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/01%E7%BA%A2%E5%9C%B0%E7%BB%BF%E7%89%A1%E4%B8%B9%E7%BA%B9%E9%97%AA%E7%BC%8E%E6%8A%AB%E6%96%99.jpg', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/02%E7%B2%89%E8%89%B2%E6%9A%97%E8%8A%B1%E7%BB%B8%E9%95%B6%E7%BB%A3%E8%BE%B9%E5%A5%B3%E9%A9%AC%E5%A4%B9.jpg', description: '古代器物展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/03%E8%93%9D%E8%89%B2%E7%BC%8E%E7%BB%A3%E8%8A%B1%E8%9D%B6%E7%BA%B9%E5%A5%B3%E8%A4%82.jpg', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/04%E6%9C%B1%E7%BA%A2%E7%BC%8E%E5%9C%B0%E5%BD%A9%E7%BB%A3%E9%A3%8E%E6%99%AF%E8%8A%B1%E5%8D%89%E7%BA%B9%E5%AF%B9%E8%A5%9F%E5%A5%B3%E8%A4%82.jpg', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/05%E5%A4%A7%E7%BA%A2%E5%9C%B0%E7%BB%87%E5%8B%BE%E8%8E%B2%E7%89%A1%E4%B8%B9%E7%9B%B4%E5%BE%84%E7%BA%B1.jpg', description: '古代器物展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/06%E7%BA%A2%E5%9C%B0%E5%8A%A0%E9%87%91%E9%BE%99%E5%87%A4%E5%A6%86%E8%8A%B1%E7%BC%8E%E8%A3%99.jpg', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/07%E8%8D%89%E7%BB%BF%E6%9A%97%E8%8A%B1%E7%BB%B8%E9%B1%BC%E9%B3%9E%E7%99%BE%E8%A4%B6%E8%A3%99.jpg', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E5%A4%A7%E5%9C%B0%E7%BA%A2%E4%BA%94%E5%BD%A9%E5%A4%A7%E8%9F%92%E7%BC%8E.png', description: '古代器物展示' },
                
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E5%A4%A7%E7%BA%A2%E5%9C%B0%E7%BB%87%E5%8B%BE%E8%BF%9E%E7%89%A1%E4%B8%B9%E7%9B%B4%E5%BE%84%E7%BA%B1.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E5%A4%A7%E7%BA%A2%E7%BB%87%E9%87%91%E5%AF%B8%E8%9F%92%E5%A6%86%E8%8A%B1%E7%BC%8E.png', description: '古代器物展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E6%9C%88%E7%99%BD%E5%9C%B0%E7%BB%87%E6%AD%A3%E5%8D%8D%E5%AD%97%E7%BB%87%E9%93%B6%E7%BC%8E.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E6%9D%8F%E9%BB%84%E8%89%B2%E7%BC%8E%E6%89%93%E7%B1%BD%E7%BB%A3%E9%B9%A4%E9%B9%BF%E8%8A%B1%E8%9D%B6%E7%BA%B9%E9%A9%AC%E9%9D%A2%E8%A3%99.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E6%B9%96%E8%89%B2%E5%9C%B0%E6%A2%85%E7%AB%B9%E7%BA%B9%E6%8F%90%E8%8A%B1%E7%BD%97%E5%A5%B3%E5%8D%95%E8%A1%AB.png', description: '古代器物展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E6%B9%96%E8%89%B2%E6%8F%90%E8%8A%B1%E7%BD%97%E9%95%B6%E9%BB%91%E8%BE%B9%E5%BD%A9%E8%89%B2%E6%89%93%E7%B1%BD%E8%8A%B1%E5%8D%89%E7%BD%97%E8%A3%99.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E7%81%B0%E8%93%9D%E8%89%B2%E6%9A%97%E8%8A%B1%E7%BB%B8%E6%89%93%E7%B1%BD%E7%BB%A3%E5%A5%B3%E9%A9%AC%E7%94%B2.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E7%99%BD%E8%89%B2%E5%9B%A2%E9%B9%A4%E5%9B%9B%E5%90%88%E5%A6%82%E6%84%8F%E7%BA%B9%E5%AE%81%E7%BB%B8%E5%B8%B8%E6%9C%8D%E8%A2%8D.png', description: '古代器物展示' },

                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E7%9F%B3%E9%9D%92%E7%BC%8E%E5%9C%B0%E4%BA%8C%E9%BE%99%E6%88%8F%E7%8F%A0%E5%8F%8C%E5%87%A4%E5%9B%A2%E8%8A%B1%E7%BA%B9%E7%BB%87%E9%87%91%E9%94%A6%E5%AF%B9%E8%A5%9F%E5%A5%B3%E8%A4%82%E6%AD%A3%E8%A7%86%E5%9B%BE.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E7%B4%AB%E5%9C%B0%E7%BB%87%E9%93%B6%E5%BD%A9%E5%8D%B0%E8%8A%B1%E5%8D%89%E7%BC%8E%E6%97%A0%E8%A2%96%E5%A4%B9%E6%97%97%E8%A2%8D.png', description: '古代器物展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E7%B4%AB%E8%A4%90%E7%BC%8E%E5%9C%B0%E6%B5%B7%E6%B0%B4%E5%9B%A2%E9%B8%9F%E7%BA%B9%E5%BD%A9%E7%BB%A3%E5%A5%B3%E7%A4%BC%E6%9C%8D.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E7%BA%A2%E5%9C%B0%E5%8A%A0%E9%87%91%E9%BE%99%E5%87%A4%E5%A6%86%E8%8A%B1%E7%BC%8E%E8%A3%99%E6%AD%A3%E8%A7%86%E5%9B%BE.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E8%93%9D%E5%9C%B0%E5%9B%A2%E9%BE%99%E7%BA%B9%E5%88%BA%E7%BB%A3%E7%BB%B2%E8%BE%B9%E5%A5%B3%E8%A4%82.png', description: '古代器物展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E6%B9%96%E8%89%B2%E6%8F%90%E8%8A%B1%E7%BD%97%E9%95%B6%E9%BB%91%E8%BE%B9%E5%BD%A9%E8%89%B2%E6%89%93%E7%B1%BD%E8%8A%B1%E5%8D%89%E7%BD%97%E8%A3%99.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E8%93%9D%E8%89%B2%E6%9A%97%E5%85%AB%E4%BB%99%E7%BA%B9%E6%BC%B3%E7%BC%8E%E8%A2%84.png', description: '古代铜盘展示' },
                { url: 'http://39.107.97.152:9016/silk_picture/%E4%B8%9D%E7%BB%B8%E5%8D%9A%E7%89%A9%E9%A6%86%E8%97%8F%E6%B8%85%E4%BB%A3%E6%9C%8D%E9%A5%B07%E4%BB%B6/%E9%93%B6%E7%B4%AB%E8%89%B2%E5%AE%9E%E5%9C%B0%E8%8A%B1%E7%BD%97%E7%94%B7%E9%95%BF%E8%A1%AB.png', description: '古代器物展示' },
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
          window.location.href = 'http://10.156.8.21:8894/files/html/newSystem.html?_ijt=u4vfe8ujgs48lgu2be2qvbafsb'; 
        },
        goToExternalPage() {
          window.location.href = 'http://10.156.8.21:8899/files/html/newSystem_classification.html';
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
    bottom: 40px; /* 调整按钮距离底部的距离 */
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
    bottom: 40px; /* 调整按钮距离底部的距离 */
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
    bottom: 40px; /* 调整按钮距离底部的距离 */
    left: 20%;
    transform: translateX(-50%);

  }

  .pagination-button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
</style>