<script setup>
import { client } from "../lib/microcms.js";
import { ref, onMounted } from "vue";

const works = ref([]);
onMounted(async () => {
  const data = await client.get({
    endpoint: "works",
    queries: {
      limit: 100, // 14件なら余裕
    },

  });
  works.value = data.contents;
});
</script>

<template>

  <section class="work-list">
    <TransitionGroup name="fade" appear>
      <article v-for="(item, index) in works" :key="item.id" class="work-card" :style="{ '--delay': index }">
        <!-- <div class="work-card-inner"> -->
        <div class="work-thumbnail">
          <img :src="item.work_thumbnail.url" :alt="item.work_title" />
        </div>
        <h2>
          <RouterLink :to="`/works/${item.work_slug}`">
            {{ item.work_title }}
          </RouterLink>
        </h2>
        <p>{{ item.work_description }}</p>
        <!-- </div> -->
      </article>
    </TransitionGroup>
  </section>
</template>

<style scoped>
.fade-enter-from {
  opacity: 0;
  transform: translateY(50px);
}

.fade-enter-active {
  transition: opacity 1s ease, transform 1s ease;
  transition-delay: calc(0.2s * var(--delay));
}

.fade-enter-to {
  opacity: 1;
  transform: translateY(0);
}




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
