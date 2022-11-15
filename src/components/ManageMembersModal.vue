<template>
    <div class="modal" ref="modal" v-if="ModalOpenProp">
        <h1 class="header">MANAGE AVAILABLE MEMBERS</h1>
        <div class="container">
        <div v-if="AvailableMembersData == 'a'">
            <h1>Nobody is currently available.</h1>
        </div>
        <div class="gig" v-else v-for="member in AvailableMembersData" :key="member._id">
            <div class="dropdownOuterBox" @click="openDropDown(member._id)" @mouseleave="closeDropDown">
                <svg class="dropdownMenu" viewBox="-95 -95 700 700">
                    <circle cx="256" cy="256" r="48"></circle>
                    <path
                        d="M470.39 300l-.47-.38-31.56-24.75a16.11 16.11 0 01-6.1-13.33v-11.56a16 16 0 016.11-13.22L469.92 212l.47-.38a26.68 26.68 0 005.9-34.06l-42.71-73.9a1.59 1.59 0 01-.13-.22A26.86 26.86 0 00401 92.14l-.35.13-37.1 14.93a15.94 15.94 0 01-14.47-1.29q-4.92-3.1-10-5.86a15.94 15.94 0 01-8.19-11.82l-5.59-39.59-.12-.72A27.22 27.22 0 00298.76 26h-85.52a26.92 26.92 0 00-26.45 22.39l-.09.56-5.57 39.67a16 16 0 01-8.13 11.82 175.21 175.21 0 00-10 5.82 15.92 15.92 0 01-14.43 1.27l-37.13-15-.35-.14a26.87 26.87 0 00-32.48 11.34l-.13.22-42.77 73.95a26.71 26.71 0 005.9 34.1l.47.38 31.56 24.75a16.11 16.11 0 016.1 13.33v11.56a16 16 0 01-6.11 13.22L42.08 300l-.47.38a26.68 26.68 0 00-5.9 34.06l42.71 73.9a1.59 1.59 0 01.13.22 26.86 26.86 0 0032.45 11.3l.35-.13 37.07-14.93a15.94 15.94 0 0114.47 1.29q4.92 3.11 10 5.86a15.94 15.94 0 018.19 11.82l5.56 39.59.12.72A27.22 27.22 0 00213.24 486h85.52a26.92 26.92 0 0026.45-22.39l.09-.56 5.57-39.67a16 16 0 018.18-11.82c3.42-1.84 6.76-3.79 10-5.82a15.92 15.92 0 0114.43-1.27l37.13 14.95.35.14a26.85 26.85 0 0032.48-11.34 2.53 2.53 0 01.13-.22l42.71-73.89a26.7 26.7 0 00-5.89-34.11zm-134.48-40.24a80 80 0 11-83.66-83.67 80.21 80.21 0 0183.66 83.67z">
                    </path>
                </svg>
                <div class="dropdown" v-if="dropDownOpen == member._id">
                    <p class="dropdownItem" style="color: rgb(255, 91, 91);" @click="removeUser(member)">Remove</p>
                </div>
            </div>
            <h1 class="gigName"><span v-if="member.isExec" style="color: rgb(229, 157, 22); font-style: bold;">EXEC </span><span
                    v-if="member.FirstName == `Ethan` && member.LastName == `Ross`">"Pharaoh" </span>{{ member.FirstName }} {{
                member.LastName }}</h1>
            <p>{{ member.Email }}</p>
        </div>
        </div>
        <button @click="closeModalAnimation" class="closeButton">DONE</button>
    </div>
    <div class="darkenbackground" ref="darkenbackground" v-if="ModalOpenProp" @click="closeModalAnimation"></div>
</template>


<script>
import io from "socket.io-client"

