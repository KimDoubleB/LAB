### 원시 HTML

```jsx
<template>
  <div>
    <div v-html="rawHTML"></div>
    <div v-html="xss"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rawHTML: '<div style=""color:red;">12345</div>',
      xss: `<iframe onload="alert('ATTACK !')"></iframe>`
    }
  }
}

</script>
```

- rawHTML에 직접 HTML 코드를 작성하고, 이를 v-html에 전해주면 바로 HTML을 보여준다.
- 하지만 [XSS 취약점](https://www.leafcats.com/42) 때문에 외부에서 HTML 코드를 가져와 보여주는 것은 매우 주의해야한다고 함.
    - 사용자가 입력한 바를 활용해서 HTML코드로 바꿔 이걸 보여주는 용도로 하게 되면, 악성 스크립트를 담아 사용자가 보내게되서 이게 HTML로 변환되어 다른 사용자에게 악성 스크립트를 제공하게 되는 문제
- 보통 백엔드에서 사용자로부터 데이터를 받을 때, 이러한 악성 스크립트 태그를 다 없애주는 작업을 수행하고 저장한다고 함.

### 동적 전달인자

태그의 속성도 데이터를 받아 적용이 가능하다.

```jsx
<template>
  <div>
    <div :[attrName]="message">
      {{ number }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      number: 2,
      message: 'This is title !',
      attrName: 'title'
    }
  }
}
</script>
```

- 이렇게 아예 속성이름을 데이터로 받아 속성을 설정할 수 있다.
    - 여기서는 title 속성을 데이터로 받아 적용했다.
- 뿐만 아니라 이벤트 이름 (click, ...) 등도 데이터로 바꿔가며 적용이 가능하다.

### 약어

- v-bind: 속성

```jsx
<!-- 전체 문법 -->
<a v-bind:href="url"> ... </a>

<!-- 약어 -->
<a :href="url"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a :[key]="url"> ... </a>
```

- v-on: 이벤트

```jsx
<!-- 전체 문법 -->
<a v-on:click="doSomething"> ... </a>

<!-- 약어 -->
<a @click="doSomething"> ... </a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

각자 2번째에 존재하는 예제를 가장 많이 사용하고, 우리도 이걸 사용하기로 하자고 함.
