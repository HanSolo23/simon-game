<template>
  <div id="app">
    <SimonGame/>
  </div>
</template>

<script>
import SimonGame from './components/SimonGame.vue'

export default {
  name: 'App',
  components: {
    SimonGame
  }
}
</script>

<style>
body {
  background-color: rgba(145, 219, 197, 0.404);
}
</style>
