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
    
  </main>
</template>
