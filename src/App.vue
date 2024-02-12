<script setup lang="ts">
import { onErrorCaptured, ref } from "vue";
import SuspenseComponentA from "./components/SuspenseComponentA.vue";
import SuspenseComponentB from "./components/SuspenseComponentB.vue";

const showComponentA = ref(true);
const error = ref(false);

onErrorCaptured(() => {
  error.value = true;
});

/**
 * Suspense의 생명주기는 pending - fallback - resolve 로 이루어진다.
 *
 * - 중요한 점
 * fallback 상태는 최초에만 존재한다.
 * <component :is="showComponentA ? SuspenseComponentA : SuspenseComponentB" /> 처럼 컴포넌트를 스위칭할 때는
 * pending - fallback - resolve 이 아니라 pending - resolve 상태만 존재하게 된다.
 *
 * 따라서 만약 SuspenseComponentA 컴포넌트에서 데이터 페칭에 실패하게 되면 (= resolve하지 못하면) fallback UI가 노출되는 것이 아니라 Pending 상태에서 넘어갈 수 없다.
 *
 * - 해결책
 * 1. Suspense 내에서 조건부 렌더링 패턴을 지양한다?
 * 2. 에러바운더리를 구현한다
 * 3. 타임아웃을 설정한다. (Ex. pending 상태에서 1s동안 변화가 없으면 에러로 간주한다)
 */

const pending = () => {
  console.log("pending");
};

const resolve = () => {
  console.log("resolve");
};

const fallback = () => {
  console.log("fallback");
};

const toggleComponent = () => {
  showComponentA.value = !showComponentA.value;
};
</script>

<template>
  <main>
    <button @click="toggleComponent">모드 변경</button>
    <Suspense @pending="pending" @resolve="resolve" @fallback="fallback">
      <template #default>
        <!-- <ErrorBoundary> -->
        <!-- <SuspenseComponentA v-if="showComponentA" />
      <SuspenseComponentB v-else /> -->
        <component
          :is="showComponentA ? SuspenseComponentA : SuspenseComponentB"
        />
        <!-- </ErrorBoundary> -->
      </template>
      <template #fallback>
        <div v-if="error">오류가 발생했습니다</div>
        <div v-else>로딩 중...</div>
      </template>
    </Suspense>
  </main>
</template>

<!-- <component :is="condition === A ? ComponentA : ComponentB" /> -->

<!-- <ComponentA v-if="condition === A" />
<ComponentB v-else /> -->
