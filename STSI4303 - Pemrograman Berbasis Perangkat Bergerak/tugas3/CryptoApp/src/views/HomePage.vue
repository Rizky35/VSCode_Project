<template>
  <ion-page>
    <ion-content class="ion-padding">

      <!-- Tombol Refresh -->
      <div class="btn-container">
        <ion-button color="primary" @click="loadCrypto" >
          Refresh
        </ion-button>
      </div>

      <!-- Loading -->
      <div v-if="loading" class="loading">Memuat data...</div>

      <!-- Error -->
      <div v-if="error" class="error">{{ error }}</div>

      <!-- List Crypto -->
      <ion-list v-if="!loading && !error">
        <ion-item v-for="coin in coins" :key="coin.id" class="coin-item" lines="none">
          <ion-label>
            <div class="row">
              <div class="rank">Rank {{ coin.rank }}</div>

              <div class="crypto-info">
                <strong>{{ coin.name }}</strong>
                <div>{{ coin.symbol }}</div>
              </div>

              <div class="price">
                USD <br />
                <strong>{{ coin.price_usd }}</strong>
              </div>
            </div>
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
  IonItem,
  IonList,
  IonLabel,
  IonButton,
} from "@ionic/vue";

// Data reactive
const coins = ref<any[]>([]);
const loading = ref(true);
const error = ref("");

// Fungsi load data crypto
const loadCrypto = async () => {
  loading.value = true;
  error.value = "";

  try {
    const url = "https://api.coinlore.net/api/tickers/";

    const response = await axios.get(url);
    coins.value = response.data.data.slice(0, 20); // ambil 20 teratas
  } catch (err) {
    error.value = "Gagal memuat data cryptocurrency.";
  } finally {
    loading.value = false;
  }
};

// Panggil API ketika halaman pertama kali terbuka
onMounted(() => {
  loadCrypto();
});
</script>

<style>
.loading {
  margin-top: 15px;
}

.error {
  margin-top: 15px;
  color: red;
  font-weight: bold;
}

.row {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.rank {
  width: 60px;
  font-weight: bold;
}

.crypto-info {
  text-align: center;
  flex-grow: 1;
}

.price {
  width: 100px;
  text-align: right;
}

.btn-container {
	display: flex;
	justify-content:center;
	margin-top:20px;
}

.coin-item {
  --border-radius: 6px;
	--background: #fff4d0;
	display: flex;
	justify-content: space-between;
  margin-bottom: 10px;
}

</style>
