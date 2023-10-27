<script setup>
import { computed, ref, watch } from "vue";
import TimeSetting from "./components/TimeSetting.vue";
import { TimerMode } from "./util";
import ModeHeader from "./components/ModeHeader.vue";
import ProgressBar from "./components/ProgressBar.vue";

const mode = ref(TimerMode.SETTING);
const repeat = ref(true);
const restTimeMinute = ref(10);
const studyTimeMinute = ref(50);

const timerSeconds = ref(0);
const timerMinutesText = computed(() =>
  String(Number.parseInt(timerSeconds.value / 60)).padStart(2, "0")
);
const timerSecondsText = computed(() =>
  String(timerSeconds.value % 60).padStart(2, "0")
);
const timerPercent = computed(() =>
  Number.parseInt(
    (1 -
      timerSeconds.value /
        (mode.value == TimerMode.REST ? restTimeMinute : studyTimeMinute)
          .value /
        60) *
      100
  )
);
let timerId;
watch(mode, async (newMode, oldMode) => {
  // console.log(newMode, oldMode);
  switch (newMode) {
    case TimerMode.STUDY:
      timerSeconds.value = studyTimeMinute.value * 60;
      break;
    case TimerMode.REST:
      timerSeconds.value = restTimeMinute.value * 60;
      break;
  }
  if (timerId) {
    clearInterval(timerId);
  }
  timerId = setInterval(() => {
    if (timerSeconds.value == 0) {
      clearInterval(timerId);
      const audio = new Audio("./clock-alarm-8761.mp3");
      audio.play();
      if (repeat.value) {
        // console.log('반복 계속')
        switch (mode.value) {
          case TimerMode.STUDY:
            mode.value = TimerMode.REST;
            break;
          case TimerMode.REST:
            mode.value = TimerMode.STUDY;
            break;
        }
      } else {
        // console.log('반복 중지')
        mode.value = TimerMode.SETTING;
      }
      return;
    }
    timerSeconds.value--;
  }, 1000);
});
</script>

<template>
  <div class="container text-center py-5">
    <header>
      <Transition mode="out-in">
        <ModeHeader
          id="studyHeader"
          label="실습 시간"
          img="./taxi-multitasking-at-work.png"
          v-model="repeat"
          v-if="mode == TimerMode.STUDY"
        />
        <ModeHeader
          id="restHeader"
          label="쉬는 시간"
          img="./taxi-man-tired-of-studying.png"
          v-model="repeat"
          v-else-if="mode == TimerMode.REST"
        />
        <ModeHeader
          id="settingHeader"
          label="실습 타이머"
          img="./taxi-coming-soon-text-with-a-waiting-man-lettering.png"
          v-model="repeat"
          v-else
        />
      </Transition>
    </header>
    <form>
      <div class="mb-3 row d-flex justify-content-center">
        <Transition mode="out-in">
          <TimeSetting
            v-model:studyTimeMinute="studyTimeMinute"
            v-model:restTimeMinute="restTimeMinute"
            v-model:mode="mode"
            v-if="mode === TimerMode.SETTING"
          />
          <div class="col-9 col-md-6" v-else>
            <p class="display-4 p-4">
              {{ timerMinutesText }}:{{ timerSecondsText }}
            </p>
            <ProgressBar :mode="mode" :timerPercent="timerPercent" />
            <div class="row my-4">
              <div class="col">
                <button
                  type="button"
                  class="btn btn-outline-primary m-2"
                  @click="
                    () => {
                      mode = TimerMode.SETTING;
                    }
                  "
                >
                  재설정
                </button>
              </div>
            </div>
          </div>
        </Transition>
      </div>
    </form>
  </div>
</template>

<style>
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
