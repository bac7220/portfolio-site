<script setup>
import { RouterLink, RouterView } from "vue-router";
import TheHeader from "./components/TheHeader.vue";
import HeroSection from "./components/section/HeroSection.vue"
</script>

<template>
  <TheHeader />
  <main>
    <HeroSection />
    <RouterView />
  </main>
</template>

<style scoped></style>
