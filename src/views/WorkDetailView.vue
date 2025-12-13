<script setup>
import { client } from '../lib/microcms.js';
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();
const work = ref(null);

onMounted(async () => {
  const slug = route.params.slug;
  const data = await client.get({
    endpoint: 'works',
    queries: { filters: `work_slug[equals]${slug}` }
  });
  work.value = data.contents[0];
})
</script>

<template>
  <main>
    <h1>{{ work?.work_title}}</h1>
  </main>
</template>