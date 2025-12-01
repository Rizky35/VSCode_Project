<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>Cuaca Jakarta</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">

      <h2>Prakiraan Suhu per Jam</h2>

      <div v-if="loading">Memuat data cuaca...</div>
      <div v-if="error" class="error">{{ error }}</div>

      <ion-list v-if="!loading && !error">
        <ion-item v-for="(time, index) in times" :key="index">
          <ion-label>
            <h3>{{ time }}</h3>
            <p>Suhu: {{ temperatures[index] }} °C</p>
          </ion-label>
        </ion-item>
      </ion-list>

    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import axios from "axios";

import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonList,
  IonItem,
  IonLabel
} from "@ionic/vue";

const times = ref<string[]>([]);
const temperatures = ref<number[]>([]);
const loading = ref(true);
const error = ref("");

const loadWeather = async () => {
  try {
    const url =
      "https://api.open-meteo.com/v1/forecast?latitude=-6.2&longitude=106.8&hourly=temperature_2m";

    const response = await axios.get(url);

    const data = response.data;

    times.value = data.hourly.time.slice(0, 24);          // ambil 24 jam pertama
    temperatures.value = data.hourly.temperature_2m.slice(0, 24);
  } catch (err) {
    error.value = "Gagal memuat data cuaca. Pastikan koneksi internet aktif.";
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  loadWeather();
});
</script>

<style>
h2 {
  margin-bottom: 15px;
}

.error {
  color: red;
  margin-bottom: 10px;
}
</style>
