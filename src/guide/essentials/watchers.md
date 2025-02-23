# 감시자 {#watchers}

## 기본 예제 {#basic-example}

계산된 속성은 계산되어 파생된 값을 선언적으로 사용할 수 있게 합니다.
그러나 상태 변경에 대한 반응으로 "사이드 이펙트"(예: DOM을 변경하거나 비동기 작업의 결과를 기반으로 다른 상태를 변경하는 것)를 수행해야 하는 경우가 있습니다.

<div class="options-api">

옵션 API를 사용하는 경우, [`watch` 옵션](/api/options-state.html#watch)을 사용하여 반응형 속성이 변경될 때마다 함수를 실행할 수 있습니다:

```js
export default {
  data() {
    return {
      question: '',
      answer: '질문에는 일반적으로 물음표가 포함됩니다.',
      loading: false
    }
  },
  watch: {
    // 질문이 변경될 때마다 이 함수가 실행됩니다
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.loading = true
      this.answer = '생각 중...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer === 'yes' ? '네' : '아니오'
      } catch (error) {
        this.answer = '에러! API에 연결할 수 없습니다. ' + error
      } finally {
        this.loading = false
      }
    }
  }
}
```

```vue-html
<p>
  예/아니오 질문:
  <input v-model="question" :disabled="loading" />
</p>
<p>{{ answer }}</p>
```

[온라인 연습장으로 실행하기](https://play.vuejs.org/#eNp9VEtP20AQ/itTX5yoYKviFgUQrTjQQ6GPoy+WvSamztr1rklQFAlUVKUEqSA1JQciUclVW4lDRKnUA/1D2c1/6PiZQBGSJe889pv55rEdZS0ItN2IKDWlzqzQDfiKQUk78EMONnHMyOPQMSiAbXKzUs3OACHhUUgLCeBdRBh3fVoDVV0olCZlLRKiSn7vics/8uxEHH0GOboR46G8OJDnN+LrOYjLGzk6np6eT8b7MP10OR38ECc/Rb8n+rE2A/N803bpdg0c02MkV3eTXzcVWia3GrUiI12HPOjoGsSv/cnVX3FyAWJwLFDdjzGLa8BIsjdMwsp+PP3ysQybYRScKpS0XubnBfA9uxDKagC4Dsy7aS61SXvTqairahVWYPHJnC8Ab7hM2yZ8LS1QpVpYUjq3WDUJb/g2K3mZbI9aMHd1BpuC5lWCZeBhRG6Zsm6gRZXvR5PxAcj4VNM0tXQK9+ZztHzKOPaZ4Q2zZbocHIIVrqgNzgNW0/U9wqivtbijm4GrlhzuRqtklxFI22FYzGq1tC1jLoiiwiqo4jBWIRmVwSH2QA7jMq8uWElroULC0A//r+Mcr2Esvg0fwdrWBs4ayLPx5Go8HVwAdhmlD/Lodz5WoMJjSPFmURyXmp53qwZ3SppO3r29Mih+db1cIBQ4aQaeyQlKAPUg/QHIYU8vKeYjWstMdZcGEYfdxaZvE2/ZUIr5MxTQMxQ9g0G0TiffLuh2M3VdLyMqCwpn2D/H3U5rjrudkjIUy28GrkfCzSABZoZSDpahIHm/9TzVJbOTbxjeaRDr7T36HdZOdIayhb0l4S4xlNLGzRBnNDOvv35B2ngujcgv8tD7AeMrwnwvysgnbk8jamPac35pthvN5J3C5rxh621OKCtIFcOfLZGh4AP37AHqs3SXtKWioUr3H/+H2xc=)

`watch` 옵션은 점으로 구분된 경로도 지원합니다:

```js
export default {
  watch: {
    // 참고: 단순 경로만 가능합니다. 표현식은 지원되지 않습니다.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}
```

</div>

<div class="composition-api">

Composition API를 사용하는 경우, [`watch` 함수](/api/reactivity-core.html#watch)를 사용하여 반응형 속성이 변경될 때마다 함수를 실행할 수 있습니다:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('질문에는 일반적으로 물음표가 포함됩니다.')
const loading = ref(false)

// watch는 ref에서 직접 작동합니다
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = '생각 중...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer === 'yes' ? '네' : '아니오'
    } catch (error) {
      answer.value = '에러! API에 연결할 수 없습니다. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    예/아니오 질문:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[온라인 연습장으로 실행하기](https://play.vuejs.org/#eNplkkGPmzAQhf/KKxdA3Rj1mpJUUdVDT22lHrlYxDRuYOzaJjRC/PcdxyGr3b2A7PfmmzcMc3awVlxGlW2z2rdO2wCvwmj3DenBGhcww6nuCZMM7QkLOmcG5FyRN9RQa8gH/BuVD9oQdtFb5Hm5KpL8pNx6/+vu8xj9KPv+CnYFqQnyhTFIdxb4vCkjpaFb32JVnyD9lVoUpKaVVmK3x9wQoLtXgtB0VP9/cOMveYk9Np/K5MM9l7jIflScLv990nTW9EcIwXNFR3DX1YwYk4dxyrNXTlIHdCrGyk8hWL+tqqvyZMQUukpaHYOnujdtilTLHPHXGyrKUiRH8i9obx+5UM4Z98j6Pu23qH/AVzP2R5CJRMl14aRw+PldIMdH3Bh3bnzxY+FcdZW2zPvlQ1CD7WVQfALquPToP/gzL4RHqsg89rJNWq3JjgGXzWCOqt812ao3GaqEqRKHcfO8/gDLkq7r6tEyW54Bf5TTlg==)

### 감시 대상 타입 {#watch-source-types}

`watch`의 첫 번째 인자는 다양한 유형의 반응형 "소스"일 수 있습니다. 참조(계산된 참조 포함), 반응형 객체, 게터 함수 또는 여러 소스의 배열이 될 수 있습니다:


```js
const x = ref(0)
const y = ref(0)

// 단일 ref
watch(x, (newX) => {
  console.log(`x값: ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`x + y: ${sum}`)
  }
)

