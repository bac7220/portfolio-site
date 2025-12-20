<script setup>
import { client } from "../lib/microcms.js";
import { ref, onMounted } from "vue";

const works = ref([]);
const show = ref(false);
onMounted(async () => {
  const data = await client.get({ endpoint: "works" });
  works.value = data.contents;
});
</script>

<template>

    <section class="work-list">
        <article v-for="item in works" :key="item.id" class="work-card">
              <div class="work-thumbnail">
                <img :src="item.work_thumbnail.url" :alt="item.work_title" />
              </div>
              <h2>
                <RouterLink :to="`/works/${item.work_slug}`">
                  {{ item.work_title }}
                </RouterLink>
              </h2>
              <p>{{ item.work_description }}</p>
        </article>
    </section>
</template>

<style scoped>
.work-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 24px;
  margin-top: 40px;
}
.work-card {
  background-color: #fff;
  padding: 16px;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.work-card h2 {
  font-size: 18px;
  font-weight: 700;
  margin-bottom: 12px;
}

.work-thumbnail {
  width: 100%;
  aspect-ratio: 16 / 9;
  overflow: hidden;
  margin-bottom: 16px;
}

.work-thumbnail img {
  width: 100%;
  height: auto;
  object-fit: cover;
}
</style>