export default {
    name: 'ManageMembersModal',
    props: {
        ModalOpenProp: Boolean,
        AvailableMembers: Object,
        GigId: String
    },
    emits: ["closemodal"],
    data() {
        return {
            dropDownOpen: "",
            socket: {},
            AvailableMembersData: "a"
        }
    },
    created() {
        this.socket = io("https://io.mciafc.com/gigs")
    },
    mounted() {
        this.socket.on("removeduser", data => {
            this.AvailableMembersData = data.availability
        })
        this.socket.on("availabilityData", data => {
            this.AvailableMembersData = data.availability
        })
    },
    methods: {
        closeModalAnimation() {
            this.$refs.modal.classList.add('fade-out')
            this.$refs.darkenbackground.classList.add('unblur')
            setTimeout(this.closeModalEvent, 100)
        },
        closeModalEvent() {
            this.$emit('closemodal')
        },
        openDropDown(gigId) {
            return this.dropDownOpen = gigId
        },
        logger() {
            console.log(this.AvailableMembersData)
        },
        closeDropDown() {
            return this.dropDownOpen = undefined
        },
        removeUser(user) {
            console.log(user)
            console.log(this.GigId)
            this.removing = true
            this.socket.emit("removeuser", user, this.GigId)
        }
    },
    computed: {
        employeesAvailable() {
            return function (gigId) {
                let value = this.AvailableMembers.find(o => o.gigId === gigId)
                if (value) {
                    let members = value.availableMembers
                    return members
                }
                return "There was an issue finding the availabilities for this event."
            }
        }
    }
}

</script>


<style scoped>
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
    padding-bottom: 20px;
    padding-left: 20px;
    padding-right: 20px;
    flex: 0 0 auto;
    max-width: 500px;
    height: fit-content;
    overflow: hidden;
    width: 500px;
    margin: 20px;
}

.container {
    font-family: 'Poppins', sans-serif;
    display: flex;
    overflow-x: auto;
    flex-wrap: nowrap;
    padding-bottom: 15px;
    scroll-behavior: smooth;
}

.dropdownMenu {
    transition: 200ms all;
    display: flex;
    position: absolute;
    margin: 0;
    fill: #e59d1675;
    background: #252525cc;
    border-radius: 50%;
    width: 50px;
    height: 50px;
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

@keyframes blur {
    0% {
        backdrop-filter: blur(0px);
    }

    100% {
        backdrop-filter: blur(8px);
    }
}

@keyframes fade-in {
    0% {
        opacity: 0;
    }

    100% {
        opacity: 1;
    }
}

@keyframes unblur {
    0% {
        backdrop-filter: blur(8px);
    }

    100% {
        backdrop-filter: blur(0px);
    }
}

@keyframes fade-out {
    0% {
        opacity: 1;
    }

    100% {
        opacity: 0;
    }
}

.fade-out {
    animation: fade-out 100ms forwards ease-out !important;
}

.unblur {
    animation: unblur 100ms forwards ease-out !important;
}

.modal {
    box-shadow: .8rem .8rem 1.4rem #151515,
        -.2rem -.2rem 1.8rem #272727;
    animation: fade-in 300ms forwards ease-out;
    position: absolute;
    margin: auto;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    text-align: center;
    height: fit-content;
    width: 1200px;
    background-color: #191919;
    font-size: 16px;
    z-index: 20000000;
    padding: 15px;
    padding-bottom: 25px;
    padding-left: 50px;
    padding-right: 50px;
    border-radius: 3rem;
    overflow: hidden;
}

.darkenbackground {
    z-index: 10000;
    width: 100%;
    height: 100%;
    background-color: #19191980;
    animation: blur 200ms forwards ease-out;
    opacity: 1;
    position: absolute;
    margin: 0;
    top: 0;
    left: 0;
}

.closeButton {
    margin-top: 20px;
}

.unscrollable {
    overflow: hidden !important;
}

.header {
    margin-bottom: -5px;
}

.continuebutton {
    position: absolute;
    margin: auto;
    left: 0;
    right: 0;
    bottom: 80px;
}

.pagenumber {
    position: absolute;
    margin: auto;
    left: 0;
    right: 0;
    bottom: 20px;
}

.fieldCheckText {
    position: absolute;
    margin: auto;
    left: 0;
    right: 0;
    bottom: 120px;
}
</style>