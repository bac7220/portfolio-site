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

  const imgs = profile.value.profile_bg_img;

  return [
    { content: profile.value.profile_intro, arrowSide: "left", img: imgs[0].url },
    { content: profile.value.profile_belief, arrowSide: "right", img: imgs[1].url },
    { content: profile.value.profile_skill, arrowSide: "right", img: imgs[2].url },
    { content: profile.value.profile_tools, arrowSide: "right", img: imgs[3].url },
    { content: profile.value.profile_history, arrowSide: "left", img: imgs[4].url },
    { content: profile.value.profile_activity, arrowSide: "right", img: imgs[5].url },
    { content: profile.value.profile_hobby },
  ];
});

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
        },
      });
      const firstArrow = allContainer[0].querySelector(".card-arrow");

      tl.from(
        allContainer[0],
        {
          y: "30vh",
          duration: 3,
          ease: "none",
        },
        0
      );
      tl.to(allContainer[0], {
        x: 0,
        y: "-5vh",
        duration: 3,
        ease: "none",
      });

      if (firstArrow) {
        tl.to(firstArrow, { opacity: 1, duration: 0.5 }, "-=2.5");
      }

      tl.to(allContainer[0], {
        x: "-100vw",
        y: "-100vh",
        duration: 8.5,
        ease: "none",
      });

      let lastExitX = -100;
      const allBgs = gsap.utils.toArray(".bg-image-item");

      for (let i = 1; i < allContainer.length; i++) {
        const container = allContainer[i];
        const targetBg = allBgs[i - 1];
        const prevBg = allBgs[i - 2];

        const itemData = profileItems.value[i - 1];
        const entryX = (lastExitX = lastExitX * -1);
        const exitX = itemData.arrowSide === "left" ? 100 : -100;
        const arrow = container.querySelector(".card-arrow");
        if (arrow) {
          gsap.set(arrow, { opacity: 0 });
        }
        // プロフィールカードの動き制御
        tl.fromTo(
          container,
          { x: entryX + "vw", y: "100vh" },
          { x: 0, y: "15vh", duration: 8.5, ease: "none" },
          "<"
        );
        if (targetBg) {
          tl.fromTo(
            targetBg,
            {
              opacity: 0,
              y: 40,
            },
            {
              opacity: 1,
              y: 0,
              duration: 8.5,
              ease: "power2.inOut",
            },
            "<"
          );
        }
        if (prevBg) {
          // 1つ前の背景をふわっと消す（クロスフェード）
          tl.fromTo(
            prevBg,
            {
              x: 0,
              y: 0,
              opacity: 1,
            },
            {
              opacity: 0,
              y: -40,
              x: entryX / 30 + "vw",
              duration: 8.5,
              ease: "power2.inOut",
            },
            "<"
          );
        }

        tl.to(container, {
          y: "-10vh",
          duration: 2.5,
          ease: "none",
        });

        if (i < allContainer.length - 1) {
          tl.to(container, {
            x: exitX + "vw",
            y: "-100vh",
            duration: 8.5,
            ease: "none",
          });
          if (arrow) {
            tl.to(arrow, { opacity: 1, duration: 0.5, ease: "none" }, "<");
          }
        }
        lastExitX = exitX;
      }
    });
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
      <div class="profile-bg">
        <!-- <img src="../../assets/image/about-a.jpg" alt=""> -->
      </div>
      <div class="profile-container">
        <div class="profile-card row-container">
          <div class="card-content" v-html="profile?.profile_name"></div>
        </div>
        <div class="card-content"></div>
        <div class="card-arrow">
          <AboutArrow>
            <template #text>NEXT</template>
            <template #after>
              <ArrowRight />
            </template>
          </AboutArrow>
        </div>
      </div>

      <div class="profile-img__wrapper">
        <div class="profile-bg-fixed">
          <div
            v-for="(item, index) in profileItems"
            :key="'bg-' + index"
            class="bg-image-item"
            :class="`${item?.arrowSide}`"
            :style="{ backgroundImage: `url(${item?.img})` }"
          ></div>
        </div>
      </div>

      <div v-for="(item, index) in profileItems" :key="index" class="profile-container">
        <div class="profile-card">
          <div class="card-content" v-html="item?.content"></div>
          <!-- <div class="card-bg-img"><img :src="item?.img" alt="" /></div> -->
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
    </div>
    <div v-else-if="isLoading" class="profile-loading">読み込み中...</div>
  </div>
</template>

<style scoped>
.about {
  overflow: hidden;
  width: 100%;
}

.profile-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 110vh;
  z-index: -1;
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
  /* transform: translate(-50%, -50%); */
  display: flex;
  flex-direction: column;
  align-items: center;
  will-change: transform;
}

.row-container {
  display: flex;
  flex-direction: row;
  gap: 32px;
}

.profile-card {
  /* max-width: 600px; */
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
  mix-blend-mode: difference;
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

/*　プロフィール画像のスタイル */
.profile-bg-fixed {
  position: absolute;
  top: 50%;
  left: 40%;
  transform: translate(-50%, -50%);
  max-width: 660px;
  width: 100%;
  height: auto;
  z-index: -1;
  /* overflow: hidden; */
  aspect-ratio: 660 / 430;
}

.bg-image-item {
  position: absolute;
  inset: 0;
  background-size: cover;
  background-position: center;
  opacity: 0;
  filter: brightness(0.6);
}
.bg-image-item.left {
  clip-path: polygon(25% 0%, 100% 0%, 75% 100%, 0% 100%);
}

.bg-image-item.right {
  clip-path: polygon(0 0, 75% 0, 100% 100%, 25% 100%);
}
</style>
