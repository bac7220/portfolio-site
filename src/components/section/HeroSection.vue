<script setup>

</script>

<template>
  <section class="hero">
    <div class="hero-bg">
    </div>
      <div class="hero-wrapper">
        <div class="hero__content">
          <h2 class="hero-name"><br>MASAKI</br></h2>
          <h3 class="hero-name">My Portfolio</h3>
        </div>
    </div>
  </section>
</template>

<style scoped>
.hero {
  position: relative;
  height: 100svh;
}

.hero-bg {
  position: absolute;
  display: block;
  background-color: #000;
  width: 100vw;
  height: 100%;
  z-index: -1;
}

.hero-wrapper {
  display: flex;
}


.hero-content {}

.hero-name {
  color: #FFF;
  font-family: Inter;
  font-size: 120px;
  font-style: normal;
  font-weight: 700;
  line-height: normal;
}
</style>