<script setup>
import { reactive, ref, computed } from "vue";
import ky from "ky";
import "animate.css";

defineProps({
  msg: String,
});
const input = ref(null);
let isShow = ref(false);
let weather = reactive({
  weather: "",
  wind: 0,
  temp: 0,
  description: "",
});

let weatherClass = computed(() => {
  return weather.weather.toLocaleLowerCase();
});
const query = async () => {
  try {
    if (input._value.value === "") {
      isShow.value = false;
      alert("Введите название города");
      return;
    }
    isShow.value = false;
    let weatherRequest = await ky
      .get(
        `https://api.openweathermap.org/data/2.5/weather?q=${input._value.value}&appid=d59df76d4159ffd1335453f3f101f469&lang=ru&units=metric`
      )
      .json();
    isShow.value = true;
    console.log(weatherRequest);
    weather.weather = weatherRequest.weather[0].main;
    weather.wind = weatherRequest.wind.speed;
    weather.temp = weatherRequest.main.temp;
    weather.description = weatherRequest.weather[0].description;
    // weather.weather = weatherRequest.weather[0].main;
  } catch (e) {
    if (e.response.status === 404) {
      console.log("город не найден");
    }
  }
};
</script>

<template>
  <div class="layout" :class="weatherClass">
    <form action="">
      <input type="text" ref="input" placeholder="Enter Location" />
      <input type="submit" @click.prevent="query" value="&#128269;" />
    </form>
    <div
      v-if="isShow"
      class="weatherResponse animate__animated animate__fadeInUp"
    >
      <p>Погода: {{ weather.weather }}</p>
      <p>Описание: {{ weather.description }}</p>
      <p>Температура: {{ Math.ceil(weather.temp) }} &deg;</p>
      <p>Ветер: {{ Math.ceil(weather.wind) }} м/с</p>
    </div>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