// 여러 소스의 배열
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x는 ${newX}이고, y는 ${newY} 입니다.`)
})
```

다음과 같이 반응형 객체의 속성을 감시할 수는 없습니다:

```js
const obj = reactive({ count: 0 })

// 이것은 watch()에 숫자를 전달하기 때문에 작동하지 않습니다.
watch(obj.count, (count) => {
  console.log(`count 값: ${count}`)
})
```

대신 getter를 사용하십시오:

```js
watch(
  () => obj.count,
  (count) => {
    console.log(`count 값: ${count}`)
  }
)
```

</div>

## 깊은 감시자 {#deep-watchers}

<div class="options-api">

`watch`는 기본적으로 얕습니다. 콜백은 감시되는 프로퍼티에 새 값이 할당되었을 때만 트리거되며 중첩된 프로퍼티 변경에는 트리거되지 않습니다. 중첩된 모든 변경에 대해 콜백이 실행되도록 하려면 심층 감시자를 사용해야 합니다:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // 참고:
        // someObject가 다른 객체로 교체되지 않는 한,
        // newValue와 oldValue는 같습니다.
        // 둘 다 동일한 객체를 참고하고 있기 때문입니다!
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

반응형 객체에서 `watch()`를 직접 호출하면 암시적으로 심층 감시자가 생성되며, 콜백은 중첩된 모든 변경에서 트리거됩니다:

```js
const someObject = reactive({ count: 0 })

watch(someObject, (newValue, oldValue) => {
  // 중첩된 속성 변경사항이 있을 경우 실행됩니다.
  // 참고:
  // `newValue`와 `oldValue`는 같습니다.
  // 둘 다 동일한 객체를 참고하고 있기 때문입니다!
})

someObject.count++
```
반응형 객체를 반환하는 게터와 구별해야 합니다. 후자의 경우 콜백은 게터가 다른 객체를 반환하는 경우에만 실행됩니다:

```js
const state = reactive({
  someObject: { count: 0 }
})

watch(
  () => state.someObject,
  () => {
    // state.someObject가 교체될 때만 실행됩니다.
  }
)
```

