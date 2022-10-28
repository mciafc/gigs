<template>
  <GigComponent />
</template>

<script>
import GigComponent from "./components/GigComponent.vue"

export default {
  name: 'App',
  components: {
    GigComponent
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #FFF;
  margin-top: 60px;
}

body {
  background-color: #1a1a1a;
}

.footer {
  position: absolute;
  bottom: 5px;
  margin: auto;
  left: 0;
  right: 0;
}
</style>
