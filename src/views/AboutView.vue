<script setup>
import { client } from "../lib/microcms.js";
import { ref, onMounted, nextTick, } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const profile = ref(null);
const isLoading = ref(true);

onMounted(async () => {
  try {
    const data = await client.get({
      endpoint: "profile",
    });
    profile.value = data;
    await nextTick();
    const container = document.querySelector(".profile-wrapper");
    const cards = gsap.utils.toArray(".profile-card");

    const scrollAmount = (container.offsetWidth - window.innerWidth) + 100;

    console.log("全体の幅:", container.scrollWidth);
    console.log("画面の幅:", window.innerWidth);
    console.log("動かす距離:", scrollAmount);
    gsap.to(container, {
      x: -scrollAmount,
      ease: "none",
      scrollTrigger: {
        trigger: ".about",
        start: "top top",
        end: `+=${scrollAmount}`,
        pin: true,
        scrub: 1.3,
        anticipatePin: 1,
        markers: true,
        onUpdate: (self) => console.log("進捗", self.progress)
      }
    })

  } catch (error) {
    console.error("データの取得に失敗しました", error);
  }
});

</script>

<template>
  <div class="about">
    <div v-if="profile" class="profile-wrapper">
    <!-- <pre>{{ profile }}</pre> -->
      <div class="profile-card">{{ profile?.profile_name }}</div>
      <div class="profile-card" v-html="profile?.profile_intro"></div>
      <div class="profile-card">{{ profile?.profile_skill }}</div>
      <div class="profile-card">{{ profile?.profile_tools }}</div>
      <div class="profile-card" v-html="profile?.profile_history"></div>
      <div class="profile-card" v-html="profile?.profile_activity"></div>
      <div class="profile-card" v-html="profile?.profile_hobby"></div>
      <div class="profile-card" v-html="profile?.profile_belief"></div>
      <div class="profile-img"><img :src="profile?.profile_img.url" alt=""></div>
    </div>
    <div v-else-if="isLoading" class="profile-loading">now Loading...</div>
  </div>
</template>

<style scoped>
.about {
  height: 100svh;
  overflow: hidden;
  position: relative;
  margin-top: 100px;
}

.profile-wrapper {
  display: flex;
  /* flex-direction: column; */

  width: fit-content;
  gap: 100px;
  align-items: center;
  /* overflow: visible; */
  padding: 0 50px;

}

.profile-card {
  /* display: flex; */
  padding: 24px 48px;
  flex-shrink: 0;
  margin-right: 100px;
  border: 1px solid #ccc;
  /* max-width: 800px; */
  width: 600px;
}

.profile-card h3 {
  width: 32px;
  font-weight: 700;
}

.profile-img {
  max-width: 300px;
  height: auto;
  overflow: hidden;
}
</style>
