<template>
  <div v-if="userAuthenticated">
    <p class="bindToTop">Welcome back, <span style="color: rgb(229, 157, 22)" v-if="user.isExec"><b>[EXEC] </b></span> <span v-if="user.FirstName == `Ethan` && user.LastName == `Ross`">"Senior Executive Member" (but not really) </span>{{ user.FirstName }}</p>
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
                <h3 v-if="gig.additionalInformation != 'No additional details specified.'">Additional Info:</h3>
                <p v-if="gig.additionalInformation != 'No additional details specified.'">{{ gig.additionalInformation }}</p>
                <p v-if="gig.registeredByOrganizer == false">Registered by AFC Exec. (Information may be inaccurate)</p>
                <div v-if="employeesAvailable(gig._id)">
                    <button @click="this.socket.emit('available', user, gig._id)">AVAILABLE? CLICK HERE</button>
                    <p>{{ employeesAvailable(gig._id).length }} member<span v-if="employeesAvailable(gig._id).length > 1 || employeesAvailable(gig._id).length == 0">s are</span><span v-else> is</span> marked as available.</p>
                </div>
                <div v-if="user.isExec && execToolsEnabled">
                    <h3>Exec Tools</h3>
                    <p><button @click="getOrganizerContactInfo(gig)">VIEW ORGANIZER CONTACT INFO</button></p>
                    <p><button>MANAGE AVAILABLE MEMBERS</button></p>
                    <p><button class="deletebutton" @click="areYouSureYouWantToDelete()" v-if="!deleteConfirmation">DELETE EVENT</button></p>
                    <p><button v-if="deleteConfirmation" class="deletebutton" @click="requestEventDeletion(gig._id)">ARE YOU REALLY SURE YOU WANT TO DO THIS? YOU CANT GO BACK!!!!</button></p>
                    <p><button v-if="deleteConfirmation" class="canceldeletebutton" @click="deleteConfirmation = false">CANCEL DELETION</button></p>
                </div>
            </div>
        </div>
        <div v-else>
            <h1>There are no upcoming events currently scheduled.</h1>
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
            execToolsEnabled: false,
            deleteConfirmation: false,
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
        this.socket.on("deleteResponse", () => {
            this.deleteConfirmation = false
            this.execToolsEnabled = false
        })
        this.socket.on("availability", data => {
            this.availableEmployees = data
        })
    },
    methods: {
        getOrganizerContactInfo(gig) {
            if (gig.registeredByOrganizer == true) {
                return alert(`Organizer Name: ${gig.organizerName}\nOrganizer Email: ${gig.organizerContactEmail}\nOrganizer Phone Number: ${gig.organizerContactNumber}`)
            }
            alert(`WARNING: THIS INFORMATION MAY NOT BE ACCURATE AS THIS EVENT WAS REGISTERED BY AN AFC EXEC AND NOT THE ORGANIZER\nOrganizer Name: ${gig.organizerName}\nOrganizer Email: ${gig.organizerContactEmail}\nOrganizer Phone Number: ${gig.organizerContactNumber}`)
        },
        areYouSureYouWantToDelete() {
            this.deleteConfirmation = true
            alert("PLEASE MAKE SURE THIS IS ACTUALLY WHAT YOU ARE MEANING TO DO, IF YOU DELETE THE EVENT IT IS PERMANENTLY DELETED AND NOT RECOVERABLE. BE CAREFUL PLEASE")
        },
        requestEventDeletion(gigId) {
            console.log(gigId)
            this.socket.emit("deleteRequest", gigId)
        }
    },
    computed: {
        employeesNeeded() {
            return function(amountSpecified) {
                if (amountSpecified > 0) {
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
        },
        employeesAvailable() {
            return function(gigId) {
                let value = this.availableEmployees.find(o => o.gigId === gigId)
                if (value) {
                    let members = value.availableMembers
                    return members
                }
                return []
            }
        }
    }

}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,300;0,400;0,600;1,800&display=swap');
.gig {
    background-color: #31303080;
    box-shadow:.8rem .8rem 1.4rem #151515, 
               -.2rem -.2rem 1.8rem #272727;
    backdrop-filter: blur(8px);
    color: #fff;
    float: left;
    padding-top: 50px;
    border-radius: 10px;
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
    padding-top: 25px;
    padding-bottom: 25px;
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
    font-size: 20px;
    max-width: 80%;
    border: 5px 5px;
    border-color: rgb(229, 157, 22);
    border-radius: 5px;
    color: white;
    transition: all 200ms;
    font-weight: bold;
    font-family: 'Poppins', sans-serif;
}

.deletebutton {
    background-color: rgb(255, 0, 0) !important;
    border-color: rgb(255, 0, 0) !important
}

.canceldeletebutton {
    background-color: green !important;
    border-color: green !important;
}

button:hover {
    background-color: rgb(229, 157, 22, 0.9);
    transition: all 200ms;
}
</style>
