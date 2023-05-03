<template>
  <div class="tile" :class="{ 'playing-highlight': isPlaying }" @click="handleRadio()">
    <img :src="imageUrl" alt="country flag" />
    <div class="footer" :class="{ 'disabled': hasError }">
        <div class="inner">
            <span class="tile-text">
                <span>{{ country }}</span>
                <span>{{ stationName }}</span>
            </span>
            <transition name="fade" mode="out-in">
                <ion-spinner v-if="isLoading" class="loading-spinner" name="lines"></ion-spinner>
                <template v-else>
                    <ion-icon class="control-icon" v-if="isPlaying" :icon="pause"></ion-icon>
                    <ion-icon class="control-icon" v-else-if="!hasError" :icon="play"></ion-icon>
                    <ion-icon v-else-if="hasError" color="warning" :icon="alertCircle"></ion-icon>
                </template>
            </transition>
        </div>
    </div>
    <audio :class="`${country}-radio`" ref="audioEl" :src="currentAudioSrc"></audio>
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect, defineEmits } from "vue"
import { IonSpinner, IonIcon } from "@ionic/vue"
import { play, pause, alertCircle } from "ionicons/icons"

const props = defineProps<{
    country: string,
    stationName?: string,
    imageUrl: string,
    radioUrl: string,
}>()

const emit = defineEmits(['set-playing-radio'])

const audioEl = ref()
let isPlaying = ref<boolean>(false)
let isLoading = ref<boolean>(false)
let hasError = ref<boolean>(false)
let currentAudioSrc = ref<string>(props.radioUrl)

const handleRadio = (): void => {
    if (isLoading.value || hasError.value) return
    
    if (isPlaying.value) {
        audioEl.value?.pause()
        emit('set-playing-radio', { pausingRadio: `${props.country}-radio` })
    } else {
        audioEl.value?.play()
        emit('set-playing-radio', { playingRadio: `${props.country}-radio` })
    }
}

watchEffect(() => {
  if (audioEl.value) {
    audioEl.value.onloadstart = () => {
        isLoading.value = true
    }

    audioEl.value.onloadeddata = () => {
        isLoading.value = false
        hasError.value = false
    }

    audioEl.value.onplay = () => {
        isPlaying.value = true
        hasError.value = false
    }

    audioEl.value.onpause = () => {
        isPlaying.value = false
    }

    audioEl.value.onerror = () => {
        isLoading.value = false
        isPlaying.value = false
        hasError.value = true
    }
  }
})

</script>

<style scoped lang="css">
.tile {
    position: relative;
    width: 100%;
    height: 100%;
    border-radius: 0.5rem;
    overflow: hidden;
    transition: all 400ms ease;
    box-shadow: 0 0 4px rgba(0, 0, 0, 0.4);
    aspect-ratio: 10/7;
}

.playing-highlight {
    box-shadow: 0 0 20px #0c2666d2;
    transform: scale(1.06);
}

.tile img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.tile .footer {
    position: absolute;
    bottom: 0;
    left: 0;
    display: flex;
    align-items: flex-end;
    background-color: #2020205e;
    width: 100%;
    padding: 0.3rem 0.5rem;
    /* min-height: 34px; */
}

.footer .inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    min-height: 1.8rem;
}

.inner .control-icon {
    color: #fff;
}

.disabled {
    height: 100%;
}

.tile .tile-text {
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
    filter: drop-shadow(1px 2px 2px black);
    color: #fff;
}

.loading-spinner {
    transition: all 200ms ease;
    position: absolute;
    z-index: 5;
    bottom: 0;
    right: 0;
    margin: 0.3rem;
    color: #fff;
}

.fade-enter-active,
.fade-leave-active {
    opacity: 1;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

audio {
  visibility: hidden;
  position: fixed;
}

ion-icon {
    font-size: 1.5rem;
}
</style>