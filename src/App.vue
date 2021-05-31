<template>
  <scene />
</template>

<script>
import Scene from './components/Scene.vue'

export default {
  name: 'App',

  components: {
    Scene
  }
}
</script>

<style>
body {
  margin: 0;
}

html, body {
  height: 100vh;
  background: rgb(24, 24, 24);
}
</style>
