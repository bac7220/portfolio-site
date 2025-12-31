<script setup>
import AboutArrow from "../icons/AboutArrow.vue";
import ArrowLeft from "../icons/ArrowLeft.vue";
import ArrowRight from "../icons/ArrowRight.vue";

import { client } from "../../lib/microcms.js";
import { ref, computed, onMounted, nextTick, onUnmounted } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const profile = ref(null);
const isLoading = ref(true);

let ctx;
let tl;

const profileItems = computed(() => {
  if (!profile.value) return [];

  return [
    { content: profile.value.profile_intro, arrowSide: 'left' },
    { content: profile.value.profile_belief, arrowSide: 'right' },
    { content: profile.value.profile_skill, arrowSide: 'right' },
    { content: profile.value.profile_tools, arrowSide: 'right' },
    { content: profile.value.profile_history, arrowSide: 'left' },
    { content: profile.value.profile_activity, arrowSide: 'right' },
    { content: profile.value.profile_hobby },
  ]
})

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
          end: "+=20000",
          scrub: 1.5,
          pin: true,
          // markers: true,
        }
      });
      const firstArrow = allContainer[0].querySelector(".card-arrow");
      if (firstArrow) {
        tl.fromTo(firstArrow, { opacity: 0 }, { opacity: 1, duration: 0.5 }, "<")
      }

      tl.from(allContainer[0], {
        y: "100vh",
        duration: 5,
        ease: "none",
      })
      tl.to(allContainer[0], {
        x: "-100vw",
        y: "-100vh",
        duration: 10,
      });
      let lastExitX = -100;


      for (let i = 1; i < allContainer.length; i++) {
        const container = allContainer[i];
        const itemData = profileItems.value[i - 1];

        const entryX = lastExitX = lastExitX * -1;
        const exitX = itemData.arrowSide === 'left' ? 100 : -100;

        const arrow = container.querySelector(".card-arrow");
        // プロフィールカードの動き制御
        tl.from(container, {
          x: entryX + "vw",
          y: "100vh",
          duration: 10,
        }, "<")
          .to(container, {
            x: 0,
            y: 0,
            duration: 1,

          });
        if (arrow) {
          tl.fromTo(arrow, { opacity: 0 }, { opacity: 1, duration: 0.5 }, "<");
        }

        if (i < allContainer.length - 1) {
          tl.to(container, {
            x: exitX + "vw",
            y: "-100vh",
            duration: 10,
          });
        }
        lastExitX = exitX;
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

  <div class="about-profile">

    <div v-if="profile" class="profile-wrapper">
      <!-- <pre>{{ profile }}</pre> -->
      <div class="profile-container ">
        <div class="profile-card row-container">
          <div class="card-content">
            <h3 class="profile-label">名前</h3>
            <p>{{ profile?.profile_name }}</p>
            <img v-if="profile?.profile_img" class="profile-img" :src="profile?.profile_img?.url" alt="">
          </div>
          <div class="card-content">
            <h3 class="profile-label">Webでの活動名</h3>
            <p>ばく</p>
            <img src="../../assets/image/BacIcon.webp" alt="" class="profile-img">
          </div>
        </div>
        <div class="card-content">
        </div>
        <div class="card-arrow">
          <AboutArrow>
            <template #text>NEXT</template>
            <template #after>
              <ArrowRight />
            </template>
          </AboutArrow>
        </div>
      </div>

      <div v-for="(item, index) in profileItems" :key="index" class="profile-container">
        <div class="profile-card" v-html="item?.content"></div>
        <div v-if="index < profileItems.length - 1" class="card-arrow">
          <AboutArrow>
            <template v-if="item.arrowSide === 'left'" #before>
              <ArrowLeft />
            </template>
            <template #text>next</template>
            <template v-if="item.arrowSide === 'right'" #after>
              <ArrowRight />
            </template>
          </AboutArrow>
        </div>
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

.row-container {
  display: flex;
  flex-direction: row;
  gap: 32px;
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
  flex-direction: column;
  ;
}

.profile-card h3 {
  font-weight: 700;
}


.profile-img {
  position: absolute;
  max-width: 300px;
  height: auto;
  overflow: hidden;
  opacity: 0;
  z-index: -1;
}

.card-arrow {
  opacity: 0;
}
</style>