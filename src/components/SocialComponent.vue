<template>
  <div class="social-container">
      <img class="logo" src="../assets/social/fb.svg" alt="FB logo">
      <img class="logo" src="../assets/social/yt.svg" alt="YT logo">
      <img class="logo" src="../assets/social/ig.svg" alt="IG logo">
  </div>
</template>

<script>
export default {
    name: 'SocialComponent'
}
</script>

<style scoped>
    .social-container {
        margin-top: 50px;
        width: 100%;
        display: flex;
        justify-content: space-evenly;
        align-items: center;
    }
    img {
        transition: all 0.2s;
        cursor: pointer;
    }
    img:hover {
        filter: invert(51%) sepia(7%) saturate(2860%) hue-rotate(101deg) brightness(118%) contrast(94%);
        transition: all 0.2s;
    }
</style>