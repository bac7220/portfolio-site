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
  <main>
    <div v-if="work">
      <h1>{{ work.work_title }}</h1>
      <div style="width: 100%; aspect-ratio: 16/ 9; overflow: hidden; border-radius: 10px">
        <img
          :src="work.work_thumbnail?.url"
          :alt="work.work_title"
          style="width: 100%; height: auto"
        />
      </div>
      <p>{{ work.work_description }}</p>
      <div v-html="work.work_body"></div>
    </div>
  </main>
</template>

