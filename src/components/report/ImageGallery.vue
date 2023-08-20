<template>
    <div class="image-gallery">
      <div v-for="(image, index) in images" :key="index" class="image-container">
        <img :src="image.url" :alt="'Image ' + (index + 1 + (currentPage - 1) * perPage)" class="image-item" />
        <p class="image-description">{{ image.description }}</p>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
        images: {
        type: Array,
        required: true,
        },
        currentPage: {
        type: Number,
        required: true,
        },
        perPage: {
        type: Number,
        required: true,
        },
    },
    computed: {
        visibleImages() {
        const startIndex = (this.currentPage - 1) * this.perPage;
        const endIndex = startIndex + this.perPage;
        return this.images.slice(startIndex, endIndex);
        },
    },
  };
  </script>
  
  <style>
  .image-gallery {
    margin-left: 100px;
    height: 650px;
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
  }
  
  .image-container {
    width: 250px; /* 调整这个值来控制图片容器的宽度 */
    height: 250px; /* 调整这个值来控制图片容器的高度 */
    text-align: center;
    flex-direction: column; /* 添加flex-direction属性 */
    align-items: center; /* 图片垂直居中对齐 */
    /*justify-content: center;  图片水平居中对齐 */
  }

  .image-item {
    max-width: 100%;
    height: 100%;
    object-fit: cover;
  }
  
  .image-description {
    margin-top: 5px;
    font-weight: bold;
    font-size: 14px;
    flex: 1; /* 新增这行来使描述占据剩余空间，避免被遮挡 */
  }
  </style>
  