<template>
  <div>
    <h1>Upcoming Gigs:</h1>
    <p>Sorted in order of soonest to latest</p>
        <div v-if="this.gigs.length > 0">
            <div class="gig" v-for="gig in this.gigs" :key="gig._id">
                <h1>{{ gig.gigName }}</h1>
                <h3>By: {{ gig.organizationName }}</h3>
                <p v-html="trueOrFalse(gig.paidJob)"></p>
                <p>Employees Needed: {{ employeesNeeded(gig.employeesNeeded) }}</p>
                <p>Additional Info:</p>
                <p>{{ gig.additionalInformation }}</p>
            </div>
        </div>
        <div v-else>
            <h1>There are no upcoming gigs currently scheduled.</h1>
        </div>
    <!-- <h1>Previous Gigs:</h1>
        <div ref="pastGigsDiv">
            <div class="gig" v-for="gig in this.pastgigs" :key="gig._id">
                <h1>{{ gig.gigName }}</h1>
                <h3>By: {{ gig.organizationName }}</h3>
                <p>Additional Info:</p>
                <p>{{ gig.additionalInformation }}</p>
            </div>
        </div> -->
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
    },
    computed: {
        employeesNeeded() {
            return function(amountSpecified) {
                if (amountSpecified > -1) {
                    return amountSpecified
                }
                return "As many as possible."
            }
        },
        trueOrFalse() {
            return function(value) {
                if (value) {
                    return `Paid Job?: <span class="yes">Yes</span>`
                }
                return `Paid Job?: <span class="no">No</span>`
            }
        }
    }

}
</script>

<style>
.gig {
    background-color: #313030;
    color: #fff;
    float: left;
    padding-top: 50px;
    border-radius: 10px;
    box-shadow: #000 5px 5px;
    padding-bottom: 50px;
    padding-left: 20px;
    padding-right: 20px;
    height: fit-content;
    width: 500px;
    margin: 20px;
}
.yes {
    color: green;
}
.no {
    color: red;
}
</style>