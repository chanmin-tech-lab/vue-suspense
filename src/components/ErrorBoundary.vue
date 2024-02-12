<script setup lang="ts">
import { nextTick, onErrorCaptured, ref, watchEffect } from "vue";
const hasError = ref(false);

onErrorCaptured(() => {
  hasError.value = true;
});

nextTick(() => {
  hasError.value = false;
});

watchEffect(() => {
  console.log(hasError.value);
});
</script>

<template>
  <div>
    <slot></slot>
  </div>
  <!-- <slot v-if="!hasError"></slot>
    <slot v-else name="error"></slot> -->
</template>

<style scoped></style>
