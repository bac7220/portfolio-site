<script setup>
import { client } from "../../lib/microcms.js";
import { ref, onMounted, computed, watch, nextTick } from "vue";
const works = ref([]);
const selectedTag = ref('all');
const visibleIds = ref(new Set());
const observer = ref(null);
const isFiltering = ref(false)


const filteredWorks = computed(() => {
  if (selectedTag.value === 'all') {
    return works.value
  }
  return works.value.filter((item) =>
    item.work_tags.includes(selectedTag.value)
  )
});


const changeTag = (work_tags) => {
  isFiltering.value = true
  selectedTag.value = work_tags
  setTimeout(() => {
    isFiltering.value = false
  }, 0)
};
onMounted(async () => {
  const data = await client.get({
    endpoint: "works",
    queries: { limit: 10 },
  });
  works.value = data.contents;

  observer.value = new IntersectionObserver(
    (entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          visibleIds.value.add(entry.target.dataset.id);
          observer.value.unobserve(entry.target);
        }
      });
    },
    { threshold: 0.2 }
  );
  requestAnimationFrame(() => {
    document.querySelectorAll(".work-card").forEach(el => {
      observer.value.observe(el);
    });
  });

});

watch(selectedTag, async () => {
  if (!observer.value) return

  visibleIds.value.clear()

  await nextTick()

  document.querySelectorAll('.work-card').forEach(el => {
    observer.value.observe(el)
  })
});

</script>


<template>
  <section class="work">
    <h2 class="work-title">実績一覧</h2>

    <div class="tag-buttons" style="margin-bottom:20px;">
      <button :class="{ active: selectedTag === 'all' }" @click="changeTag('all')">ALL</button>
      <button :class="{ active: selectedTag === 'vue' }" @click="changeTag('vue')">vue</button>
      <button :class="{ active: selectedTag === 'LP' }" @click="changeTag('LP')">LP</button>
      <button :class="{ active: selectedTag === 'WordPress' }" @click="changeTag('WordPress')">WordPress</button>
    </div>

    <TransitionGroup name="fade" appear tag="section" class="work-list" :key="selectedTag">
      <article v-for="(item, index) in filteredWorks" :key="item.id" class="work-card"
        :style="{ transitionDelay: isFiltering ? '0ms' : `${index * 80}ms` }"
        :class="{ 'is-visible': visibleIds.has(item.id) }" :data-id="item.id">
        <RouterLink :to="`/works/${item.work_slug}`">
          <!-- <div class="work-card-inner"> -->
          <div class="work-thumbnail">
            <img :src="item.work_thumbnail.url" :alt="item.work_title" />
          </div>
          <h2>
            {{ item.work_title }}
          </h2>
          <p>{{ item.work_description }}</p>
          <div class="works-tag">
            <p>{{ item.work_tags }}</p>
          </div>
        </RouterLink>
      </article>
    </TransitionGroup>
  </section>
</template>

<style scoped>
body {
  background: #f7f8fa;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(50px);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 1s ease, transform 1s ease;
}

.fade-move {
  transition: transform 0.3s ease;
}

.fade-enter-to {
  opacity: 1;
  transform: translateY(0);
}

/* 消える時 */
.fade-leave-from {
  opacity: 1;
  transform: translateY(0);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}



.work-list {
  position: relative;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 32px;
  margin-top: 40px;
  padding: 40px;
}

.work-card {
  opacity: 0;
  transform: translateY(40px);
  transition: transform 0.3s ease, opacity 0.3s ease;
  background-color: #fff;
  padding: 16px;
  border-radius: 10px;
  box-shadow:
    0 5px 8px rgba(0, 0, 0, 0.5);
}

.work-card.is-visible {
  opacity: 1;
  transform: translateY(0);
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

button {
  background: #fff;
  border: 1px solid #ddd;
  color: #333;
  padding: 10px 18px;
  border-radius: 999px;
  font-size: 14px;
}

button.active {
  background: #111;
  color: #fff;
  border-color: #111;
}

.work-card {
  will-change: transform;
}

.tag-buttons {
  margin-top: 32px;
  display: flex;
  gap: 16px;
  justify-content: center;
}

.tag-buttons button {
  transition:
    transform 0.15ms ease,
    background 0.15ms ease,
    color 0.15ms ease,
}

.tag-buttons button:hover {
  transform: translateY(-1px)
}
</style>
