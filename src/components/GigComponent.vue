<template>
  <div v-if="userAuthenticated">
    <p>Welcome back, <span style="color: yellow" v-if="user.isExec"><b>[EXEC] </b></span> <span v-if="user.FirstName == `Ethan`">"Senior Executive Member" </span>{{ user.FirstName }}</p>
    <h1>Upcoming Gigs:</h1>
    <p>Sorted in order of soonest to latest</p>
        <div v-if="this.gigs.length > 0">
            <div class="gig" v-for="gig in this.gigs" :key="gig._id">
                <h1>{{ gig.gigName }}</h1>
                <h3>By: {{ gig.organizationName }}</h3>
                <h4>{{ dateRange(gig.gigStartDate, gig.gigEndDate) }}</h4>
                <p>Location: {{ gig.gigLocation }}</p>
                <p v-html="trueOrFalse(gig.paidJob)"></p>
                <p>Employees Needed: {{ employeesNeeded(gig.employeesNeeded) }}</p>
                <p>Additional Info:</p>
                <p>{{ gig.additionalInformation }}</p>
                <p v-if="gig.registeredByOrganizer == false">Registered by AFC Exec.</p>
                <button>AVAILABLE? CLICK HERE</button>
                <p>Amount of available employees goes here (not implemented yet)</p>
                <div>
                    <h3>Exec Tools</h3>
                    <button @click="getOrganizerContactInfo(gig)">VIEW ORGANIZER CONTACT INFO</button>
                </div>
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
    props: {
        userAuthenticated: Boolean,
        user: Object
    },
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
    methods: {
        getOrganizerContactInfo(gig) {
            if (gig.registeredByOrganizer == true) {
                return alert(`Organizer Name: ${gig.organizerName}\nOrganizer Email: ${gig.organizerContactEmail}\nOrganizer Phone Number: ${gig.organizerContactNumber}`)
            }
            alert(`WARNING: THIS INFORMATION MAY NOT BE ACCURATE AS THIS EVENT WAS REGISTERED BY AN AFC EXEC AND NOT THE ORGANIZER\nOrganizer Name: ${gig.organizerName}\nOrganizer Email: ${gig.organizerContactEmail}\nOrganizer Phone Number: ${gig.organizerContactNumber}`)
        }
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
        },
        dateRange() {
            return function(start, end) {
                let startString = new Date(start).toDateString()
                // let endString = new Date(end).toDateString()
                let startMinutes = new Date(start).getMinutes()
                let startHours = new Date(start).getHours()
                let endMinutes = new Date(end).getMinutes()
                let endHours = new Date(end).getHours()
                if (startMinutes < 10) {
                    startMinutes = `0${startMinutes}`
                }
                if (endMinutes < 10) {
                    endMinutes = `0${endMinutes}`
                }
                return `${startString} @ ${startHours}:${startMinutes} - ${endHours}:${endMinutes}`
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