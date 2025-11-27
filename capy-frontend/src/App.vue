<script setup>
import { ref } from 'vue'

const abc = ref(3)
</script>
<template>
  <div id="app">
    <router-view />
  </div>
</template>
<style scoped>
#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
</style>
