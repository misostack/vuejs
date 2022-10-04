<template>
  <div class="container">
    <div class="flex">
      <div class="flex-none w-64"></div>
      <div class="grow h-14">
        <h1>SixJars</h1>
        <p>
          <label>Balance:</label>
          <input
            class="form-input px-4 py-3 rounded-full"
            type="text"
            v-model.lazy="balance.value"
            placeholder="0"
          />
        </p>
        <h3>Left: {{ state.leftRatio }}</h3>
        <div class="grid grid-cols-6 gap-4 my-8">
          <div v-for="bottle in state.bottles" :key="bottle.name">
            <h3>{{ bottle.name }}</h3>
            <h4>{{ bottle.ratio }}</h4>
            <h5>
              {{ parseLocaleNumber((bottle.ratio * balanceValue) / 100) }}
            </h5>
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
import { reactive, ref, watch } from "vue";

// helpers
function parseLocaleNumber(stringNumber: number, locale: string = "en-EN") {
  return new Intl.NumberFormat(locale).format(stringNumber);
}

function reverseLocaleNumber(stringNumber: string, locale: string = "en-EN") {
  const thousandSeparator = Intl.NumberFormat(locale)
    .format(11111)
    .replace(/\p{Number}/gu, "");
  const decimalSeparator = Intl.NumberFormat(locale)
    .format(1.1)
    .replace(/\p{Number}/gu, "");

  return parseFloat(
    stringNumber
      .replace(new RegExp("\\" + thousandSeparator, "g"), "")
      .replace(new RegExp("\\" + decimalSeparator), ".")
  );
}

// application
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
let balance = reactive({ value: "" });
let balanceValue = ref(0);

watch(balance, (newBalance, oldBalance) => {
  const balanceNumber = reverseLocaleNumber(newBalance.value);
  balanceValue.value = balanceNumber;
  balance.value = parseLocaleNumber(balanceNumber);
});

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
