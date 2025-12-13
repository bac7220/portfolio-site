<script setup>
import TheWelcome from '../components/TheWelcome.vue';
import { client } from '../lib/microcms.js';
import { ref, onMounted } from 'vue';

const works = ref([]);
onMounted(async () => {
  const data = await client.get({ endpoint: 'works' });
  works.value = data.contents;
})
</script>

<template>
  <main>
    <TheWelcome />
    <div v-for="item in works" :key="item.id">
      <h2>
        <RouterLink :to="`/works/${item.work_slug}`">
        {{ item.work_title }}
      </RouterLink>
      </h2>
      <p>{{ item.work_description }}</p>
    </div>
  </main>
</template>
