<template>
  <div>
        <div :for="gig in gigs" :key="gig._id">
            <h1>gig.gigName</h1>
            <h3>By: gig.organizationName</h3>
        </div>
  </div>
</template>

<script>
import io from "socket.io-client";
export default {
    name: 'GigComponent',
    data() {
        return {
            socket: {},
            gigs: {},
            pastgigs: {},
        }
    },
    created() {
        this.socket = io("https://io.mciafc.com/gigs")
    },
    mounted() {
        this.socket.on("upcominggigs", data => {
            this.gigs = data
        })
        this.socket.on("pastgigs", data => {
            this.pastgigs = data
        })
    }
}
</script>

<style>

</style>