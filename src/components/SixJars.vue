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
        <h3>Left: {{ leftRatio }}</h3>
        <div class="grid grid-cols-6 gap-4 my-8">
          <div
            class="text-center"
            v-for="bottle in state.bottles"
            :key="bottle.name"
          >
            <h3>{{ bottle.name }}</h3>
            <h4>{{ `${bottle.ratio}%` }}</h4>
            <h5>
              {{ parseLocaleNumber((bottle.ratio * balanceValue) / 100) }}
            </h5>
            <div>
              <button
                class="p-1 mx-1 text-white bg-blue-600 rounded focus:outline-none disabled:opacity-50"
                :disabled="bottle.disabledIncrease"
              >
                <span
                  class="material-icons"
                  v-on:click="adjustRatio(bottle.name, 'increase')"
                  >add</span
                >
              </button>
              <button
                class="p-1 mx-1 text-white bg-blue-600 rounded focus:outline-none disabled:opacity-50"
                :disabled="bottle.disabledDecrease"
              >
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
    { name: "FFA", ratio: 10, disabledIncrease: true, disabledDecrease: false },
    {
      name: "PLAY",
      ratio: 10,
      disabledIncrease: true,
      disabledDecrease: false,
    },
    {
      name: "LTSS",
      ratio: 10,
      disabledIncrease: true,
      disabledDecrease: false,
    },
    { name: "EDU", ratio: 10, disabledIncrease: true, disabledDecrease: false },
    { name: "GIVE", ratio: 5, disabledIncrease: true, disabledDecrease: false },
    { name: "NEC", ratio: 55, disabledIncrease: true, disabledDecrease: false },
  ],
};
const initialBottleRatios: { [key: string]: number } = {};
initialValues.bottles.map((b) => {
  Reflect.set(initialBottleRatios, b.name, b.ratio);
});

// 6 bottles
const state = reactive(initialValues);
const balance = reactive({ value: "" });
const balanceValue = ref(0);
const leftRatio = ref(0);
const bottleRatios = reactive(initialBottleRatios);

watch(balance, (newBalance, oldBalance) => {
  const balanceNumber = reverseLocaleNumber(newBalance.value);
  balanceValue.value = balanceNumber;
  balance.value = parseLocaleNumber(balanceNumber);
});

watch(leftRatio, (newLeftRatio) => {
  // enable increase for all bottles
  if (newLeftRatio > 0) {
    state.bottles.map((b) => (b.disabledIncrease = false));
  }
});

watch(bottleRatios, (ratios) => {
  Reflect.ownKeys(ratios).map((k) => {
    const ratio = Reflect.get(ratios, k);
    const bottle = state.bottles.find((b) => b.name === k);
    if (bottle) {
      bottle.disabledDecrease = ratio === 0;
    }
  });
});

const adjustRatio = (name: string, type: "increase" | "decrease") => {
  for (let i = 0; i < state.bottles.length; i++) {
    // can not increase if no ratio is left
    if (type === "increase" && leftRatio.value === 0) {
      return;
    }
    // can not decrease if bottle ratio is running out
    if (
      type === "decrease" &&
      state.bottles[i].name === name &&
      state.bottles[i].ratio == 0
    ) {
      return;
    }
    // default: can adjust the ratio of bottle
    if (state.bottles[i].name === name) {
      const adjustValue = type === "increase" ? 1 : -1;
      state.bottles[i].ratio += adjustValue;
      leftRatio.value -= adjustValue;
      bottleRatios[name] = leftRatio.value;
    }
  }
};
</script>

<style scoped></style>
