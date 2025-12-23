<script setup>
import { onMounted } from 'vue';
import { gsap } from 'gsap';
import { routeLocationKey } from 'vue-router';

onMounted(() => {
  const tl = gsap.timeline();

  tl.to(".ball", {
    y: "50vh",
    duration: 2,
    ease: "bounce.out",
    stagger: 0.2
  })
    .to(".ball", {
      scale: 0,
      opacity: 0,
      duration: 0.3,
    }, "+=0.5")
    .to(".hero-curtain", {
      xPercent: 100,
      duration: 0.8,
      ease: "expo.inOut"
    })
    .to(".char", {
      opacity: 1,
      y: -20,
      duration: 1.5,
      stagger: 0.05,
      ease: "power3.out"
    }, "-=0.4");
  gsap.to(".bg-shape", {
    duration: (index) => 5 + index * 2,

    x: () =>Math.random() * 100 - 50,
    y: ()=> Math.random() * 50 - 30,
    rotate: 360,
    ease: "none",
    repeat: -1,
    yoyo: true,
    stagger: {
      each: 2,
      from: "random"
    },
    opacity: 0.5,
  });
});



</script>

<template>
  <section class="hero">
    <!-- ボールアニメーション -->
    <div class="ball ball-1"></div>
    <div class="ball ball-2"></div>
    <div class="ball ball-3"></div>
    <!-- 背景用 -->
    <div class="hero-bg">
      <div class="bg-shape shape-1"></div>
      <div class="bg-shape shape-2"></div>
      <div class="bg-shape shape-3"></div>
      <div class="bg-shape shape-4"></div>
    </div>
    <!-- スライドしてhero表示 -->
    <div class="hero-curtain"></div>
    <div class="hero-wrapper">
      <div class="hero__content">
        <h2 class="hero-name"><span v-for="(char, index) in 'OKUDA'" :kye="index" class="char">{{ char }}</span><br>
          <span v-for="(char, index) in 'MASAKI'" :key="index + 5" class="char">{{ char }}</span></br>
        </h2>
        <h3 class="hero-name">Portfolio</h3>
      </div>
    </div>

  </section>
</template>

<style scoped>
.hero {
  position: relative;
  overflow: hidden;
  height: 100svh;
}

.ball {
  position: absolute;
  top: -100px;
  left: 50%;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #fff;
  transform: translateX(-50%);
  z-index: 11;
}

.ball-2 {
  margin-left: -60px;
}

.ball-3 {
  margin-left: 60px;
}

.hero-bg {
  position: absolute;
  display: block;
  background-color: #000;
  width: 100vw;
  height: 100%;
  z-index: -1;
}

.bg-shape {
  position: absolute;
  background-color: rgba(255, 255, 255, 0.413);
  border-radius: 50%;
  aspect-ratio: 1 / 1;
  height: auto;
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

.hero-curtain {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #333;
  z-index: 10;
}

.hero-wrapper {
  position: absolute;
  display: flex;
  top: 50%;
  transform: translateY(-50%);
}


.hero-content {}


.char {
  display: inline-block;
  opacity: 0;
}

.hero-name {
  color: #FFF;
  font-family: Inter;
  font-size: 120px;
  font-style: normal;
  font-weight: 700;
  line-height: normal;
}
</style>