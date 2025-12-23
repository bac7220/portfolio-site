<script setup>
import { ref, onMounted } from 'vue';
import { gsap } from 'gsap';
import { routeLocationKey } from 'vue-router';

// shape画像の個数調整
const shapes = ref(Array.from({ length: 4 }, () => ({})));

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

    x: () => Math.random() * 100 - 50,
    y: () => Math.random() * 50 - 30,
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

// 背景のたまに触れたときのイベント
const handleBounce = (event) => {
  gsap.to(event.target, {
    scale: 1.2,
    duration: 0.8,
    ease: "elastic.out(1,0.5)",
    overwrite: true,
    onComplete: () => {
      gsap.to(event.target, {
        scale: 1,
        duration: 0.5,
        ease: "power2.out"
      });
    }
  });
};


</script>

<template>
  <section class="hero">
    <!-- ボールアニメーション -->
    <div class="ball ball-1"></div>
    <div class="ball ball-2"></div>
    <div class="ball ball-3"></div>
    <!-- 背景用 -->
    <div v-for="(shape, index) in shapes" :key="index" :class="`shape-${index + 1}`" class="bg-shape"
      @mouseenter="handleBounce"></div>
    <div class="hero-bg">


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
  z-index: 21;
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
  cursor: pointer;
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

.hero-curtain {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #333;
  z-index: 20;
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