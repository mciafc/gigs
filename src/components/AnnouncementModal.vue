<template>
    <div class="modal" ref="modal" v-if="announceModalOpenProp">
        <h1 class="header">Compose Announcement Email</h1>
        <p><input type="text" class="subjectLine" ref="emailSubject" v-model="emailSubject" :placeholder="`UPDATE: ${gig.gigName}`"></p>
        <textarea class="emailBody" ref="emailBody" v-model="emailBody" placeholder="Email body goes here..."></textarea>
        <p>{{emailStatus}}</p>
        <p><button @click="sendEmail" class="closeButton">DONE</button></p>
    </div>
    <div class="darkenbackground" ref="darkenbackground" v-if="announceModalOpenProp" @click="closeModalAnimation"
        :class="{ noscroll: announceModalOpenProp }"></div>
</template>


<script>
import io from "socket.io-client"

export default {
    name: 'AnnounceModal',
    props: {
        announceModalOpenProp: Boolean,
        gig: Object,
    },
    emits: ["closeannouncemodal"],
    data () {
        return {
            socket: {},
            emailSubject: "",
            emailBody: "",
            emailStatus: ""
        }
    },
    created() {
        this.socket = io("https://io.mciafc.com/gigs") 
    },
    mounted() {
        this.socket.on("emailSent", () => {
            this.emailStatus = "Sent!"
            setTimeout(this.closeModalAnimation, 1500)
        })
        this.socket.on("sendRequestReceived", () => {
            this.emailStatus = "Sending..."
        })
    },
    methods: {
        closeModalAnimation() {
            this.$refs.modal.classList.add('fade-out')
            this.$refs.darkenbackground.classList.add('unblur')
            setTimeout(this.closeModalEvent, 100)
        },
        closeModalEvent() {
            this.$emit('closeannouncemodal')
        },
        sendEmail() {
            console.log(this.emailSubject)
            console.log(this.emailBody)
            if (this.emailSubject.length == 0) {
                this.emailSubject = `UPDATE: ${this.gig.gigName}`
            }
            if (this.emailBody.length == 0) {
                return this.emailStatus = "Failed to send: Body cannot be empty."
            }
            this.socket.emit("sendEmail", this.emailSubject, this.emailBody)
        }
    },
}

</script>


<style scoped>
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

.subjectLine {
    background-color: #373737;
    border-radius: 10px;
    width: 90%;
    border: none;
    height: 30px;
    font-family: 'Poppins' sans-serif;
    transition: all 200ms;
    color: #fff;
    padding-left: 10px;
    padding-right: 5px;
}

.emailBody {
    resize: none;
    width: 90%;
    height: 225px;
    background-color: #373737;
    border: none;
    border-radius: 10px;
    color: #fff;
    padding-top: 10px;
    padding-left: 10px;
    padding-right: 5px;
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
    width: fit-content;
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
    margin-top: 5px;
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