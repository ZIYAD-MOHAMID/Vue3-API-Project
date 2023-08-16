<template>
  <main>
    <div class="h">
      <div class="info">
        <h1>Sorry , the page not found</h1>
        <p>The link you followed probably broken or the has been removd</p>
      </div>
      <div class="img">
        <img
          src="../placeholders/μην-βριαλμένη-σελίδων-λάθος-404-σπασμένο-συρμένο-χέρι-διανυσματικό-πρότυπο-ρομπότ-84451115.webp"
          alt=""
        />
      </div>
      <RouterLink to="/">GO Back To Home</RouterLink>
    </div>
  </main>
</template>

<script setup>
import { RouterLink } from 'vue-router'
</script>

<style scoped lang="scss">
.h {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  padding: 50px;
  height: 80vh;
  .info {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    h1 {
      font-weight: bold;
      color: #192a51;
    }
    p {
      font-size: 20px;
      align-items: center;
    }
  }
  .img {
    img {
      width: 500px;
      border: 1px solid #192a51;
      border-radius: 10px;
    }
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
</style>
