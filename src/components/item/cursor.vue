<script setup>
import { ref, onMounted } from 'vue';
import { gsap } from 'gsap';
const cursor = ref(null);

// マウスポインタの座標を取得
const mousePos = ref({ x: 0, y: 0 });

// ポインタに追尾する円のアニメーション管理
const updateMousePos = (event) => {
  mousePos.value.x = event.clientX;
  mousePos.value.y = event.clientY;

  gsap.to(cursor.value, {
    x: mousePos.value.x,
    y: mousePos.value.y,
    opacity: 1,
    duration: 0.5,
    ease: "Power2.out",
    xPercent: -50,
    yPercent: -50,
  });
};
onMounted(() => {
  window.addEventListener('mousemove', updateMousePos)
});




</script>

<template>
  <div ref="cursor" class="cursor-dot"></div>
</template>



<style scoped>

.cursor-dot {
  opacity: 0;
  width: 30px;
  height: 30px;
  background-color: #fff;
  position: fixed;
  top: 0;
  left: 0;
  border-radius: 50%;
  z-index: 10;
  mix-blend-mode: difference;
  /* transform: translate(-50%, -50%); */
}
</style>