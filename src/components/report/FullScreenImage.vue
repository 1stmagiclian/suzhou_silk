<template>
  <div class="fullscreen-image">
    <transition name="fade">
      <img :src="currentImage" alt="Dynamic Image" v-if="showImage" />
    </transition>
    <transition name="fade-slide">
      <button @click="showVisualization" class="custom-button" v-if="showImage">苏州</button>
    </transition>
  </div>
</template>

<script>
export default {
  name: "Full",
  data() {
    return {
      currentImage: require('@/assets/images/silk.jpg'),
      showImage: false
    };
  },
  mounted() {
    // 延迟显示图片，实现动态展示效果
    setTimeout(() => {
      this.showImage = true;
    }, 1000);
  },
  methods: {
    showVisualization() {
      this.showImage = false;
      setTimeout(() => {
        this.$router.push('/show/%22%E8%8B%8F%E5%B7%9E%22');
      }, 0);
    }
  }
};
</script>

<style scoped>
.fullscreen-image {
  width: 100%;
  height: 100%;
  object-fit: fill;
  /* display: flex; */
  /* justify-content: center; */
  /* align-items: center; */
  /* background-color: black; */
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 1s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

.fade-slide-enter-active, .fade-slide-leave-active {
  transition: all 1s;
}

.fade-slide-enter, .fade-slide-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

.fullscreen-image img {
  width: 100%;
  height: 100%;
  object-fit: fill;
}
.custom-button {
  position: absolute;
  top: 28%;
  right: 10%;
  /* background-color: transparent; */
  border: 2px solid #A08E7C;
  color: #A08E7C;
  /* padding: 1px 2px; */
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 8px;
  border-radius: 5px;
  cursor: pointer;
  transition: color 0.3s, border-color 0.3s;
  font-family: "楷体", cursive;
}
.custom-button:hover {
  background-color: #A08E7C;
  color: white;
  border-color: #A08E7C;
}
</style>





