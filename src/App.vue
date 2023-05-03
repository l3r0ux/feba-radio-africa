<template>
  <ion-app>
    <ion-router-outlet />
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp, IonRouterOutlet } from '@ionic/vue';
import { PushNotifications } from '@capacitor/push-notifications';
import { useIonRouter, useBackButton } from '@ionic/vue';

const ionRouter = useIonRouter()

useBackButton(10, () => {
  ionRouter.back()
});

PushNotifications.requestPermissions().then(result => {
  PushNotifications.register();
});
PushNotifications.addListener('registration', (token) => {
  console.log('Push registration success, token: ' + token.value);
});

PushNotifications.addListener(
  "pushNotificationActionPerformed",
  async (_notification) => {
      ionRouter.push('/prayer')
  }
)

PushNotifications.addListener("registrationError", (error) => {
  console.log(`error on register ${JSON.stringify(error)}`);
})

</script>
