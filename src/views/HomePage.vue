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
      <ion-card>
        <ion-card-header>
          <ion-card-title>Scan QR Code</ion-card-title>
        </ion-card-header>
        <ion-card-content>Click the button to scan a QR code</ion-card-content>
        <ion-button @click="codescan()" color="success" fill="clear">Escanear</ion-button>
      </ion-card>

      <ion-list>
        <!-- AQUI VAN LOS ELEMENTOS DE LA VISTA -->
        <ion-item v-for="(item, index) in scanHistory" :key="index">
          <ion-label>{{ item }}</ion-label>
        </ion-item>
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
import { ref } from 'vue';

// Importar el plugin de escaneo de codigos QR @capacitor/barcode-scanner
import { CapacitorBarcodeScanner } from '@capacitor/barcode-scanner';
const scanHistory = ref<string[]>([]);

const codescan = (async () =>{
  const { ScanResult } = await CapacitorBarcodeScanner.scanBarcode({hint: 17});
  scanHistory.value.unshift(ScanResult);
  console.log(ScanResult);
  console.log(scanHistory.value);
  //si es un link abrir pestaña
  if(ScanResult.startsWith('http')) {
    window.open(ScanResult, '_blank');
    return;
  }
  //si es un correo abrir cliente de correo
  if(ScanResult.startsWith('mailto:')) {
    window.open(ScanResult, '_self');
    return;
  }
})


const refresh = (event: CustomEvent) => {
  setTimeout(() => {
    event.detail.complete();
  }, 2000);
};
</script>
