<template>
  <div class="not-found" >
    <h1>404</h1>
    <p>Sorry, the page you are looking for could not be found.</p>
    <router-link to="/">Go back to Home</router-link>
  </div>
</template>

<script setup lang="ts">
// No script needed for this simple component
</script>

<style scoped>
.not-found {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  text-align: center;
  background: url('~@/assets/404_photos/thousand.webp') no-repeat center center fixed; 
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover;
}

.not-found h1 {
  font-size: 3em;
  margin-bottom: 1em;
}

.not-found p {
  font-size: 1.5em;
  margin-bottom: 2em;
}

.not-found a {
  color: #409EFF;
  text-decoration: none;
}
</style>