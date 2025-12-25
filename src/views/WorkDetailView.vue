<script setup>
import { client } from "../lib/microcms.js";
import { ref, onMounted } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const work = ref(null);

onMounted(async () => {
  const slug = route.params.slug;
  const data = await client.get({
    endpoint: "works",
    queries: { filters: `work_slug[equals]${slug}` },
  });
  work.value = data.contents[0];
});
</script>

<template>
  <section v-if="work" class="detail">
    <div class="detail-inner section-inner">
      <h2 class="detail-title" v-text="work.work_title"></h2>
      <div class="detail-img" style="width: 100%; aspect-ratio: 16/ 9; overflow: hidden; border-radius: 10px">
        <img :src="work.work_thumbnail?.url" :alt="work.work_title" style="width: 100%; height: auto" />
      </div>
      <h3>{{ work.work_description }}</h3>
      <div v-html="work.work_body"></div>
      <p class="detail-text">{{ work.work_role }}</p>
      <p class="detail-text">{{ work.work_techstack }}</p>
      <p class="detail-text">{{ work.work_publishedAt }}</p>

      <div class="detail-btn"><a href="{{ work.work_url }}">サイトはコチラ</a></div>
    </div>
  </section>
</template>

<style scoped>

  
</style>