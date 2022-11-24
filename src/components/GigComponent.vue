<template>
    <div v-if="userAuthenticated">
        <p class="bindToTop">Welcome back, <span style="color: rgb(229, 157, 22)" v-if="user.isExec"><b>[EXEC]
                </b></span> <span v-if="user.FirstName == `Ethan` && user.LastName == `Ross`">Lower than General Member </span>{{ user.FirstName }}</p>
        <h1 class="upcomingEvents">Upcoming Events:</h1>
        <!-- <button v-if="user.isExec" @click="this.execToolsEnabled = !this.execToolsEnabled">TOGGLE EXEC TOOLS</button> -->
        <div v-if="this.gigs.length > 0" class="container">
            <div class="gig" v-for="gig in this.gigs" :key="gig._id">
                <div class="dropdownOuterBox" v-if="user.isExec" @click="openDropDown(gig._id)" @mouseleave="closeDropDown">
                    <svg class="dropdownMenu" viewBox="-95 -95 700 700">
                        <circle cx="256" cy="256" r="48"></circle>
                        <path
                            d="M470.39 300l-.47-.38-31.56-24.75a16.11 16.11 0 01-6.1-13.33v-11.56a16 16 0 016.11-13.22L469.92 212l.47-.38a26.68 26.68 0 005.9-34.06l-42.71-73.9a1.59 1.59 0 01-.13-.22A26.86 26.86 0 00401 92.14l-.35.13-37.1 14.93a15.94 15.94 0 01-14.47-1.29q-4.92-3.1-10-5.86a15.94 15.94 0 01-8.19-11.82l-5.59-39.59-.12-.72A27.22 27.22 0 00298.76 26h-85.52a26.92 26.92 0 00-26.45 22.39l-.09.56-5.57 39.67a16 16 0 01-8.13 11.82 175.21 175.21 0 00-10 5.82 15.92 15.92 0 01-14.43 1.27l-37.13-15-.35-.14a26.87 26.87 0 00-32.48 11.34l-.13.22-42.77 73.95a26.71 26.71 0 005.9 34.1l.47.38 31.56 24.75a16.11 16.11 0 016.1 13.33v11.56a16 16 0 01-6.11 13.22L42.08 300l-.47.38a26.68 26.68 0 00-5.9 34.06l42.71 73.9a1.59 1.59 0 01.13.22 26.86 26.86 0 0032.45 11.3l.35-.13 37.07-14.93a15.94 15.94 0 0114.47 1.29q4.92 3.11 10 5.86a15.94 15.94 0 018.19 11.82l5.56 39.59.12.72A27.22 27.22 0 00213.24 486h85.52a26.92 26.92 0 0026.45-22.39l.09-.56 5.57-39.67a16 16 0 018.18-11.82c3.42-1.84 6.76-3.79 10-5.82a15.92 15.92 0 0114.43-1.27l37.13 14.95.35.14a26.85 26.85 0 0032.48-11.34 2.53 2.53 0 01.13-.22l42.71-73.89a26.7 26.7 0 00-5.89-34.11zm-134.48-40.24a80 80 0 11-83.66-83.67 80.21 80.21 0 0183.66 83.67z">
                        </path>
                    </svg>
                    <div class="dropdown" v-if="dropDownOpen == gig._id">
                        <p @click="getOrganizerContactInfo(gig)" class="dropdownItem">View Contact Info</p>
                        <p class="dropdownItem" @click="manageMembers(gig._id)">Manage Available Members</p>
                        <p class="dropdownItem" @click="toggleHomepageVisibility(gig._id)"><span v-if="!gig.showOnHomepage">Show</span><span v-else>Hide</span> on homepage</p>
                        <p class="dropdownItem" style="color: rgb(255, 91, 91);" @click="confirmDelete(gig._id)">Delete</p>
                    </div>
                </div>
                <h1 class="gigName">{{ gig.gigName }}</h1>
                <h3 class="organizationName">By: {{ gig.organizationName }}</h3>
                <h4 class="dateRange">📅 {{ dateRange(gig.gigStartDate, gig.gigEndDate) }}</h4>
                <p class="gigLocation">📌 <b>Location:</b> {{ gig.gigLocation }}</p>
                <p v-if="user.isExec" class="paidJob">💵 <b>Paid Job:</b> <span v-if="gig.paidJob">Yes</span><span
                        v-else>No</span></p>
                <p class="employeesNeeded">👥 <b>Members Needed:</b> {{ employeesNeeded(gig.employeesNeeded) }}</p>
                <h3 v-if="gig.additionalInformation != 'No additional details specified.'"
                    class="additionalInformation">Additional Info:</h3>
                <p v-if="gig.additionalInformation != 'No additional details specified.'" class="additionalInformationText">
                    {{ gig.additionalInformation }}</p>
                <p v-if="gig.registeredByOrganizer == false" class="registeredByOrganizer">Registered by AFC Exec.
                    (Information may be inaccurate)</p>
                <div
                    v-if="employeesAvailable(people, gig._id) != `There was an issue finding the availabilities for this event.`">
                    <button @click="this.socket.emit('available', user, gig._id)" class="availableButton">AVAILABLE? CLICK HERE</button>
                    <p class="employeesAvailable"><span v-if="employeesAvailable(people, gig._id).length > 0">{{ employeesAvailable(people, gig._id).length }}</span><span v-else>No</span> member<span
                            v-if="employeesAvailable(people, gig._id).length > 1 || employeesAvailable(people, gig._id).length == 0">s
                            are</span><span v-else> is</span> marked as available.</p>
                </div>
            </div>
        </div>
        <div v-else>
            <h1>There are no upcoming events currently scheduled.</h1>
        </div>
        <ManageMembersModal :ModalOpenProp="managingMembersModalOpen" :AvailableMembers="managingMembers" :GigId="managingMembersGigId" @closemodal="closeManagingMembers" />
        <InfoModal :infoModalOpenProp="organizerContactInfo.modalOpen" :ContactEmail="organizerContactInfo.email" :RegisteredByOrganizer="organizerContactInfo.regByOrganizer" :ContactNumber="organizerContactInfo.number" :ContactName="organizerContactInfo.name" @closeinfomodal="closeOrganizerContactInfo" />
        <DeletionModal :deletionModalOpenProp="deleteConfirmation" :gigToDelete="markedForDeletion" @closedeletemodal="deleteConfirmation = false" @eliminateEvent="requestEventDeletion(markedForDeletion)" />
    </div>
