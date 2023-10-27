<template>
  <div>
    <label :for="id" class="form-label">{{ label }}</label>
    <h3>{{ modelValue }}분</h3>
    <input
      type="range"
      class="form-range"
      :id="id"
      :min="min"
      :max="max"
      step="1"
      :value="modelValue"
      @change="updateValue"
    />
  </div>
</template>

<script setup>
const { id, label, min, max, modelValue } = defineProps([
  "id",
  "label",
  "min",
  "max",
  "modelValue",
]);
const emit = defineEmits();

const updateValue = (event) => {
  emit("update:modelValue", event.target.value);
};
</script>
