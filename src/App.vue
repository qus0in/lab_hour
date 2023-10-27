<script setup>
import { computed, ref, watch } from "vue";
import TimeSetting from "./components/TimeSetting.vue";
import { TimerMode } from "./util";

const mode = ref(TimerMode.SETTING);
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
      const audio = new Audio('/src/assets/clock-alarm-8761.mp3')
      audio.play()
      switch (mode.value) {
        case TimerMode.STUDY:
          mode.value = TimerMode.REST;
          break;
        case TimerMode.REST:
          mode.value = TimerMode.STUDY;
          break;
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
      <!-- TODO : 컴포넌트화 + 클래스 변수화 -->
      <Transition mode="out-in">
        <div v-if="mode == TimerMode.STUDY">
          <p class="display-3">실습 시간</p>
          <div style="height: 400px" class="d-flex align-items-center justify-content-center">
            <img class="img-fluid" src="/src/assets/taxi-multitasking-at-work.png" />
          </div>
        </div>
        <div v-else-if="mode == TimerMode.REST">
          <p class="display-3">쉬는 시간</p>
          <div style="height: 400px" class="d-flex align-items-center justify-content-center">
            <img class="img-fluid" src="/src/assets/taxi-man-tired-of-studying.png" />
          </div>
        </div>
        <div v-else>
          <p class="display-3">실습 타이머</p>
          <div style="height: 400px" class="d-flex align-items-center justify-content-center">
            <img
              class="img-fluid"
              src="/src/assets/taxi-coming-soon-text-with-a-waiting-man-lettering.png"
            />
          </div>
        </div>
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
            <!-- TODO : 컴포넌트화 -->
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
  transition: opacity 1s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
