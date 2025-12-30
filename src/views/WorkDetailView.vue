<script setup>
import { client } from "../lib/microcms.js";
import { ref, onMounted, nextTick, computed, onUnmounted } from "vue";
import { useRoute } from "vue-router";
import { gsap } from 'gsap';

const route = useRoute();
const work = ref(null);

const track = ref(null);
let ctx;

const trackRefs = ref([]);
const setTrackRefs = (el, index) => {
  if (el) trackRefs.value[index] = el;
}

const getRowClass = (index) => {
  return `row-${index + 1}`;
}

const duplicatedItems = computed(() => [...props.items, ...props.items, ...props.items]);


onMounted(async () => {
  const slug = route.params.slug;
  const data = await client.get({
    endpoint: "works",
    queries: { filters: `work_slug[equals]${slug}` },
  });
  work.value = data.contents[0];

  await nextTick();

  setTimeout(() => {
    const tl = gsap.timeline({
      defaults: { ease: "power2.out", duration: 1 }
    });
    tl.to(".detail-title", {
      y: 0,
      opacity: 1,
      startAt: { y: 20 }
    })
      .to(".detail-img", {
        scale: 1.05,
        y: 0,
        opacity: 1,
        startAt: { y: 40 }
      })
      .to(".detail-text", {
        y: 0,
        opacity: 1,
        duration: 0.8,
        stagger: 0.3,
        startAt: { y: 20 }
      })
      .to(".detail-btn", {
        y: 0,
        opacity: 1,
        startAt: { y: 20 }
      }, "-=0.5");

    // カルーセル
    ctx = gsap.context(() => {
      trackRefs.value.forEach((trackEl, index) => {
        if (!trackEl) return;

        const isReverse = index === 1;
        const xStart = isReverse ? 0 : -33.33;
        const xEnd = isReverse ? -33.33 : 0;

        gsap.set(trackEl, { xPercent: xStart });
        gsap.to(trackEl, {
          xPercent: xEnd,
          duration: 50,
          ease: "none",
          repeat: -1,
        });
      });
    });
  });
});

// カルーセルの実装
const props = defineProps({
  items: Array,
  reverse: true,
  speed: {
    type: Number,
    default: 20,
  }
})

// 画像の取得
const rows = computed(() => {
  if (!work.value) return [];

  return [
    work.value.work_gallery_1 || [],
    work.value.work_gallery_2 || [],
    work.value.work_gallery_3 || [],
  ].map(rowItems => [...rowItems, ...rowItems, ...rowItems]);
})


onUnmounted(() => {
  if (ctx) ctx.revert();
})




</script>

<template>

  <section v-if="work" class="detail">
    <div class="detail-inner section-inner">
      <div class="detail-wrapper">
        <div class="detail-img">
          <img :src="work.work_gallery_top?.url" :alt="work.work_title" />
        </div>
        <div class="detail-title__box">
          <h2 class="detail-title">
            {{ work.work_title }} 様
          </h2>
          <div class="detail-btn"><a :href="work.work_url">サイトはコチラ</a></div>
        </div>
        <div class="detail-text__contents">
          <div class="detail-text__group">
            <h3 class="detail-text__type">概要</h3>
            <p class="detail-text" v-html="work.work_body"></p>
          </div>
          <div class="detail-text__group">
            <h3 class="detail-text__type">対応範囲</h3>
            <p class="detail-text">{{ work.work_role }}</p>
          </div>
          <div class="detail-text__group">
            <h3 class="detail-text__type">使用技術・ツール</h3>
            <p class="detail-text">{{ work.work_techstack }}</p>
          </div>
          <div class="detail-text__group">
            <h3 class="detail-text__type">サイトデザイン・ディレクション</h3>
            <a class="detail-text" :href="work.work_design_url">{{ work.work_design }} 様</a>
          </div>
          <div class="detail-text__group">
            <h3 class="detail-text__type">対応時期</h3>
            <p class="detail-text detail-link">{{ work.work_publishedAt }}</p>
          </div>
        </div>
      </div>
      <div class="detail-gallery" v-if="rows.length">
        <div v-for="(rowItems, index) in rows" :key="index" :class="['gallery-row', getRowClass(index)]">
          <div class="gallery-track" :ref="el => setTrackRefs(el, index)">
            <div v-for="(img, imgIndex) in rowItems" :key="imgIndex" class="gallery-img">
              <img :src="img.url" :alt="work.work_title">
            </div>
          </div>
        </div>
      </div>
      <h3>{{ work.work_description }}</h3>
      <div v-html="work.work_body"></div>
      <div><a href={{ work.work_url }}>サイトはコチラ</a></div>
    </div>
  </section>
</template>


<style scoped>
.detail-title,
.detail-img,
.detail-text,
.detail-btn {
  opacity: 0;
}

.detail {
  padding-block: 100px;
}


.detail-title__box {
  display: flex;
  gap: 42px;
  align-items: center;
  margin: 42px 0 0 32px;

}

.detail-title {
  font-family: Inter;
  font-size: 69.5px;
  font-style: normal;
  font-weight: 700;
  line-height: normal;
}

.detail-img {
  max-width: 1440px;
  width: 100%;
  height: 300px;
  overflow: hidden;
}

.detail-img img {
  object-fit: contain;
  object-position: center left;
}

.detail-text__contents {
  padding-left: 2vw;
  margin-left: auto;
  max-width: 1240px;
  margin-top: 100px;
  border-top: 1px #111 solid;
  display: flex;
  flex-direction: column;
  gap: 32px;
}

.detail-text__group:first-child {
  padding-top: 32px;
}

.detail-text {
  display: block;
  padding-block: 32px;
  font-size: 18px;
  line-height: 1.5;
  border-bottom: 1px #111 solid;
}


.detail-text__type {
  font-size: 24px;
  font-weight: bold;

}

.detail-btn a {
  padding: 16px 24px;
  background-color: #111;
  color: #fff;
}


.detail-gallery {
  width: 100vw;
  margin-top: 100px;
  overflow: hidden;


}

.gallery-row {
  height: 250px;
  overflow: hidden;
}


.gallery-track {
  display: flex;
  width: max-content;
  /* height: 100%; */
}

.gallery-img {
  display: flex;
  align-items: center;
  justify-content: center;
  width: clamp(400px, auto, 30vw);
  height: 250px;
  padding: 20px;
  border: solid 1px #ccc;
  flex-shrink: 0;
}

.gallery-img img {
  width: 100%;
  object-fit: contain;
  height: 100%;
}
</style>