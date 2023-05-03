<template>
  <ion-page class="live-radio-container">
    <navbar back-route="home" heading="Live Radio" />
    <div class="main-container">
      <div class="inner-container">
        <h2>Click on a flag to play radio.</h2>
        <div class="tiles-container">
          <radio-tile
            v-for="tile of radioTiles"
            @set-playing-radio="setPlayingRadio($event)"
            :key="tile.country"
            :station-name="tile.stationName"
            :country="tile.country"
            :imageUrl="tile.imageUrl"
            :radioUrl="tile.radioUrl"
          />
        </div>
      </div>
    </div>
  </ion-page>
</template>

<script setup lang="ts">
import { IonPage } from '@ionic/vue';
import Navbar from '@/components/Navbar.vue';
import RadioTile from '@/components/RadioTile.vue';
import { ref } from 'vue';

interface RadioTile {
  imageUrl: string,
  stationName?: string,
  country: string,
  radioUrl: string
}

let playingRadios = ref<string[]>([])

const setPlayingRadio = ({ playingRadio, pausingRadio }: {
  playingRadio: string,
  pausingRadio: string,
}): void => {
  playingRadios.value.push(playingRadio)

  if (pausingRadio)
    playingRadios.value.splice(0, 1)

  if (playingRadios.value.length === 2) {
    const prevAudioEl: HTMLAudioElement | null = document.querySelector(`.${playingRadios.value[0]}`)
    prevAudioEl?.pause()
    playingRadios.value.splice(0, 1)
  }
}

const radioTiles: Array<RadioTile> = [
  {
    imageUrl: '/images/malawi_flag.png',
    country: 'Malawi',
    stationName: 'Litala FM',
    radioUrl: 'http://r103.netradio.pro:2000/main',
  },
  {
    imageUrl: '/images/cambodia_flag.png',
    country: 'Cambodia',
    radioUrl: 'http://s14.myradiostream.com:10064/;',
  },
  {
    imageUrl: '/images/ukraine_flag.png',
    country: 'Ukraine',
    radioUrl: 'https://stream.radiom.ua/mp3_high',
  },
  {
    imageUrl: '/images/russia_flag.png',
    country: 'Russia',
    radioUrl: 'http://stream.teos.fm:8004/mp3_hq',
  },
  {
    imageUrl: '/images/china_flag.png',
    country: 'China',
    radioUrl: 'http://ly729.out.airtime.pro:8000/ly729_b',
  },
  {
    imageUrl: '/images/korea_flag.png',
    country: 'Korea',
    radioUrl: 'http://mlive2.febc.net:1935/live/seoulfm/playlist.m3u8',
  },
  {
    imageUrl: '/images/philippines_flag.png',
    country: 'Philippines',
    radioUrl: 'http://icecast.eradioportal.com:8000/febc_dzas',
  },
]
</script>

<style lang="css" scoped>
.live-radio-container {
  background: linear-gradient(45deg, #ffffff, #86c8ff, #00579e);
  padding-top: 60px;
}

.inner-container {
  width: 100%;
  height: 100%;
  max-width: 500px;
  margin: 0 auto;
}

.inner-container h2 {
  margin-top: 0;
  margin-bottom: 1.5rem;
  filter: drop-shadow(1px 1px 1px rgba(0, 0, 0, 0.7));
  color: #fff;
}

.main-container {
  width: 100%;
  height: 100%;
  padding: 1rem;
  overflow-y: auto;
}

.tiles-container {
  position: relative;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}
</style>