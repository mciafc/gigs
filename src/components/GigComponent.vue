<template>
  <div v-if="userAuthenticated">
    <p class="bindToTop">Welcome back, <span style="color: yellow" v-if="user.isExec"><b>[EXEC] </b></span> <span v-if="user.FirstName == `Ethan`">"Senior Executive Member" </span>{{ user.FirstName }}</p>
    <h1>Upcoming Events:</h1>
        <button v-if="user.isExec" @click="this.execToolsEnabled = !this.execToolsEnabled">TOGGLE EXEC TOOLS</button>
        <div v-if="this.gigs.length > 0" class="container">
            <div class="gig" v-for="gig in this.gigs" :key="gig._id">
                <h1>{{ gig.gigName }}</h1>
                <h3>By: {{ gig.organizationName }}</h3>
                <h4>📅{{ dateRange(gig.gigStartDate, gig.gigEndDate) }}</h4>
                <p>📍<b>Location:</b> {{ gig.gigLocation }}</p>
                <p v-html="trueOrFalse(gig.paidJob)" v-if="user.isExec && gig.paidJob"></p>
                <p>👥<b>Members Needed:</b> {{ employeesNeeded(gig.employeesNeeded) }}</p>
                <h3>Additional Info:</h3>
                <p>{{ gig.additionalInformation }}</p>
                <p v-if="gig.registeredByOrganizer == false">Registered by AFC Exec. (Information may be inaccurate)</p>
                <!-- <button @click="this.socket.emit('available', user, gig._id)">AVAILABLE? CLICK HERE</button>
                <p>{amount} marked available. (incl. {execs} exec<span>s</span>)</p> -->
                <div v-if="user.isExec && execToolsEnabled">
                    <h3>Exec Tools</h3>
                    <p><button @click="getOrganizerContactInfo(gig)">VIEW ORGANIZER CONTACT INFO</button></p>
                    <p><button>COPY EMAIL LIST OF MEMBERS MARKED AS AVAILABLE</button></p>
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
            availableEmployees: {},
            pastgigs: {},
            execToolsEnabled: false
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

<style scoped>
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
    flex: 0 0 auto;
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

.container {
    display: flex;
    overflow-x: auto;
    flex-wrap: nowrap;
}

.bindToTop {
    position: absolute;
    top: 0;
    margin-left: auto;
    margin-right: auto;
    left: 0;
    right: 0;
    text-align: center;
}

button {
    background-color: rgb(229, 157, 22);
    padding: 7px;
    border: 5px 5px;
    border-color: rgb(229, 157, 22);
    border-radius: 5px;
    color: white;
}

button:hover {
    background-color: rgb(229, 157, 22, 0.9);
    transition: all 200ms;
}
</style>