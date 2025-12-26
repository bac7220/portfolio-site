<script setup>

</script>

<template>
  <div class="profile-wrapper">
    <div class="profile-card">
      <div class="profile-image">
        <img src="../../assets/image/myprofile.webp" alt="">
      </div>
      <div class="profile-container">
        <h2 class="profile-name">奥田将輝 Okuda Masaki</h2>
        <div class="profile-text">新しいもの・ガジェット好き<br>
          独学でHTML/CSSから入り、WordPress構築をメインに活動中<br>
          Vue.js、GSAP、microCMS、AWSで当サイトを構築
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.profile-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  transform: translateY(100svh);
  z-index: 2;
  margin-block: 30px;
  background-color: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(10px);
}

.profile-card {
  box-shadow: 0 -20px 50px rgba(0, 0, 0, 0.2);
  background-color: #fff;
  border: 1px solid #333;
  max-width: 800px;
  width: fit-content;
  padding: 24px 32px;
  gap: 16px;
  display: flex;
  align-items: center;
  margin-inline: auto;
}

.profile-image {
  max-width: 200px;
  height: auto;
}
</style>
