<template>
  <div id="app">
    <CardGenerator/>
  </div>
</template>

<script>
import CardGenerator from './components/CardGenerator.vue'

export default {
  components: {
    CardGenerator
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 20px;
}
</style>
