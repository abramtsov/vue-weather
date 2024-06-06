<script setup>
import { reactive, ref, watchEffect } from "vue";
import ky from "ky";

defineProps({
  msg: String,
});
const input = ref(null);
let isShow = ref(false);
let weatherRequest = ref({});
let weather = reactive({
  name: "",
});
const query = async () => {
  try {
    if (input._value.value === "") {
      alert("Введите название города");
      return;
    }
    weatherRequest = await ky
      .get(
        `https://api.openweathermap.org/data/2.5/weather?q=${input._value.value}&appid=d59df76d4159ffd1335453f3f101f469&lang=ru&units=metric`
      )
      .json();
    isShow.value = true;
    console.log(weatherRequest);
    weather.name = weatherRequest.name;
  } catch (e) {
    if (e.response.status === 404) {
      console.log("город не найден");
    }
    console.log();
  }
};
</script>

<template>
  <form action="">
    <input type="text" ref="input" placeholder="Enter Location" />
    <input type="submit" @click.prevent="query" value="&#128269;" />
  </form>
  <div v-if="isShow">
    <p>Город: {{ weather.name }}</p>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
