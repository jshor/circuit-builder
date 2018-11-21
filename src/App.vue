<template>
  <div id="app">
    <Builder />
  </div>
</template>

<script>
import Builder from './components/Builder.vue'

export default {
  name: 'app',
  components: {
    Builder
  }
}
</script>

<style>
body, html {
  height: 100%;
  padding: 0;
  margin: 0;
}

#app {
  display: flex;
  width: 100%;
  height: 100%;
  justify-content: center;
  align-items: center;  
}
</style>