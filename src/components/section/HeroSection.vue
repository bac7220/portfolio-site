<script setup>
import { ref, onMounted } from 'vue';
import { gsap } from 'gsap';
import { routeLocationKey } from 'vue-router';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
gsap.registerPlugin(ScrollTrigger)


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



  // スクロールアウトアニメーション
  gsap.to([".hero-name", ".shape-wrapper"], {
    scrollTrigger: {
      trigger: ".hero",
      start: "top top",
      end: "bottom top",
      scrub: 2,
      pin: true,
      pinSpacing: true,
      markers: true, // デバック用
    },
    y: 150,
    opacity: 0,
    ease: "none",
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
    <Shape />
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
    <div class="hero-video">

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
  background-color: var(--main-color);
  width: 100vw;
  height: 100%;
  z-index: -1;
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

.hero-video {
  position: absolute;
  right: 0;
  bottom: 10px;
  width: 45%;
  height: 400px;
  background-color: #aaa;
}
</style>