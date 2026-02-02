<script setup>
import { onMounted, provide, ref, watch } from "vue";
import PanelRight from "./components/PanelRight.vue";
import { API_ENDPOINT, cityProvide } from "./constants";
import PanelLeft from "./components/PanelLeft.vue";

let data = ref();
let error = ref();
let activeIndex = ref(0);
let city = ref("Kovel");

provide(cityProvide, city);

watch(city, () => {
  getCity(city.value);
});

onMounted(() => {
  getCity(city.value);
});

async function getCity(city) {
  const params = new URLSearchParams({
    q: city,
    lang: "en",
    key: "30db5d3b6f9b4730924215412262701",
    days: 3,
  });
  const res = await fetch(`${API_ENDPOINT}/forecast.json?${params.toString()}`);
  if (res.status != 200) {
    error.value = await res.json();
    data.value = null;
    return;
  }
  error.value = null;
  data.value = await res.json();
}
</script>

<template>
  <main class="main">
    <div class="left">
      <PanelLeft
        v-if="data"
        :day-data="data.forecast.forecastday[activeIndex]"
      />
    </div>
    <div class="right">
      <PanelRight
        :data
        :error
        :active-index="activeIndex"
        @select-index="(i) => (activeIndex = i)"
      />
    </div>
  </main>
</template>

<style scoped>
.right {
  background: var(--color-bg-main);
  padding: 60px 50px;
  border-radius: 0 25px 25px 0;
}
.left {
  /* width: 500px;
  height: 680px; */
  border-radius: 30px;
  background-image: url("/bg.jpg");
  background-repeat: no-repeat;
  background-size: cover;
}
@media (min-width: 1101px) {
  .main {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .left {
    width: 500px;
    height: 680px;
  }
}
</style>