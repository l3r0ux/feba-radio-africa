<template>
    <nav class="navbar">
        <ion-icon size="large" @click="ionRouter.back()" :icon="arrowBack"></ion-icon>
        <h1>{{ heading }}</h1>
    </nav>
</template>

<script setup lang="ts">
import { IonButton, IonPage, IonIcon, useIonRouter } from '@ionic/vue';
import { arrowBack } from 'ionicons/icons';

defineProps<{
    backRoute: String,
    heading: String
}>()

const ionRouter = useIonRouter()

</script>

<style scoped lang="css">
.navbar {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 10;
    display: flex;
    align-items: center;
    width: 100%;
    padding: 0.8rem 0.5rem;
    background-color: #0c2566;
}

.navbar h1 {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    margin: 0;
}
</style>