</template>

<script>
import io from "socket.io-client";
import DeletionModal from "./DeletionModal.vue"
import InfoModal from "./InfoModal.vue"
import ManageMembersModal from "./ManageMembersModal.vue"

export default {
    name: 'GigComponent',
    components: {
        DeletionModal,
        InfoModal,
        ManageMembersModal
    },
    props: {
        userAuthenticated: Boolean,
        user: Object
    },
    data() {
        return {
            socket: {},
            gigs: {},
            people: {},
            pastgigs: {},
            markedForDeletion: "",
            execToolsEnabled: false,
            dropDownOpen: undefined,
            deleteConfirmation: false,
            organizerContactInfo: {},
            managingMembersModalOpen: false,
            managingMembers: {},
            managingMembersGigId: ""
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
            this.execToolsEnabled = false
            this.markedForDeletion = ""
        })
        this.socket.on("availability", data => {   
            this.saveAvailabilities(data)
        })
    },
    methods: {
        getOrganizerContactInfo(gig) {
            this.organizerContactInfo.name = gig.organizerName
            this.organizerContactInfo.email = gig.organizerContactEmail
            this.organizerContactInfo.number = gig.organizerContactNumber
            this.organizerContactInfo.regByOrganizer = gig.registeredByOrganizer
            this.organizerContactInfo.modalOpen = true
            console.log(this.people)
        },
        saveAvailabilities(avdata) {
            this.people = avdata
            console.log(this.people)
        },
        toggleHomepageVisibility(gigId) {
            this.socket.emit("toggleHomepageVisibility", gigId)
        },
        closeOrganizerContactInfo() {
            this.organizerContactInfo.name = undefined
            this.organizerContactInfo.email = undefined
            this.organizerContactInfo.number = undefined
            this.organizerContactInfo.modalOpen = false
        },   
        manageMembers(gigId) {
            this.socket.emit('getavailability', gigId)
            this.managingMembersModalOpen = true
            this.managingMembersGigId = gigId
            this.managingMembers = this.employeesAvailable(gigId)
        },
        closeManagingMembers() {
            this.managingMembersModalOpen = false;
            this.managingMembers = {}
        },
        confirmDelete(gigId) {
            this.markedForDeletion = this.dropDownOpen
            this.deleteConfirmation = gigId
        },
        requestEventDeletion(gigId) {
            console.log(gigId)
            this.socket.emit("deleteRequest", gigId)
        },
        openDropDown(gigId) {
            return this.dropDownOpen = gigId
        },
        closeDropDown() {
            return this.dropDownOpen = undefined
        },
        removeMember(gigId) {
            console.log(gigId)
            this.closeManagingMembers()
            this.manageMembers(gigId)
        }
    },
    computed: {
        employeesNeeded() {
            return function (amountSpecified) {
                if (amountSpecified > 0) {
                    return amountSpecified
                }
                return "As many as possible."
            }
        },
        trueOrFalse() {
            return function (value) {
                if (value) {
                    return `Paid Job?: <span class="yes">Yes</span>`
                }
                return `Paid Job?: <span class="no">No</span>`
            }
        },
        dateRange() {
            return function (start, end) {
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
            return function (people, gigId) {
                try {
                    let value = people.find(o => o.gigId === gigId)
                    if (value) {
                        let members = value.availableMembers
                        return members
                    }
                    return "There was an issue finding the availabilities for this event."
                } catch(e) {
                    return "There was an issue finding the availabilities for this event."
                }
            }
        }
    }

}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,300;0,400;0,600;1,800&display=swap');


