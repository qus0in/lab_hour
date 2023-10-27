<template>
  <div>
    <p class="display-3">{{ label }}</p>
    <div
      style="height: 400px"
      class="d-flex align-items-center justify-content-center"
    >
      <img class="img-fluid" :src="'/src/assets/' + img" />
    </div>
    <div class="row d-flex justify-content-center">
      <div class="col-3">
        <div class="form-check form-switch">
          <input
            class="form-check-input"
            type="checkbox"
            :id="id"
            :checked="modelValue"
            @change="updateValue"
          />
          <label class="form-check-label" :for="id">반복 모드</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const { id, label, img, modelValue } = defineProps([
  "id",
  "label",
  "img",
  "modelValue",
]);
const emit = defineEmits();

const updateValue = (event) => {
  emit("update:modelValue", event.target.checked);
};
</script>
