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
let loader = ref("");
let weather = reactive({
  weather: "",
  wind: 0,
  temp: 0,
  description: "",
});
watch(error, () => {
  const resetErr = setTimeout(() => {
    error.class = "animate__animated animate__fadeOutDown";
    error.bool = false;
    error.class = "animate__animated animate__fadeInUp";
    error.msg = "";
    return;
  }, 3000);
  // clearTimeout(resetErr);
});
const weatherClass = computed(() => {
  return weather.weather.toLocaleLowerCase();
});
const addFav = () => {
  let fav = localStorage.getItem("city");
  if (fav != null) {
    fav = JSON.parse(fav);
    if (fav.cities.length < 4) {
      fav.cities.push(input._value.value);
      localStorage.setItem("city", JSON.stringify({ cities: fav.cities }));
    }
  } else {
    let cities = [];
    cities.push(input._value.value);
    localStorage.city = JSON.stringify({ cities });
  }
};
const query = async () => {
  try {
    if (input._value.value === "") {
      isShow.value = false;
      error.bool = true;
      error.msg = "Field must be filled in";
      return;
    }
    isShow.value = false;
    loader.value = true;
    let weatherRequest = await ky
      .get(
        // `https://api.openweathermap.org/data/2.5/weather?q=${input._value.value}&appid=d59df76d4159ffd1335453f3f101f469&lang=ru&units=metric`,
        `https://api.weatherapi.com/v1/current.json?key=ef804e37e4dc40b9813122333242409&q=${input._value.value}&aqi=no`,
        {
          // headers: {
          //   "Content-Type": "application/json",
          //   "Access-Control-Allow-Origin": "*",
          //   "Access-Control-Allow-Headers":
          //     "Origin, X-Requested-With, Content-Type, Accept",
          //   "Access-Control-Allow-Methods": "GET, POST",
          // },
        }
      )
      .json();
    loader.value = false;
    isShow.value = true;
    console.log(weatherRequest);
    // weather.weather = weatherRequest.weather[0].main;
    weather.humidity = weatherRequest.current.humidity;
    weather.icon = weatherRequest.current.condition.icon;
    weather.wind = weatherRequest.current.wind_kph;
    weather.temp = weatherRequest.current.temp_c;
    weather.description = weatherRequest.current.condition.text;
  } catch (e) {
    console.log(e.response.status);
    if (e.name === "TimeoutError" || e.name === "TypeError") {
      loader.value = false;
      error.bool = true;
      error.msg = "Check your Internet connection";
      return;
    }
    if (e.response.status === 404 || e.response.status === 400) {
      loader.value = false;
      error.bool = true;
      error.msg = "City not found";
      return;
    }
  }
};
</script>

<template>
  <div class="layout" :class="weatherClass">
    <div class="fav">
      <div>
        <p>i</p>
      </div>
      <div>
        <p>i</p>
      </div>
      <div>
        <p>i</p>
      </div>
      <div>
        <p>i</p>
      </div>
    </div>
    <form action="">
      <input
        type="text"
        ref="input"
        placeholder="Enter Location"
        required
        autofocus
      />
      <input type="submit" @click.prevent="query" value="&#128269;" />
      <button class="cityFav" v-if="isShow" @click.prevent="addFav">
        <svg
          height="2rem"
          width="2rem"
          version="1.1"
          id="Layer_1"
          xmlns="http://www.w3.org/2000/svg"
          xmlns:xlink="http://www.w3.org/1999/xlink"
          viewBox="0 0 512 512"
          xml:space="preserve"
        >
          <path
            style="fill: #ffda44"
            d="M276.014,23.866l67.027,135.799l149.825,21.785c18.306,2.662,25.615,25.157,12.369,38.071
	L396.825,325.205l25.578,149.24c3.125,18.232-16.012,32.135-32.385,23.528l-134.025-70.452l-134.016,70.452
	c-16.374,8.608-35.511-5.294-32.386-23.528l25.58-149.241L6.764,219.521c-13.246-12.912-5.937-35.409,12.369-38.071l149.824-21.787
	l67.026-135.798C244.171,7.277,267.827,7.277,276.014,23.866z"
          />
          <path
            style="fill: #ffaa00"
            d="M492.867,181.448l-149.825-21.785L276.014,23.866c-4.215-8.541-12.524-12.695-20.718-12.441v416.463
	l0.698-0.366l134.025,70.451c16.374,8.607,35.51-5.296,32.386-23.528l-25.578-149.241l108.409-105.685
	C518.482,206.605,511.172,184.11,492.867,181.448z"
          />
        </svg>
      </button>
      <div>
        <span v-if="loader" class="loader"></span>
      </div>
    </form>

    <div
      v-if="isShow"
      class="weatherResponse animate__animated animate__fadeInUp"
    >
      <div>
        <img :src="weather.icon" alt="" />
        <p>{{ weather.description }}</p>
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
        <p>Ветер: {{ Math.ceil((weather.wind * 1000) / 3600) }} м/с</p>
      </div>
      <div>
        <svg
          fill="#404040"
          height="24px"
          width="24px"
          version="1.1"
          id="Capa_1"
          xmlns="http://www.w3.org/2000/svg"
          xmlns:xlink="http://www.w3.org/1999/xlink"
          viewBox="0 0 328.61 328.61"
          xml:space="preserve"
          stroke="#404040"
          stroke-width="8.929164999999999"
        >
          <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
          <g
            id="SVGRepo_tracerCarrier"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke="#CCCCCC"
            stroke-width="0.657222"
          ></g>
          <g id="SVGRepo_iconCarrier">
            <g>
              <path
                d="M209.306,50.798c-2.452-3.337-7.147-4.055-10.485-1.602c-3.338,2.453-4.055,7.147-1.603,10.485 c54.576,74.266,66.032,123.541,66.032,151.8c0,27.691-8.272,52.794-23.293,70.685c-17.519,20.866-42.972,31.446-75.651,31.446 c-73.031,0-98.944-55.018-98.944-102.131c0-52.227,28.103-103.234,51.679-136.829c25.858-36.847,52.11-61.415,52.37-61.657 c3.035-2.819,3.209-7.565,0.39-10.6c-2.819-3.034-7.565-3.209-10.599-0.39c-1.11,1.031-27.497,25.698-54.254,63.765 c-24.901,35.428-54.586,89.465-54.586,145.71c0,31.062,9.673,59.599,27.236,80.353c20.361,24.061,50.345,36.779,86.708,36.779 c36.794,0,66.926-12.726,87.139-36.801c17.286-20.588,26.806-49.117,26.806-80.33C278.25,156.216,240.758,93.597,209.306,50.798z"
              ></path>
            </g>
          </g>
        </svg>
        <p>Влажность: {{ weather.humidity }}%</p>
      </div>
    </div>
    <div class="error" :class="error.class" v-if="error.bool">
      {{ error.msg }}
    </div>
  </div>
</template>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Comfortaa:wght@300..700&family=Montserrat+Alternates:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
.read-the-docs {
  color: #888;
}
</style>
