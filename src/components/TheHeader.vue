<script setup>
import { ref } from "vue";

const isOpen = ref(false);

</script>

<template>
  <header>
    <button @click="isOpen = !isOpen" class="drawer-btn">
      <span class="drawer-line"></span>
      <span class="drawer-line"></span>
    </button>

    <Transition name="fade">
      <div v-show="isOpen" class="overlay" @click="isOpen = false" />
    </Transition>
    <Transition name="drawer">
      <nav v-show="isOpen" class="drawer">
        <div class="drawer-inner">
          <button class="close" @click="isOpen = false">×</button>
          <RouterLink to="/">Home</RouterLink>
          <RouterLink to="/about">About</RouterLink>
        </div>
      </nav>
    </Transition>
  </header>
</template>

<style scoped>
.drawer-btn {
  position: fixed;
  top: 50px;
  right: 20px;
  width: 60px;
  display: flex;
  flex-direction: column;
  gap: 8px;
  z-index: 1;
  mix-blend-mode: difference;
  padding: 10px;
  transition: translate 0.3s ease;
  transition: gap 0.3s ease;
}

.drawer-btn:hover {
  gap: 12px;
  transition: all 0.3s ease;
}

.drawer-btn:hover .drawer-line:first-of-type {
   transform: translateY(-2px);
transition: transform 0.3s ease;
}

.drawer-line {
  width: 100%;
  height: 1px;
  background-color: #fff;
}

.drawer-line:nth-of-type(2) {
  width: 80%;

}

.overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.6);
  z-index: 1;
}

.drawer {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  max-width: 800px;
  width: 100%;
  height: fit-content;
  z-index: 2;
}

.drawer-inner {
  display: flex;
  flex-direction: column;
  border-radius: 16px;
  background-color: #fff;
  padding: 20% 10%;
}

.drawer-enter-from {
  opacity: 0;
}

.drawer-enter-to {
  opacity: 1;
}

.drawer-leave-from {
  opacity: 1;
}

.drawer-leave-to {
  opacity: 0;
}

.drawer-enter-active,
.drawer-leave-active {
  transition: opacity 0.3s ease;
}
</style>