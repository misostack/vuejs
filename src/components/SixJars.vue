<template>
  <div class="container">
    <div class="flex">
      <div class="flex-none w-64"></div>
      <div class="grow h-14">
        <h1>SixJars</h1>
        <h3>Left: {{ state.leftRatio }}</h3>
        <div class="grid grid-cols-6 gap-4 my-8">
          <div v-for="bottle in state.bottles" :key="bottle.name">
            <h3>{{ bottle.name }}</h3>
            <h4>{{ bottle.ratio }}</h4>
            <div>
              <button>
                <span
                  class="material-icons"
                  v-on:click="adjustRatio(bottle.name, 'increase')"
                  >add</span
                >
              </button>
              <button>
                <span
                  class="material-icons"
                  v-on:click="adjustRatio(bottle.name, 'decrease')"
                  >remove</span
                >
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="flex-none w-64"></div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
const initialValues = {
  bottles: [
    { name: "FFA", ratio: 10 },
    { name: "PLAY", ratio: 10 },
    { name: "LTSS", ratio: 10 },
    { name: "EDU", ratio: 10 },
    { name: "GIVE", ratio: 5 },
    { name: "NEC", ratio: 55 },
  ],
  leftRatio: 0,
};
// 6 bottles
const state = reactive(initialValues);

const adjustRatio = (name: string, type: "increase" | "decrease") => {
  for (let i = 0; i < state.bottles.length; i++) {
    if (type === "increase" && state.leftRatio === 0) {
      return;
    }
    if (state.bottles[i].name === name) {
      const adjustValue = type === "increase" ? 1 : -1;
      state.bottles[i].ratio += adjustValue;
      state.leftRatio -= adjustValue;
    }
  }
};
</script>

<style scoped></style>
