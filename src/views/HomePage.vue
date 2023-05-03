<template>
    <ion-page>
        <section class="home-page-container">
            <div class="wave" />
            <div class="logo-container">
                <img class="feba-logo" src="/images/feba_logo.png">
            </div>
            <div class="main-buttons">
                <ion-button router-link="/live-radio" router-direction="forward" class="button">Live Radio</ion-button>
                <ion-button router-link="/prayer" router-direction="forward" class="button">Daily Prayer</ion-button>
                <ion-button
                    @click="openBrowser('https://febaradio.co.za/stories/')"
                    class="button"
                >
                    Stories
                </ion-button>
                <ion-button
                    @click="openBrowser('https://febaradio.co.za/about-us/')"
                    class="button"
                >
                    About Us
                </ion-button>
                <ion-button
                    @click="openBrowser('https://febaradio.co.za/contact-us/')"
                    class="button"
                >
                    Contact Us
                </ion-button>
            </div>
            <button
                @click="openBrowser('https://febaradio.co.za/donate/')"
                class="donate-button ion-activatable ripple-parent"
            >
                <ion-ripple-effect class="custom-ripple"></ion-ripple-effect>
                <span>Donate</span>
                <ion-icon :icon="heart" size="large"></ion-icon>
            </button>
        </section>
    </ion-page>
</template>

<script setup lang="ts">
import { IonButton, IonPage, IonRippleEffect, IonIcon } from '@ionic/vue';
import { InAppBrowser } from '@ionic-native/in-app-browser';
import { heart } from 'ionicons/icons';
import { onMounted, ref, watch } from 'vue';
import { useRoute } from 'vue-router';

const openBrowser = (url: string) => {
    InAppBrowser.create(url)
}

const route = useRoute()

watch(route, (route) => {
    if (route.path !== '/home') {
        clearInterval(intervalId.value)
    } else {
        const wave = document.querySelector<HTMLElement>('.wave')
        if (wave) {
            startAnimationInterval(wave)
            animate(wave)
        }
    }
})

const startAnimationInterval = (wave: HTMLElement) => {
    if (intervalId.value) clearInterval(intervalId.value)
    if (!wave) return
    intervalId.value = setInterval(() => {
        animate(wave)
    }, 2500)
}

const animate = (wave: HTMLElement) => {
    const randXPos = Math.floor(Math.random() * window.innerWidth)
    const randYPos = Math.floor(Math.random() * window.innerHeight)

    wave.style.top = `${randYPos}px`
    wave.style.left = `${randXPos}px`

    wave.animate({
        opacity: [1, 1, 0],
        width: ['0', '400px'],
        height: ['0', '400px'],
    },
    {
        duration: 2500,
        easing: 'ease-out'
    })
}

let intervalId = ref<any>()

onMounted(() => {
    const wave = document.querySelector<HTMLElement>('.wave')
    if (wave) {
        startAnimationInterval(wave)
        animate(wave)
    }
})
</script>

<style lang="css" scoped>
.home-page-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
    overflow: auto;
    background: #0e1b2a url('/images/world_background.png') no-repeat fixed left / cover;
    height: 100%;
    position: relative;
}

.logo-container {
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    z-index: 2;
    width: 100%;
    background: linear-gradient(
        #e0e0e0c4,
        #e0e0e0a8,
        #e0e0e09d,
        #00000000
    );
}

.logo-container img {
    filter: drop-shadow(1px 1px 1px #000000b3);
}

.feba-logo {
    width: 100%;
    max-width: 400px;
    padding: 0.5rem 0;
    padding-bottom: 1.5rem;
}

.home-page-container .button {
    color: #fff;
    width: 60%;
    max-width: 350px;
    white-space: pre-wrap;
    margin: 0.5rem 0;
    height: 2.8rem;
    font-size: 1.1rem;
}

.main-buttons {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%;
    position: relative;
    z-index: 5;
    margin-top: 1rem;
}

.ripple-parent {
    position: relative;
    overflow: hidden;
}

.custom-ripple {
  color: #003f58;
}

.donate-button {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    position: fixed;
    z-index: 5;
    right: 0;
    bottom: 0;
    margin: 1.4rem 1.4rem;
    border-radius: 100vw;
    width: 7rem;
    height: 7rem;
    background: linear-gradient(120deg, #ff816d, #fe6047, #b13522);
    box-shadow: 0 0 10px #9c3b2ce1;
    font-size: 1rem;
    font-weight: bold;
    text-transform: uppercase;
    color: #e6e6e6;
}

.donate-button span {
    margin-bottom: 0.2rem;
}

ion-button {
    --background: #0c2666ea;
    --color: #000000;
    --border-radius: 0.4rem;
    --box-shadow: 0 2px 6px 0 #0000004d;
    --ripple-color: #0011ff;
}

.wave {
    position: fixed;
    transform: translate(-50%, -50%);
    z-index: 1;
    border: 3px solid #0c2566;
    border-radius: 100vw;
    opacity: 0;
}
</style>
