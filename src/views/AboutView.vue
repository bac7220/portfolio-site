<script setup>
import { ref, onMounted, nextTick, onUnmounted } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import AboutSection from "@/components/section/AboutSection.vue";

gsap.registerPlugin(ScrollTrigger);

const containerRef = ref(null);
const isLoading = ref(true);
let ctx;

onMounted(async () => {
  try {
    await nextTick();
    ctx = gsap.context(() => {
      const container = containerRef.value;
      if (!container) return;

      const ScrollAmount = container.scrollWidth - window.innerWidth;

      gsap.to(container, {
        x: () => -ScrollAmount,
        ease: "none",
        scrollTrigger: {
          trigger: ".about-hero",
          start: "top top",
          end: () => `+=${ScrollAmount}`,
          pin: true,
          scrub: 1.3,
          invalidateOnRefresh: true,
          markers: true,
        },
      });
    });
  } catch (error) {
    console.error("エラーが発生しました。", error);
  } finally {
    isLoading.value = false;
  }
});
</script>

<template>
  <div class="about">
    <div class="about-hero">
      <h2 ref="containerRef" class="about-description">
        制作会社様のパートナーとして スピーディーで確実なコーディングを
      </h2>
    </div>
    <AboutSection />
  </div>
</template>

<style scoped>
.about {
  overflow: hidden;
  width: 100%;
}

.about-hero {
  height: 100vh;
  overflow: hidden;
  display: flex;
  align-items: center;
  width: 100%;
  background-color: var(--main-color);
  margin-right: 100px;
}

.about-description {
  font-size: 120px;
  color: #fff;
  white-space: nowrap;
  font-weight: 700;
  width: fit-content;
  padding: 0 100px;
  will-change: transform;
  letter-spacing: 10px;
}
</style>