그러나 `deep` 옵션을 명시적으로 사용하여 두 번째 경우(위 예제)를 깊은 감시자로 강제할 수 있습니다:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // 참고: 
    // state.someObject가 교체되지 않는 한 여기에서
    // `newValue`와 `oldValue`는 같습니다.
  },
  { deep: true }
)
```

</div>

:::warning 사용 시 주의사항
깊은 감시는 감시된 객체의 모든 중첩 속성을 탐색하므로, 큰 데이터 구조에서 사용할 때 비용이 많이 들 수 있습니다.
성능에 영향을 주는지 고려해서 필요한 경우에만 사용하십시오.
:::

## 열성적인 감시자 {#eager-watchers}

`watch`는 기본적으로 게으릅니다(lazy).
콜백은 감시된 소스가 변경되기 전까지 호출되지 않습니다.
그러나 어떤 경우에는 동일한 콜백 로직이 열성적으로 실행되기를 원할 수 있습니다.
예를 들어 최초 데이터가 구성된 후 콜백이 실행되기를 원할 수 있습니다.

<div class="options-api">
`handler` 함수와 `immediate: true` 옵션으로 구성된 객체를 사용해 감시자를 선언함으로써 콜백이 즉시 실행되도록 할 수 있습니다:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // 이제 컴포넌트 생성 시
        // `beforeCreate`와 `created` 훅 사이에
        // 한 번 실행됩니다.
      },
      // 열성적으로 콜백 실행
      immediate: true
    }
  }
  // ...
}
```


핸들러 함수의 초기 실행은  `created` 훅 직전에 발생합니다. Vue는 이미 `data`, `computed`, `method` 옵션을 처리했을 것이므로 첫 번째 호출에서 해당 속성을 사용할 수 있습니다.

</div>

<div class="composition-api">

`immediate: true` 옵션을 전달하여 워처의 콜백이 즉시 실행되도록 강제할 수 있습니다:

```js
watch(
  source,
  (newValue, oldValue) => {
    // 즉시 실행된 다음 `source`가 변경되면 다시 실행됩니다.
  },
  { immediate: true }
)
```

</div>


<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

워처 콜백이 소스와 정확히 동일한 반응형 상태를 사용하는 것은 일반적입니다. 예를 들어, 다음 코드를 고려해 보세요. 이 코드는 `todoId` ref가 변경될 때마다 원격 리소스를 로드하기 위해 워처를 사용합니다:

```js
const todoId = ref(1)
const data = ref(null)

watch(
  todoId,
  async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    data.value = await response.json()
  },
  { immediate: true }
)
```

특히 워처가 `todoId`를 소스로 한 번, 콜백 내에서 다시 한 번 사용하는 것에 주목하세요.

