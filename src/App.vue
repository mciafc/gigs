<template>
  <div class="authentication-modal" v-if="!authenticated" ref="authentication-modal">
    <h1>This site requires authentication.</h1>
    <h2>Please enter your AFC PIN Below.</h2>
  </div>
  <GigComponent :userAuthenticated="authenticated"></GigComponent>
</template>

<script>
import GigComponent from "./components/GigComponent.vue"

export default {
  name: 'App',
  components: {
    GigComponent
  },
  data() {
    return {
      authenticated: false
    }
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

.authentication-modal {
  position: absolute;
  z-index: 2;
  width: 100%;
  height: 100%;
  background-color: #1a1a1a;
  overflow: hidden;
  margin: 0;
  padding: 0;
}
</style>