@keyframes rotate-cog {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}

.gig {
    background-color: #31303080;
    box-shadow: .8rem .8rem 1.4rem #1a1a1a,
        -.2rem -.2rem 1.8rem #272727;
    backdrop-filter: blur(8px);
    color: #fff;
    float: left;
    word-wrap: break-word;
    border-radius: 10px;
    padding-top: 20px;
    padding-bottom: 0px;
    padding-left: 20px;
    padding-right: 20px;
    flex: 0 0 auto;
    height: fit-content;
    overflow: hidden;
    width: 500px;
    margin: 20px;
}

.yes {
    color: green;
}

.no {
    color: red;
}

.upcomingEvents {
    font-family: 'Poppins', sans-serif;
    font-size: 35px;
    margin-bottom: 5px;
}

.container {
    font-family: 'Poppins', sans-serif;
    display: flex;
    overflow-x: auto;
    flex-wrap: nowrap;
    padding-bottom: 40px;
    scroll-behavior: smooth;
}

.bindToTop {
    font-family: 'Poppins', sans-serif;
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
    position: relative;
}

.availableButton {
    background-color: rgb(229, 157, 22);
    padding: 7px;
    font-size: 20px;
    top: -20px;
    max-width: 80%;
    border: 5px 5px;
    border-color: rgb(229, 157, 22);
    border-radius: 5px;
    color: white;
    transition: all 200ms;
    font-weight: bold;
    font-family: 'Poppins', sans-serif;
    position: relative;
}

.deletebutton {
    background-color: rgb(255, 0, 0) !important;
    margin: 15px;
    border-color: rgb(255, 0, 0) !important
}

.canceldeletebutton {
    background-color: rgb(229, 157, 22) !important;
    border-color: rgb(229, 157, 22) !important;
}

button:hover {
    cursor: pointer;
    background-color: rgb(229, 157, 22, 0.9);
    transition: all 200ms;
}

.dropdown {
    background-color: #1f1f1fcc;
    overflow: hidden;
    box-shadow: .4rem .4rem .7rem #1a1a1a,
                -.1rem -.1rem .9rem #272727;
    z-index: 1;
    border-radius: 20px;
    position: absolute;
    top: 50px;
}

.dropdownMenu {
    transition: 200ms all;
    display: flex;
    position: absolute;
    margin: 0;
    top: 20px;
    fill: #e59d1675;
    background: #252525;
    border-radius: 50%;
    width: 50px;
    height: 50px;
    transition: all 200ms;
}

.dropdownMenu:hover {
    transition: 200ms all;
    cursor: pointer;
    fill: #e59d16dc;
    animation-name: rotate-cog;
    animation-duration: 1s;
    animation-play-state: forwards;
    animation-timing-function: cubic-bezier(.34, .46, .35, 1.1)
}

.dropdownItem {
    padding-left: 30px;
    padding-right: 10px;
    padding-top: 15px;
    padding-bottom: 15px;
    margin: 20px;
    margin-left: 0px;
    margin-top: 0;
    margin-bottom: 0;
    text-align: left;
    width: 100%;
    position: relative;
    z-index: 2;
}

.dropdownItem:hover {
    cursor: pointer;
    background-color: #1b1b1bcc;
    transition: 200ms all;
}

.gigName {
    font-weight: 600;
    position: relative;
    margin-left: 50px;
    margin-right: 50px;
    line-height: 1;;
}

.organizationName {
    position: relative;
    top: -15px;
    color: #c7c7c7;
}

.dateRange {
    position: relative;
    top: -10px;
}

.gigLocation {
    position: relative;
    top: -15px;
}

.paidJob {
    position: relative;
    top: -15px;
}

.employeesNeeded {
    position: relative;
    top: -15px;
}

.additionalInformation {
    position: relative;
    font-size: 25px;
    top: -15px;
}

.additionalInformationText {
    position: relative;
    top: -35px;
}

.registeredByOrganizer {
    position: relative;
    word-wrap: break-word;
    font-size: 12px;
    color: #c7c7c7;
    top: -40px;
}

.employeesAvailable {
    position: relative;
    top: -20px;
}

/* Width */
::-webkit-scrollbar {
  width: 10px;
}

/* Track */
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 5px #2c2c2c; 
  border-radius: 10px;
}
 
/* Handle */
::-webkit-scrollbar-thumb {
  background: #272727; 
  box-shadow: inset 0 0 5px #202020; 
  border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #202020; 
}
</style>