이는 [`watchEffect()`](/api/reactivity-core#watcheffect)로 단순화될 수 있습니다. `watchEffect()`는 콜백의 반응형 종속성을 자동으로 추적할 수 있습니다. 위의 워처는 다음과 같이 다시 작성될 수 있습니다:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

여기서, 콜백은 즉시 실행되며, `immediate: true`를 명시할 필요가 없습니다. 실행 중에 자동으로 `todoId.value`를 종속성으로 추적합니다(계산된 속성과 유사하게). `todoId.value`가 변경될 때마다 콜백이 다시 실행됩니다. `watchEffect()`를 사용하면 `todoId`를 소스 값으로 명시적으로 전달할 필요가 없습니다.

`watchEffect()`와 반응형 데이터 가져오기가 작동하는 [이 예제](/examples/#fetching-data)를 확인해 보세요.

이러한 예제에서, 단일 종속성만 있는 경우, `watchEffect()`의 이점은 상대적으로 작습니다. 그러나 여러 종속성을 가진 워처의 경우, `watchEffect()`를 사용하면 종속성 목록을 수동으로 유지하는 부담을 없앨 수 있습니다. 또한, 중첩된 데이터 구조에서 여러 속성을 감시해야 하는 경우, `watchEffect()`는 깊은 워처보다 더 효율적일 수 있습니다. 콜백에서 사용되는 속성만 추적하는 반면, 모든 속성을 재귀적으로 추적하는 것이 아니기 때문입니다.

:::tip
`watchEffect`는 **동기적** 실행 중에만 종속성을 추적합니다. 비동기 콜백을 사용할 때는 첫 번째 `await` 틱 전에 접근한 속성만 추적됩니다.
:::

### `watch` vs. `watchEffect` {#watch-vs-watcheffect}

`watch`와 `'watchEffect`' 둘 다 사이드 이펙트를 반응적으로 실행할 수 있게 해줍니다.
주요 차이점은 반응형 의존성을 추적하는 방식입니다:

- `watch`는 명시적으로 감시된 소스만 추적합니다.
  콜백 내에서 조회하는 항목은 추적하지 않습니다.
  또한 콜백은 소스가 실제로 변경된 경우에만 트리거됩니다.
  `watch`는 의존성 추적을 사이드 이펙트와 분리하여, 콜백이 실행되어야 하는 시기를 보다 정확하게 제어할 수 있습니다.

- 반면 `watchEffect`는 의존성 추적과 사이드 이펙트를 하나의 단계로 결합합니다.
  동기적(sync) 실행 중에 조회되는 모든 반응형 속성을 자동으로 추적합니다.
  이것은 더 편리하고 일반적으로 더 간결한 코드를 생성하지만, 콜백이 실행되어야 하는 시기가 덜 명시적입니다.

</div>

## 콜백 실행 타이밍 {#callback-flush-timing}

반응형 상태를 변경하면 Vue 컴포넌트 업데이트와 사용자가 만든 감시자 콜백이 모두 실행됩니다.

컴포넌트 업데이트와 유사하게, 사용자가 만든 감시자(watcher) 콜백은 중복 호출을 방지하기 위해 일괄 처리됩니다. 예를 들어, 감시하고 있는 배열에 동기적으로 천 개의 항목을 푸시할 때 감시자가 천 번 실행되는 것을 원하지 않을 것입니다.

기본적으로, 감시자의 콜백은 부모 컴포넌트 업데이트가 있을 경우 **이후에** 호출되고, 소유 컴포넌트의 DOM 업데이트 **이전에** 호출됩니다. 이는 감시자 콜백 내에서 소유 컴포넌트의 DOM에 접근하려고 할 때, DOM이 업데이트 전 상태에 있을 것임을 의미합니다.

### 후처리 감시자 {#post-watchers}

Vue가 업데이트한 후에 감시자 콜백에서 소유 컴포넌트의 DOM에 접근하고자 한다면, `flush: 'post'` 옵션을 지정해야 합니다:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'post'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```

`flush: 'post'` 옵션이 적용된 `watchEffect()`를 보다 간편하게 사용하기 위해서 `watchPostEffect()`를 사용할 수 있습니다:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* Vue가 업데이트 된 후 실행됩니다 */
})
```

</div>

기본적으로 `flush: 'pre'|'post'` 옵션은 콜백을 버퍼링하여,
동일한 "틱(tick)"에서 여러 번 상태 변경이 되더라도,
마지막에 한 번만 호출됩니다.

동일한 틱 내에 여러 번 상태 변경 시 마다 동기적으로 콜백을 호출해야 하는 경우,
`flush: 'sync'` 옵션을 사용해야 합니다.
단, 일반적으로 이러한 동작은 비효율적이므로 사용하려는 경우,
정말 필요한지 다시 한번 고민해봐야 합니다.

<div class="options-api">

```js
export default {
  data: () => ({ count: 0 }),
  watch: {
    count: {
      handler(val, preVal) {
        console.log('변경이 감지됨!', val, preVal)
      },
      flush: 'sync'
    }
  },
  methods: {
    increment() {
      this.count++
      // 이어서 callback이 실행됨
      this.count++
      // 역시 callback이 실행됨
      this.count++
      // 또 callback이 실행됨
    }
  }
}
```

</div>

<div class="composition-api">

```js
const count = ref(0)
const callback = (val, preVal) => console.log('변경이 감지됨!', val, preVal)
const options = { flush: 'sync' }

watch(count, callback, options)

count.value++
// 이어서 callback이 실행됨
count.value++
// 역시 callback이 실행됨
count.value++
// 또 callback이 실행됨
```

</div>

### 동기적 감시자 {#sync-watchers}

Vue에서 동기적으로 실행되는 감시자(watcher)를 만드는 것도 가능합니다. 이 감시자는 Vue가 관리하는 업데이트 이전에 동기적으로 발동됩니다:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'sync'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'sync'
})

