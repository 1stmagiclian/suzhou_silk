<template>
  <div class="fullscreen-image">
    <transition name="fade">
      <img :src="currentImage" alt="Dynamic Image" v-if="showImage" />
    </transition>
    <transition name="fade-slide">
      <button @click="showVisualization" class="custom-button" v-if="showImage">苏州丝绸纹样可视化平台</button>
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
        this.$router.push('/home');
      }, 1000);
    }
  }
};
</script>

<style scoped>
.fullscreen-image {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: black;
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
  max-width: 100%;
  max-height: 100%;
}
.custom-button {
  position: absolute;
  top: 5%;
  right: 5%;
  /* background-color: transparent; */
  border: 2px solid #A08E7C;
  color: #A08E7C;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 18px;
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





