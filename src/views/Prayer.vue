<template>
    <ion-page class="prayer-page-container">
        <navbar back-route="home" heading="Daily Prayer" />
        <img class="background" src="/images/prayer-background.jpg" alt="prayer background image">
        <div class="inner-container">
            <transition name="fade" mode="out-in">
                <div class="prayer-container" v-if="!isLoading">
                    <div v-if="engPrayer && engPrayer.prayerText">
                        <h2>{{ engPrayer.prayerDate }} (ENG)</h2>
                        <p>{{ engPrayer.prayerText }}</p>
                    </div>
                    <div v-if="afrPrayer && afrPrayer.prayerText">
                        <h2>{{ afrPrayer.prayerDate }} (AFR)</h2>
                        <p>{{ afrPrayer.prayerText }}</p>
                    </div>
                    <div v-if="afrPrayerError && engPrayerError" class="error-container">
                        <h3>
                            Oops! Something went wrong: {{ afrPrayerError || engPrayerError }}.
                        </h3>
                        <ion-button class="button" @click="getDailyPrayer()">Retry</ion-button>
                    </div>
                    <h3 v-else-if="!engPrayer && !afrPrayer" class="error-container">
                        There are no available prayers for today, come back tomorrow!
                    </h3>
                </div>
                <div v-else class="loading-container">
                    <ion-spinner class="loading-spinner" name="lines"></ion-spinner>
                </div>
            </transition>
        </div>
    </ion-page>
</template>

<script setup lang="ts">
import { IonPage, useIonRouter, IonSpinner, IonButton } from '@ionic/vue';
import { onMounted } from 'vue'
import axios from 'axios'
import { load } from "cheerio";
import { ref } from 'vue';
import Navbar from '@/components/Navbar.vue';

interface Prayer {
    prayerEndTime?: number,
    prayerDate: String,
    prayerStartTime?: number,
    prayerText: string,
}

let engPrayer = ref<Prayer>()
let afrPrayer = ref<Prayer>()
let engPrayerError = ref<string>('')
let afrPrayerError = ref<string>('')
let isLoading = ref<boolean>(true)

onMounted(async () => {
    await getDailyPrayer()
})

const getDailyPrayer = async () => {
    isLoading.value = true

    const currentDate = new Date()
    const date = currentDate.getDate() < 10 ? `0${currentDate.getDate()}` : currentDate.getDate()
    const month = currentDate.getMonth() + 1 < 10 ? `0${currentDate.getMonth() + 1}` : currentDate.getMonth() + 1
    const year = currentDate.getFullYear()
    const engQuery = `pray_${date}-${month}-${year}/`
    const afrQuery = `bid_${date}-${month}-${year}/`

    try {
        const engRes = await axios.get(`https://febaradio.co.za/events/event/${engQuery}`)
        if (engRes.status === 200)
            engPrayer.value = scrapePrayerData(engRes.data)
        engPrayerError.value = ''
    } catch (err: any) {
        engPrayerError.value = err.message
        console.error(err)
    }
    
    try {
        const afrRes = await axios.get(`https://febaradio.co.za/events/event/${afrQuery}`)
        if (afrRes.status === 200)
            afrPrayer.value = scrapePrayerData(afrRes.data)
        afrPrayerError.value = ''
    } catch (err: any) {
        afrPrayerError.value = err.message
        console.error(err)
    }

    isLoading.value = false
}

const scrapePrayerData = (data: string) => {
    const $ = load(data);

    const prayerText = $('.page-content > p').text()
    let prayerStartDate = $('.page-content ul li:first-of-type time:first-of-type').text()
    let prayerEndDate = $('.page-content ul li:first-of-type time:nth-of-type(2)').text()
    
    return {
        prayerText,
        prayerDate: prayerEndDate ? `${prayerStartDate} - ${prayerEndDate}` : prayerStartDate,
        prayerStartTime: prayerStartDate ? new Date(prayerStartDate).getHours() : 0,
        prayerEndTime: prayerEndDate ? new Date(prayerEndDate).getHours() : 0
    }
}
</script>

<style lang="css" scoped>
.prayer-page-container {
    padding: 1rem;
    background-color: #131124;
}

.prayer-page-container .background {
    width: 100%;
    height: 100%;
    position: fixed;
    z-index: 0;
    top: 0;
    left: 0;
    object-fit: cover;
    filter: blur(2px) brightness(0.8);
}

.prayer-page-container .inner-container {
    overflow-y: auto;
    position: relative;
    z-index: 1;
    display: flex;
    flex-direction: column;
    max-width: 500px;
    margin: 0 auto;
    margin-top: 60px;
    width: 100%;
    height: 100%;
}

.prayer-page-container .prayer-container {
    transition: all 500ms ease;
    color: #fff;
}

.prayer-container div {
    filter: drop-shadow(1px 2px 1px black);
}

.prayer-container div:nth-child(2) {
    margin-top: 2rem;
}

.prayer-container h2 {
    margin: 0 0 1rem 0;
}

.prayer-container p {
    margin: 0;
    font-size: 1.2rem;
    line-height: 1.2;
}

.error-container {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.loading-container {
    transition: all 500ms ease;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    font-size: 1.5rem;
    font-weight: bold;
    color: #fff;
}

.button {
    color: #003f58;
    width: 60%;
    max-width: 150px;
    white-space: pre-wrap;
    margin: 0.5rem 0;
    height: 2.8rem;
    font-size: 1.1rem;
    margin-top: 1rem;
}

ion-button {
    --background: #e0e0e0d5;
    --color: #000000;
    --border-radius: 100vw;
    --box-shadow: 0 2px 6px 0 #0000004d;
    --ripple-color: #0011ff;
}

.fade-enter-active,
.fade-leave-active {
    opacity: 1;
}

.fade-enter-from {
    transform: translateY(2rem);
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}
</style>
