<script setup lang="ts">
import axios from "axios";
import { ref } from "vue";

const data = ref();

const fetchData = async () => {
  try {
    const res = await axios.get(
      "https://jsonplaceholder.typicode.com/todos/99999"
    );
    data.value = res.data;
  } catch (err) {
    throw new Error();
  }
};

// 특이점 : Suspense로 감싼 컴포넌트에서는 top-level await을 사용할 수 있다.
await fetchData();
</script>

<template>
  <div>Suspense A</div>
  <div>{{ data.title }}</div>
</template>

<style scoped></style>
