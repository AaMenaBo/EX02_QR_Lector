<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Inbox</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-refresher slot="fixed" @ionRefresh="refresh($event)">
        <ion-refresher-content></ion-refresher-content>
      </ion-refresher>

      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Inbox</ion-title>
        </ion-toolbar>
      </ion-header>

      <!-- Botón dentro de un card para escanear un codigo QR-->
      <ion-card router-link="/scan">
        <ion-card-header>
          <ion-card-title>Scan QR Code</ion-card-title>
        </ion-card-header>
        <ion-card-content>Click the button to scan a QR code</ion-card-content>
        <ion-button @click="codescan(index)" color="success" fill="clear">Escanear</ion-button>
      </ion-card>

      <ion-list>
        <!-- AQUI VAN LOS ELEMENTOS DE LA VISTA -->

      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonContent,
  IonHeader,
  IonList,
  IonPage,
  IonRefresher,
  IonRefresherContent,
  IonTitle,
  IonToolbar,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonCardContent,
  IonButton,
  IonButtons,
  IonBackButton,
  IonLabel, 
  IonItem,
  IonIcon,
  IonAvatar,
} from '@ionic/vue';
import { getMessages, Message } from '@/data/messages';
import { ref } from 'vue';

const messages = ref<Message[]>(getMessages());

const refresh = (ev: CustomEvent) => {
  setTimeout(() => {
    ev.detail.complete();
  }, 3000);
};
</script>
