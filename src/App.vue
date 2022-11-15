<template>
  <div class="authentication-modal" v-if="!authenticated" ref="authentication-modal">
    <h1>This site requires authentication.</h1>
    <h2>Please enter your AFC PIN Below.</h2>
    <input type="password" maxlength="4" oninput="this.value = this.value.replace(/[^0-9.]/g, '').replace(/(\..*)\./g, '$1');" class="pininput" placeholder="1234" ref="pinInput" @keydown.enter="sendAuthenticationAttempt(this.$refs.pinInput.value)">
  </div>
  <GigComponent :userAuthenticated="authenticated" :user="user"></GigComponent>
</template>

<script>
import GigComponent from "./components/GigComponent.vue"
import io from "socket.io-client";

export default {
  name: 'App',
  components: {
    GigComponent
  },
  data() {
    return {
      authenticated: false,
      user: {},
      socket: {}
    }
  },
  methods: {
    sendAuthenticationAttempt(pin) {
      this.socket.emit("authenticationAttempt", pin)
    }
  },
  created() {
    this.socket = io('https://io.mciafc.com/authentication')
  },
  mounted() {
    this.socket.on("successfulAuthentication", data => {
      this.user = data
      this.authenticated = true
      console.log("Successfully authenticated")
    })
    this.socket.on("failedAuthentication", () => {
      window.location.href = `https://mciafc.com`
    })
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,300;0,400;0,600;1,800&display=swap');

#app {
  font-family: "Poppins", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #FFF;
  margin-top: 60px;
}

body {
  background-image: linear-gradient(-45deg, #1a1a1a, #292929);
  height: 0;
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
  top: 0;
  left: 0;
}

.pininput {
  font-family: "Poppins", sans-serif;
  background-color: white;
  outline: none;
  border: none;
  width: 10%;
  resize: none;
  margin: auto;
  padding: .5rem 1rem;
  font-size: 1rem;
  border-radius: .5rem;
  margin-bottom: 1rem;
  transition: 200ms;
  text-align: center;
}

.pininput:focus {
  background-color: white;
  outline: none;
  border: none;
  width: 10%;
  transform: scale(1.1);
  transition: 200ms;
  margin: auto;
  padding: .5rem 1rem;
  font-size: 1rem;
  border-radius: .5rem;
  margin-bottom: 1rem;
  text-align: center;
}
</style>
