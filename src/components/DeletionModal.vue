<template>
<div class="modal" ref="modal" v-if="deletionModalOpenProp">
    <h1>HOLD UP!</h1>
    <p>Are you sure you want to delete this event? There's no going back.</p>
    <p><button class="deletebutton" @click="deleteEvent">YES, LET'S DELETE IT</button>
    <button @click="closeModalAnimation">NO, GO BACK</button></p>
</div>
<div class="darkenbackground" ref="darkenbackground" v-if="deletionModalOpenProp" @click="closeModalAnimation" :class="{ noscroll: deletionModalOpenProp }"></div>
</template>


<script>

export default {
    name: 'DeletionModal',
    props: {
        deletionModalOpenProp: Boolean,
        gigToDelete: String,
    },
    emits: ["closedeletemodal", "eliminateEvent"],
    methods: {
        closeModalAnimation() {
            this.$refs.modal.classList.add('fade-out')
            this.$refs.darkenbackground.classList.add('unblur')
            setTimeout(this.closeModalEvent, 100)
        },
        closeModalEvent() {
            this.$emit('closedeletemodal')
        },
        deleteEvent() {
            console.log(this.gigToDelete)
            this.$emit("eliminateEvent", this.gigToDelete)
            this.closeModalAnimation()
        }
    },
}

</script>

<style>
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
    height: 250px;
    width: 400px;
    background-color: #191919;
    font-size: 16px;
    z-index: 20000000;
    padding: 20px;
    padding-left: 75px;
    padding-right: 75px;
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

.unscrollable {
    overflow: hidden !important;
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