<script setup lang="ts">
import { ref } from "vue";

const data = ref();

const fetchData = async () => {
  await fetch("https://jsonplaceholder.typicode.com/todos/1")
    .then((response) => {
      // fetch는 response.ok 를 통해 오류 여부를 확인한다.
      // https://chat.openai.com/share/12c6dfd5-6fe8-4a60-97da-6e8d2f1f6652
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      return response.json();
    })
    .then((json) => (data.value = json))
    .catch((err) => {
      console.log(err);
    });
};
await fetchData();
</script>

<template>
  <div>Suspense B</div>
  <div>{{ data.title }}</div>
</template>
