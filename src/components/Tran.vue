<script setup>
import { ref } from "vue";
import "animate.css";
let one = ref(false);
let two = ref(false);
</script>
<template>
  <Transition
    name="custom-classes"
    enter-active-class="animate__animated animate__zoomIn"
    leave-active-class="animate__animated animate__zoomOut"
  >
    <div class="absol" v-if="one">
      <p>one</p>
    </div>
  </Transition>
  <Transition
    name="custom-classes"
    enter-active-class="animate__animated animate__zoomIn"
    leave-active-class="animate__animated animate__zoomOut"
  >
    <div class="absol" v-if="two">
      <p>two</p>
    </div>
  </Transition>
  <button
    @click="
      () => {
        two = false;
        one = true;
      }
    "
  >
    1
  </button>
  <button
    @click="
      () => {
        one = false;
        two = true;
      }
    "
  >
    2
  </button>
</template>
<style scoped>
.absol {
  position: absolute;
}
button {
  position: relative;
}
</style>