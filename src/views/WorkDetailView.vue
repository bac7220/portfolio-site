<script setup>
import { client } from "../lib/microcms.js";
import { ref, onMounted, nextTick, computed } from "vue";
import { useRoute } from "vue-router";
import { gsap } from 'gsap';

const route = useRoute();
const work = ref(null);

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
      })
  })
})

// カルーセルの実装
const props = defineProps({
  items: Array,
  reverse: true,
  speed: {
    type: Number,
    default:20,
  }
})

const track = ref(null);
let ctx;

const duplicatedItems = computed() => [...props.items, ...props.items];

onMounted(() => {
  gsap.to(".detail-gallery__first", {
    xPercent: 50,
    duration: 10,
    ease: "none",
    repeat:-1,
  })

}, track.value);

onMounted(() => {
  if (ctx) ctx.revert();
})
</script>

<template>

  <section v-if="work" class="detail">
    <div class="detail-inner section-inner">
      <div class="detail-wrapper">
        <div class="detail-img">
          <img :src="work.work_gallery_top?.url" :alt="work.work_title" style="width: 100%; height: auto" />
        </div>
        <div class="detail-title__box">
          <h2 class="detail-title" v-text="work.work_title"></h2>
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
            <h3 class="detail-text__type">対応時期</h3>
            <p class="detail-text">{{ work.work_publishedAt }}</p>
          </div>
        </div>
      </div>
      <div class="detail-gallery" v-if="work && work.work_gallery_pc && work.work_gallery_sp">
        <div class="detail-gallery__first">
          <div class="gallery-img">
            <img :src="work.work_gallery_pc[0]?.url" alt="">
          </div>
          <div class="gallery-img">
            <img :src="work.work_gallery_pc[1]?.url" alt="">
          </div>
          <div class="gallery-img">
            <img :src="work.work_gallery_sp[0]?.url" alt="">
          </div>
        </div>
        <div class="detail-gallery__second">
          <div class="gallery-img">
            <img :src="work.work_gallery_pc[2]?.url" alt="">
          </div>
          <div class="gallery-img">
            <img :src="work.work_gallery_sp[1]?.url" alt="">
          </div>
          <div class="gallery-img">
            <img :src="work.work_gallery_pc[3]?.url" alt="">
          </div>
        </div>
        <div class="detail-gallery__third">
          <div class="gallery-img">
            <img :src="work.work_gallery_sp[2]?.url" alt="">
          </div>
          <div class="gallery-img">
            <img :src="work.work_gallery_pc[4]?.url" alt="">
          </div>
          <div class="gallery-img">
            <img :src="work.work_gallery_pc[5]?.url" alt="">
          </div>
        </div>
      </div>
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

.detail-gallery {}

.gallery-img img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.detail-gallery__first,
.detail-gallery__second,
.detail-gallery__third {
  display: grid;
  grid-template-columns: repeat(3, auto);
  grid-template-rows: 250px;
}


.gallery-img {
  width: 100%;
  height: 100%;
  padding: 20px;
  box-shadow: 0 0 4px 0.5px #333;
}
</style>