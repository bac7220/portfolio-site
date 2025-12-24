<script setup>
import { ref, onMounted } from 'vue';
import { gsap } from 'gsap';

const shapes = ref(Array.from({ length: 4 }, () => ({})));


onMounted(() => {
  gsap.to(".bg-shape", {
    duration: (index) => 5 + index * 2,
    x: () => Math.random() * 100 - 50,
    y: () => Math.random() * 50 - 30,
    ease: "none",
    repeat: -1,
    yoyo: true,
    stagger: {
      each: 2,
      from: "random",
    },
    opacity: 0.5,
  })
})


// 背景シェイプが動くアニメーション
gsap.to(".bg-shape", {
  duration: (index) => 5 + index * 2,
  x: () => Math.random() * 100 - 50,
  y: () => Math.random() * 50 - 30,
  ease: "none",
  repeat: -1,
  yoyo: true,
  stagger: {
    each: 2,
    from: "random"
  },
  opacity: 0.5,
});


</script>

<template>

  <!-- 背景用 -->
  <div v-for="(shape, index) in shapes" :key="index" class="shape-wrapper"
    :style="{ position: 'absolute', top: 0, left: 0, width: '100%', height: '100%' }">
    <div :class="`shape-${index + 1}`" class="bg-shape" @mouseenter="handleBounce"></div>
  </div>

</template>

<style scoped>

.bg-shape {
  position: absolute;
  background-color: rgba(255, 255, 255, 0.413);
  border-radius: 50%;
  aspect-ratio: 1 / 1;
  height: auto;
  /* cursor: pointer; */
  z-index: 3;
}


.shape-1 {
  width: 100px;
  left: calc(50% - 800px);
  bottom: 300px;
}

.shape-2 {
  width: 150px;
  left: calc(50% - 300px);
  bottom: 650px;
}

.shape-3 {
  width: 50px;
  left: calc(50% + 100px);
  top: 20px;
}

.shape-4 {
  width: 120px;
  left: calc(50% + 220px);
  top: 600px;
}
</style>