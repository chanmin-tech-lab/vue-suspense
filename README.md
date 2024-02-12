# Vue에서 Suspense 사용하기

### 기록

React와 마찬가지로 Vue에서도 [Suspense](https://ko.vuejs.org/guide/built-ins/suspense#suspense)를 사용할 수 있다. (다만 아직은 Experimental 딱지가 붙어 있다.)

React에 비해 사용하기 쉬운 구조를 제공하지만, 조건부로 렌더링되는 컴포넌트에서 오류가 발생하는 경우가 pitfall이 될 수 있을 것 같았다.

```
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
 * 1. Suspense 내에서 조건부 렌더링 패턴을 지양한다
 * 2. 에러바운더리를 구현한다
 * 3. 타임아웃을 설정한다. (Ex. pending 상태에서 1s동안 변화가 없으면 에러로 간주한다)
 */
```

#default 슬롯의 컴포넌트를 에러바운더리로 감싸 해결할 수도 있을 것 같은데, 슬롯과 관련된 문제일 것 같다...

```
 <Suspense @pending="pending" @resolve="resolve" @fallback="fallback">
    <template #default>
      <ErrorBoundary> // <-- Error (#default 내에서 추가적인 슬롯을 활용할 수 없는 것 같다.)
        <component
          :is="showComponentA ? SuspenseComponentA : SuspenseComponentB"
        />
      </ErrorBoundary>
    </template>
    <template #fallback>
      <div v-if="error">오류가 발생했습니다</div>
      <div v-else>로딩 중...</div>
    </template>
```

```
vue.js?v=6407d98c:9605 Uncaught (in promise) TypeError: Failed to execute 'insertBefore' on 'Node': parameter 1 is not of type 'Node'.
```

### 기대효과

- 선언적인 코드와는 별개로, Vue에서 제공하는 `<Transition>` 컴포넌트와 궁합이 매우 잘 맞을 것 같았다.
- 로딩용 스켈레톤을 보여주다가 데이터 컴포넌트로 자연스럽게 트랜지션되는 경험, 상상만 해도 멋지다!

```
<RouterView v-slot="{ Component }">
  <template v-if="Component">
    // v-if에 비해 선언적으로 Fallback / default UI를 지정할 수 있다.
    // Fallback UI 컴포넌트와 데이터 컴포넌트간 UI를 부드럽게 전환할 수도 있다.
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <!-- 메인 컨텐츠 -->
          <component :is="Component"></component>

          <!-- 로딩 상태 -->
          <template #fallback>
            로딩중...
          </template>
        </Suspense>
      </KeepAlive>
    </Transition>
  </template>
</RouterView>
```
