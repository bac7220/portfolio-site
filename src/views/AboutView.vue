<script setup>
import { client } from "../lib/microcms.js";
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

      const container = document.querySelector(".about-description");
      const scrollAmount = container.scrollWidth - window.innerWidth;

      console.log("全体の幅:", container.scrollWidth);
      console.log("画面の幅:", window.innerWidth);
      console.log("動かす距離:", scrollAmount);
      gsap.to(container, {
        x: () => -scrollAmount,
        ease: "none",
        scrollTrigger: {
          trigger: ".about-hero",
          start: "top top",
          end: () => `+=${container.offsetWidth}`,
          pin: true,
          scrub: 1.3,
          anticipatePin: 1,
          invalidateOnRefresh: true,
          // markers: true,
          onUpdate: (self) => console.log("進捗", self.progress)
        }
      });

      const allContainer = gsap.utils.toArray(".profile-container");
      const allCards = gsap.utils.toArray(".profile-card");



      tl = gsap.timeline({
        scrollTrigger: {
          trigger: ".profile-wrapper",
          start: "top top",
          end: "+=50000",
          scrub: true,
          pin: true,
          markers: true,
        }
      });

      // 最初のコンテナのアニメーション
      tl.to(allContainer[0], {
        x: "-100vw",
        y: "-100vh",
        duration: 5,
      });

      // 2番目以降のコンテナをループ処理でアニメーション化
      for (let i = 1; i < allContainer.length; i++) {
        const container = allContainer[i];
        // 左右交互に登場位置を設定 (奇数番目は右から、偶数番目は左から)
        const xPos = i % 2 !== 0 ? "100vw" : "-100vw";

        // 前のアニメーション(前のコンテナの退場)と同時に開始
        tl.from(container, {
          x: xPos,
          y: "100vh",
          duration: 3,
        }, "<")
          .to(container, {
            x: 0,
            y: 0,
            duration: 1, // 中央に留まる時間
          });

        // 最後のコンテナ以外は退場アニメーションを追加
        if (i < allContainer.length - 1) {
          tl.to(container, {
            x: xPos, // 登場したのと同じサイドへ退場
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
  <div class="about">
    <div class="about-hero">
      <h2 class="about-description">WEBに、ワクワクの体験を。
      </h2>
    </div>
    <div class="about-profile">

      <div v-if="profile" class="profile-wrapper">
        <!-- <pre>{{ profile }}</pre> -->
        <div class="profile-container">
          <div class="profile-card profile-animation">ばく
            <!-- <div class="profile-img"><img :src="profile?.profile_img.url" alt=""></div> -->
          </div>
        </div>
        <div class="profile-container">
          <div class="profile-card" v-html="profile?.profile_belief"></div>
        </div>
        <div class="profile-container">
          <div class="profile-card profile-animation-2" v-html="profile?.profile_intro"></div>
        </div>

        <div class="profile-container">
          <div class="profile-card">{{ profile?.profile_skill }}</div>
        </div>
        <div class="profile-container">
          <div class="profile-card">{{ profile?.profile_tools }}</div>
        </div>
        <div class="profile-container">
          <div class="profile-card" v-html="profile?.profile_history"></div>
        </div>
        <div class="profile-container">
          <div class="profile-card" v-html="profile?.profile_activity"></div>
        </div>
        <div class="profile-container">
          <div class="profile-card" v-html="profile?.profile_hobby"></div>
        </div>

      </div>
      <div v-else-if="isLoading" class="profile-loading">読み込み中...</div>
    </div>
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
  /* height: 100vh; */
}

.profile-container {
  background-color: #ccc;
}

.profile-card {
  position: relative;
  /* display: flex; */
  padding: 24px 48px;
  flex-shrink: 0;
  margin-right: 100px;
  border: 1px solid #ccc;
  /* max-width: 800px; */
  width: 600px;
}

.profile-card:nth-child(even) {
  /* transform: translateX(-200px); */
}

.profile-card:nth-child(odd) {
  /* transform: translateX(200px); */
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
