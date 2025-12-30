<script setup>
import { client } from "../../lib/microcms.js";
import { ref, onMounted, nextTick, onUnmounted } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const profile = ref(null);
const isLoading = ref(true);

let ctx;
let tl;

onMounted(async () => {
  try {
    const data = await client.get({
      endpoint: "profile",
    });
    profile.value = data;
    await nextTick();
    ctx = gsap.context(() => {

      const allContainer = gsap.utils.toArray(".profile-container");
      const allCards = gsap.utils.toArray(".profile-card");



      tl = gsap.timeline({
        scrollTrigger: {
          trigger: ".profile-wrapper",
          start: "top top",
          end: "+=50000",
          scrub: true,
          pin: true,
          // markers: true,
        }
      });
      const firstImg = allContainer[0].querySelector(".profile-img");
      if (firstImg) {
        gsap.set(firstImg, { opacity: 1 })
      }

      tl.to(allContainer[0], {
        x: "-100vw",
        y: "-100vh",
        duration: 3,
      });


      for (let i = 1; i < allContainer.length; i++) {
        const container = allContainer[i];
        const img = container.querySelector(".profile-img");
        const xPos = i % 2 !== 0 ? "100vw" : "-100vw";

        tl.from(container, {
          x: xPos,
          y: "100vh",
          duration: 2,
        }, "<")
          .to(container, {
            x: 0,
            y: 0,
            duration: 1,
          });
        if (img) {
          tl.fromTo(img, { opacity: 0 }, { opacity: 1, duration: 0.5 }, "<");
        }
        if (i < allContainer.length - 1) {
          if (img) {
            tl.to(img, { opacity: 0, duration: 0.5 });
          }
        }

        if (i < allContainer.length - 1) {
          tl.to(container, {
            x: xPos,
            y: "-100vh",
            duration: 4,
          });
        }
      }

    })
    ScrollTrigger.refresh();
  } catch (error) {
    console.error("データの取得に失敗しました", error);
  } finally {
    isLoading.value = false;
  }
});

onUnmounted(() => {
  if (ctx) {
    ctx.revert();
  }
});

</script>

<template>

  <!-- <img  :src="profile.profile_img?.url" alt=""> -->
  <div class="about-profile">

    <div v-if="profile" class="profile-wrapper">
      <!-- <pre>{{ profile }}</pre> -->
      <div class="profile-container">
        <div class="profile-card">
          <h3 class="profile-label">名前</h3>
          <div class="card-content">
            <p>{{ profile?.profile_name }}</p>
            <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">
          </div>
          <div class="card-content">
            <h3 class="profile-label">Webでの活動名</h3>
            <p>ばく</p>
            <img src="../../assets/image/BacIcon.webp" alt="">
          </div>
        </div>
      </div>
      <div class="profile-container">
        <div class="profile-card" v-html="profile?.profile_belief"></div>
        <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">

      </div>
      <div class="profile-container">
        <div class="profile-card" v-html="profile?.profile_intro"></div>
        <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">

      </div>

      <div class="profile-container">
        <div class="profile-card">{{ profile?.profile_skill }}</div>
        <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">

      </div>
      <div class="profile-container">
        <div class="profile-card">{{ profile?.profile_tools }}</div>
        <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">

      </div>
      <div class="profile-container">
        <div class="profile-card" v-html="profile?.profile_history"></div>
        <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">

      </div>
      <div class="profile-container">
        <div class="profile-card" v-html="profile?.profile_activity"></div>
        <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">

      </div>
      <div class="profile-container">
        <div class="profile-card" v-html="profile?.profile_hobby"></div>
        <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">

      </div>

    </div>
    <div v-else-if="isLoading" class="profile-loading">読み込み中...</div>
  </div>
</template>

<style scoped>
.about {
  overflow: hidden;
  width: 100%;
}




.profile-wrapper {
  position: relative;
  height: fit-content;
  padding: 8vh 50px;
  height: 100vh;
  overflow: hidden;
  /* background-image: url(../assets/image/about.webp); */

}

.profile-container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  flex-direction: column;
  align-items: center;
}

.profile-card {
  max-width: 600px;
  padding: 24px 48px;
  position: relative;
  /* display: flex; */
  flex-shrink: 0;
  /* max-width: 800px; */
  width: 100%;
}

.profile-card:nth-child(even) {
  /* transform: translateX(-200px); */
}

.profile-card:nth-child(odd) {
  /* transform: translateX(200px); */
}

.card-content {
  display: flex;
  
}

.profile-card h3 {
  width: 32px;
  font-weight: 700;
}


.profile-img {
  max-width: 300px;
  height: auto;
  overflow: hidden;
  opacity: 0;
}
</style>
