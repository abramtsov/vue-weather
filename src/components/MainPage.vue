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
// watch(error, () => {
//   const resetErr = setTimeout(() => {
//     error.class = "animate__animated animate__fadeOutDown";
//     setTimeout(() => {
//       error.bool = false;
//       error.class = "animate__animated animate__fadeInUp";
//       error.msg = "";
//       console.log(error);
//     }, 1000);
//     return;
//   }, 3000);
//   // clearTimeout(resetErr);
// });
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
      <!-- <p>Погода: {{ weather.weather }}</p> -->
      <p>За окном {{ weather.description }}</p>
      <div>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          viewBox="0 0 24 24"
          style="fill: rgba(80, 80, 80, 1)"
        >
          <path
            d="M9 16a3.001 3.001 0 0 0 6 0c0-.353-.072-.686-.184-1H9.184A2.962 2.962 0 0 0 9 16z"
          ></path>
          <path
            d="M18 6V4h-3.185A2.995 2.995 0 0 0 12 2c-1.654 0-3 1.346-3 3v5.8A6.027 6.027 0 0 0 6 16c0 3.309 2.691 6 6 6s6-2.691 6-6a6.027 6.027 0 0 0-3-5.2V10h3V8h-3V6h3zm-4.405 6.324A4.033 4.033 0 0 1 16 16c0 2.206-1.794 4-4 4s-4-1.794-4-4c0-1.585.944-3.027 2.405-3.676l.595-.263V5a1 1 0 0 1 2 0v7.061l.595.263z"
          ></path>
        </svg>
        <p>Температура: {{ Math.ceil(weather.temp) }} &deg;</p>
      </div>
      <div>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          viewBox="0 0 24 24"
          style="fill: rgba(80, 80, 80, 1)"
        >
          <path
            d="M13 5.5C13 3.57 11.43 2 9.5 2 7.466 2 6.25 3.525 6.25 5h2c0-.415.388-1 1.25-1 .827 0 1.5.673 1.5 1.5S10.327 7 9.5 7H2v2h7.5C11.43 9 13 7.43 13 5.5zm2.5 9.5H8v2h7.5c.827 0 1.5.673 1.5 1.5s-.673 1.5-1.5 1.5c-.862 0-1.25-.585-1.25-1h-2c0 1.475 1.216 3 3.25 3 1.93 0 3.5-1.57 3.5-3.5S17.43 15 15.5 15z"
          ></path>
          <path
            d="M18 5c-2.206 0-4 1.794-4 4h2c0-1.103.897-2 2-2s2 .897 2 2-.897 2-2 2H2v2h16c2.206 0 4-1.794 4-4s-1.794-4-4-4zM2 15h4v2H2z"
          ></path>
        </svg>
        <p>Ветер: {{ Math.ceil(weather.wind) }} м/с</p>
      </div>
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
