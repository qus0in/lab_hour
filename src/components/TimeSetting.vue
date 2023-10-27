<template>
    <div class="col-6">
        <div class="row mt-4">
            <div class="col-md p-2">
                <TimeRange id="studyTimeMinute" label="실습 시간" min="1" max="60" :model-value="studyTimeMinute"
                    @change="updateStudyTimeMinute" />
            </div>
            <div class="col-md p-2">
                <TimeRange id="restTimeMinute" label="쉬는 시간" min="1" max="30" :model-value="restTimeMinute"
                    @change="updateRestTimeMinute" />
            </div>
        </div>
        <div class="row mt-4">
            <div class="col-md">
                <button type="button" class="btn btn-outline-primary m-2" @click="updateModeStudy">
                    실습 시간 시작
                </button>
            </div>
            <div class="col-md">
                <button type="button" class="btn btn-outline-success m-2" @click="updateModeRest">
                    쉬는 시간 시작
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { TimerMode } from "../util";
import TimeRange from "./TimeRange.vue";
const { studyTimeMinute, restTimeMinute, mode } = defineProps([
    "studyTimeMinute",
    "restTimeMinute",
    "mode",
]);
const emit = defineEmits([
    "update:studyTimeMinute",
    "update:restTimeMinute",
    "update:mode",
]);

const updateStudyTimeMinute = (event) => {
    emit("update:studyTimeMinute", event.target.value);
};
const updateRestTimeMinute = (event) => {
    emit("update:restTimeMinute", event.target.value);
};
const updateModeStudy = (event) => {
    emit("update:mode", TimerMode.STUDY);
};
const updateModeRest = (event) => {
    emit("update:mode", TimerMode.REST);
};
</script>
