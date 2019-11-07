<template>
  <div id="app">
    <PhpmdViewer />
  </div>
</template>

<script>
import PhpmdViewer from './components/PhpmdViewer.vue'

export default {
  name: 'app',
  components: {
    PhpmdViewer
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
