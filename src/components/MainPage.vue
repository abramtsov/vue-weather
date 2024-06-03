<script setup>
import { ref } from "vue";
import ky from "ky";

defineProps({
  msg: String,
});

const json = await ky
  .get(
    "https://api.openweathermap.org/data/2.5/weather?q=moscow&appid=d59df76d4159ffd1335453f3f101f469"
  )
  .json();

console.log(json);
</script>

<template>
  <form action="">
    <input type="text" placeholder="Enter Location" />
    <input type="submit" value="&#128269;" />
  </form>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
