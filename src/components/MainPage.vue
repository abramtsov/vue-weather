<script setup>
import { reactive, ref, computed, watch } from "vue";
import ky from "ky";
import "animate.css";

defineProps({
  msg: String,
});
const input = ref(null);
let isShow = ref(false);
let error = reactive({
  bool: false,
  msg: "",
  class: "animate__animated animate__fadeInUp",
});
// let errorMsg = ref("");
let weather = reactive({
  weather: "",
  wind: 0,
  temp: 0,
  description: "",
});
watch(error, () => {
  const resetErr = setTimeout(() => {
    error.class = "animate__animated animate__fadeOutDown";
    setTimeout(() => {
      error.bool = false;
      error.class = "animate__animated animate__fadeInUp";
      error.msg = "";
      console.log(error);
    }, 1000);
    return;
  }, 3000);
  // clearTimeout(resetErr);
});
const weatherClass = computed(() => {
  return weather.weather.toLocaleLowerCase();
});
const query = async () => {
  try {
    if (input._value.value === "") {
      isShow.value = false;
      error.bool = true;
      error.msg = "Field must be filled in";
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
  } catch (e) {
    if (e.response.status === 404) {
      error.bool = true;
      error.msg = "City not found";
    }
  }
};
</script>

<template>
  <div class="layout" :class="weatherClass">
    <form action="">
      <input type="text" ref="input" placeholder="Enter Location" required />
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
    <div class="error" :class="error.class" v-if="error.bool">
      {{ error.msg }}
    </div>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
