<script setup>
import { client } from "../lib/microcms.js";
import { ref, onMounted, nextTick,  } from "vue";
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
    const cards = gsap.utils.toArray(".profile-card");
    cards.forEach((card, index) => {
      const isEven = index % 2 === 0;

      gsap.set(card, {
        alignSelf: isEven ? "flex-start" : "flex-end",
        x: isEven ? -100 : 100,
        opacity: 0,
      });
      gsap.to(card, {
        x: 0,
        opacity: 1,
        duration: 1,
        ease: "power2.out",
        scrollTrigger: {
          trigger: card,
          start: "top 80%",
          toggleActions: "play none none reverse"
        }
      });
    });

  } catch (error) {
    console.error("データの取得に失敗しました", error);
  } finally {
    isLoading.value = false;
  }
});

</script>

<template>
  <div class="about">
    <h2 class="about">This is an about page</h2>
    <div v-if="profile" class="profile-wrapper">
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
.profile-wrapper {
  display: flex;
  flex-direction: column;

}

.profile-card {
  border: 1px solid #ccc;
  max-width: 800px;
  width: 100%;
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
