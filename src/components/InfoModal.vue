<template>
    <div class="modal" ref="modal" v-if="infoModalOpenProp">
        <h1 class="header">ORGANIZER CONTACT INFO</h1>
        <p><b>Name</b>: {{ ContactName }}</p>
        <p><b>Email</b>: {{ ContactEmail }}</p>
        <p v-if="ContactNumber != `Not provided`"><b>Phone Number</b>: {{ ContactNumber }}</p>
        <p v-if="!RegisteredByOrganizer">Registered by AFC Exec. Potentially inaccurate information</p>
        <button @click="closeModalAnimation" class="closeButton">DONE</button>
    </div>
    <div class="darkenbackground" ref="darkenbackground" v-if="infoModalOpenProp" @click="closeModalAnimation"
        :class="{ noscroll: infoModalOpenProp }"></div>
</template>


<script>

export default {
    name: 'InfoModal',
    props: {
        infoModalOpenProp: Boolean,
        ContactName: String,
        ContactEmail: String,
        ContactNumber: String,
        RegisteredByOrganizer: Boolean,
    },
    emits: ["closeinfomodal"],
    methods: {
        closeModalAnimation() {
            this.$refs.modal.classList.add('fade-out')
            this.$refs.darkenbackground.classList.add('unblur')
            setTimeout(this.closeModalEvent, 100)
        },
        closeModalEvent() {
            this.$emit('closeinfomodal')
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