watchEffect(callback, {
  flush: 'sync'
})
```

동기적 `watchEffect()`는 또한 `watchSyncEffect()`라는 편리한 별칭도 가지고 있습니다:

```js
import { watchSyncEffect } from 'vue'

watchSyncEffect(() => {
  /* 반응형 데이터 변경 시 동기적으로 실행됨 */
})
```

</div>

:::warning 주의해서 사용하세요
동기적 감시자는 배치 처리가 없으며, 반응형 변경이 감지될 때마다 트리거됩니다. 간단한 boolean 값들을 감시하는 데 사용하는 것은 괜찮지만, 예를 들어 배열과 같이 동기적으로 여러 번 변경될 수 있는 데이터 소스에는 사용을 피해야 합니다.
:::

<div class="options-api">

## `this.$watch()` * {#this-watch}

[`$watch()` 인스턴스 메서드](/api/component-instance.html#watch)를 사용하여 감시자를 선언적으로 생성할 수도 있습니다.

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

이는 감시자를 조건부로 설정해야 하거나, 사용자 상호 작용에 대한 응답으로만 무언가를 감시해야 할 때 유용합니다.
또한 감시자를 조기에 중지할 수 있습니다.

</div>

## 감시자 중지하기 {#stopping-a-watcher}

<div class="options-api">

`watch` 옵션이나 `$watch()` 인스턴스 메서드를 사용하여 선언된 감시자는 해당 컴포넌트가 마운트 해제될 때 자동으로 중지되므로 대부분의 경우 감시자를 직접 중지하는 것에 대해 고민할 필요가 없습니다.

드물게 해당 컴포넌트가 마운트 해제되기 전에 감시자를 중지해야 하는 경우를 위해 `$watch()` API는 이 기능을 수행할 수 있게 함수를 반환합니다:

```js
const unwatch = this.$watch('foo', callback)

// ...나중에 감시자가 더 이상 필요하지 않을 때:
unwatch()
```

</div>

<div class="composition-api">

`setup()` 또는 `<script setup>` 내부에서 동기적으로 선언된 감시자는 해당 컴포넌트 인스턴스에 바인딩되며, 해당 컴포넌트가 마운트 해제되면 자동으로 중지됩니다.
대부분의 경우 감시자를 직접 중지하는 것에 대해 고민할 필요가 없습니다.

여기서 핵심은 감시자가 **동기적**(synchronously)으로 생성되어야 한다는 것입니다.
감시자가 비동기 콜백에서 생성된 경우, 감시자는 해당 컴포넌트에 바인딩되지 않으며 메모리 누수를 방지하기 위해 수동으로 중지해야 합니다.
다음은 이러한 케이스에 해당하는 예제입니다:

```vue
<script setup>
import { watchEffect } from 'vue'

// 이 감시자는 컴포넌트가 마운트 해제되면 자동으로 중지됩니다.
watchEffect(() => {})

// ...하지만 이것은 자동으로 중지되지 않습니다.
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

감시자를 수동으로 중지하려면 반환된 함수를 사용하십시오.
이것은 `watch`와 `watchEffect` 모두에서 작동합니다:

```js
const unwatch = watchEffect(() => {})

// ...나중에 감시자가 더 이상 필요하지 않을 때:
unwatch()
```

감시자를 비동기식으로 생성해야 하는 경우는 거의 없으며, 가능하면 동기식 생성을 해야 합니다.
일부 비동기 데이터를 기다려야 하는 경우, 감시자 로직을 조건부로 만들 수 있습니다:

```js
// 비동기적으로 로드할 데이터
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // 데이터가 로드될 때 실행될 로직
  }
})
```

</div>
