<template>
  <link href='https://api.mapbox.com/mapbox-gl-js/v3.11.0/mapbox-gl.css' rel='stylesheet' />
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
      <div id='map-area' style='width: 400px; height: 300px;'></div>
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
import { CapacitorBarcodeScanner } from '@capacitor/barcode-scanner';
import mapboxgl from 'mapbox-gl'; // or "const mapboxgl = require('mapbox-gl');"
const scanHistory = ref<string[]>([]);
mapboxgl.accessToken = 'pk.eyJ1IjoieWVzY2EiLCJhIjoiY205dWwxNnVoMDNmbTJsb2VjOTB3YnMwciJ9.WBq2Zn2X1JnkJ_39zwlQjQ';


const codescan = (async () => {
  const { ScanResult } = await CapacitorBarcodeScanner.scanBarcode({ hint: 17 });
  scanHistory.value.unshift(ScanResult);
  //si es un link abrir pestaña
  if (ScanResult.startsWith('http')) {
    window.open(ScanResult, '_blank');
    return;
  }
  //si es un correo abrir cliente de correo
  if (ScanResult.startsWith('mailto:')) {
    window.open(ScanResult, '_self');
    return;
  }
  // si es un par de coordenadas (latitud, longitud) mostrar en el mapa
  const coordinateRegex = /^-?\d+(\.\d+)?,-?\d+(\.\d+)?$/;
  const flag = coordinateRegex.test(ScanResult);
  console.log(flag);
  if (flag) {
    const [lat, lng] = ScanResult.split(',').map(Number);
    createMap(lng, lat);
    console.log(`Intento de crear mapa: Latitud: ${lat}, Longitud: ${lng}`);
    return;
  }
})
const createMap = (async (lng: number, lat: number) => {
  let map = null;
  map = new mapboxgl.Map({
    container: 'map-area', // container ID
    style: 'mapbox://styles/mapbox/streets-v12', // style URL
    center: [lng, lat], // starting position [lng, lat]
    zoom: 14 // starting zoom
  });
  let marker = null
  marker = new mapboxgl.Marker()
    .setLngLat([lng, lat])
    .addTo(map);
})


const refresh = (event: CustomEvent) => {
  setTimeout(() => {
    event.detail.complete();
  }, 2000);
};

</script>
