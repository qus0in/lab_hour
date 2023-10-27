<template>
  <div class="progress">
    <div
      :class="[
        'progress-bar',
        'progress-bar-striped',
        'progress-bar-animated',
        mode === TimerMode.REST ? 'bg-success' : 'bg-danger',
      ]"
      role="progressbar"
      :aria-valuenow="timerPercent"
      aria-valuemin="0"
      aria-valuemax="100"
      :style="{ width: timerPercent + '%' }"
    ></div>
  </div>
</template>

<script setup>
import { TimerMode } from "../util";
const { mode, timerPercent } = defineProps(["mode", "timerPercent"]);
</script>
