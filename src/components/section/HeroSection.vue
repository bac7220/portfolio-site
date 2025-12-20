<script setup>

</script>

<template>
<section class="hero">
  <div class="hero-bg"></div>
  <div class="hero-wrapper">
    <div class="hero__content">
      <h2 class="hero-name">OKUDA<br>MASAKI</br></h2>
      <h3 class="hero-name">My Portfolio</h3>
    </div>
    <div class="hero__ui-shadow">
      <div class="ui-card"></div>
      <div class="ui-card"></div>
      <div class="ui-card"></div>
    </div>
  </div>
</section>
</template>

<style scoped>
.hero{
  position: relative;
}

.hero-bg {
  position: absolute;
  background-color: #000;
  width: 100vw;
  height: 100svh;
  z-index: -1;
}

.hero-wrapper{
  display: flex;
}

.hero__ui-shadow{
width: 50%;
height: 100%;
display: grid;
}

.hero-ui{
background-color: #fff;
width: 100%;
height: 100px;

}
.hero-content{

}

.hero-name{
  color: #FFF;
font-family: Inter;
font-size: 120px;
font-style: normal;
font-weight: 700;
line-height: normal;
}

</style>