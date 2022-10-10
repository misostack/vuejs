<template>
  <div class="container p-4">
    <h1>State Management Principles</h1>
    <ol>
      <li>Group relevant states</li>
    </ol>
    <h2>Example 1: Group relevant states</h2>
    {{ example1MousePosition.x }} - {{ example1MousePosition.y }}
    <div
      ref="example1"
      class="example-1 h-48 w-1/2"
      @mousemove="onMouseMove($event)"
    >
      <span id="circle2"></span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

// state
const example1 = ref<HTMLDivElement | null>(null);
const example1MousePosition = ref({ x: "0", y: "0" });

// event handlers
const onMouseMove = ($event: MouseEvent) => {
  const x = $event.clientX;
  const y = $event.clientY;
  if (example1.value) {
    const boundingRect = example1.value.getBoundingClientRect();
    example1MousePosition.value.x = x - boundingRect.x + "px";
    example1MousePosition.value.y = y - boundingRect.y + "px";
  }
};
</script>

<style scoped lang="scss">
@import "../assets/scss/var";
.example-1 {
  background-color: $primary_color;
  position: relative;
  margin: 0px auto;
  cursor: none;
  overflow: hidden;
}
#circle2 {
  position: absolute;
  -webkit-animation: pulse 4s infinite; /* Chrome, Safari, Opera */
  animation: pulse 2s infinite;
  background: rgba(236, 224, 10, 0.8);
  border-radius: 50%;
  height: 0em;
  width: 0em;
  margin-top: 0em;
  margin-left: 0em;
  left: v-bind("example1MousePosition.x");
  top: v-bind("example1MousePosition.y");
}

@keyframes pulse {
  0% {
    opacity: 0.5;
    height: 2em;
    width: 2em;
    margin-top: -0.25em;
    margin-left: -0.25em;
  }
  50% {
    opacity: 0.9;
    height: 3em;
    width: 3em;
    margin-top: -1.5em;
    margin-left: -1.5em;
  }
  100% {
    opacity: 0.5;
    height: 2em;
    width: 2em;
    margin-top: -0.25em;
    margin-left: -0.25em;
  }
}
</style>
