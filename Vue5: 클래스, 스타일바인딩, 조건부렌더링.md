
## 클래스와 바인딩

### HTML 클래스 바인딩 - 객체 구문

계속해서 예제를 보았을 때 `v-bind:class` 를 통해서 class를 값에 따라 추가를 하고, 말고를 할 수 있었다.

ex)

```jsx
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>

=================================================

data: {
  isActive: true,
  hasError: false
}
```

위 상황이면, 렌더링 될 때 결과적으로 `<div class="static active"></div>` 로 렌더링 되게 된다.

하지만 이 구조는 v-bind:class 부분이 엄청 길어지게 된다.

그래서 이걸 아예 객체 데이터로써 한번에 표현할 수 있다.

```jsx
<div v-bind:class="classObject"></div>

=================================================

data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

이렇게 한번에 표현이 가능하고, 결과적으로 `<div v-bind:class="classObject"></div>` 로 렌더링 된다.

> 혹시) 위 두 예제에서 text-danger만 ' ' 로 묶여 있는데, 이건 '-' 특수문자가 들어가게 되어 그냥 작성하면 인식을 하지 못하기 떄문에 그렇다. 특수문자가 들어가는 클래스명인 경우, ' ' 로 묶여줘야 한다.

**이를 좀 더 유지보수성을 높이고, error에 대한 데이터로 active, test-danger를 표현해보면 다음과 같다**

```jsx
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

- 변수명이 좀더 명확해졌고, 그 변수명에 따라 클래스가 추가될지/말지를 computed로 계산되어 넣게 된다.
- 똑같이 html에서는 `v-bind:class=classObject` 를 사용하면 된다.

객체 구문 말고도 배열 구문이 있는데, 많이 사용을 안한다고 한다. → 필요하면 문서를 찾아볼 것.

### 인라인 스타일 바인딩

직접 태그에 대한 스타일을 설정하는 것을 인라인 스타일이라고 배웠었다. - 그 스타일 지정방식 중 제일 우선순위가 높았던 것 (important 를 제외하고)

- 예) `<div style="color: red"></div>`

인라인 스타일 바인딩도 이와 비슷하게 작성해서 인라인 스타일이라는 이름을 쓰게 된 것이다.

- 전체적인 문법은 이전 HTML 클래스 바인딩과 유사하나 `v-bind:style` 로 사용한다.

```jsx
<div v-bind:style="styleObject"></div>

==================

data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

유의할 점이라고 하면 data로 값을 줄 때 스타일 키 값을 camelCase로 준다는 것이다.

- style에서는 `font-size`를 사용하지만 javascript 문법임으로 이걸 위에서 보는 것처럼 `fontSize`로 쓴다.

## 조건부 렌더링

`v-if` `v-else-if` `v-else` 로 if문 구절처럼 사용할 수 있다.

```jsx
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else-if="handsome">Vue is handsome!</h1>
<h1 v-else>Oh no 😢</h1>
```

- 주의해야하는 점은 v-if, v-else-if, v-else 사이에 다른 요소가 들어가서는 안된다. 둘은 인접 형제요소여야만 한다.

여기서 알아둘 것이 `v-if`, `v-else-if`, `v-else` 가 있을 때, 만약 false 값이라면 보이지 않는다는 것은 자명하다. 근데, 보이지 않는 다는 것을넘어서 false라면 그 요소를 아예 없어진다.

- 개발자 도구로 열어서 보면 false인 경우, 아예 렌더링이 되지 않는다.

이러한 특성으로 인해서 장점과 단점이 존재한다.

- 장점: 초기 렌더링 비용이 낮다. → 렌더링 할 필요가 없는 건 아예 그리지 않으면 되므로
- 단점: 반복 렌더링 비용이 높다. → true/false를 자주 왔다갔다하는 경우, 렌더링을 새로 했다/지웠다를 반복해야므로 이 비용이 높아지게 된다. 단순히 스타일을 수정하는게 아니라 요소 자체를 만들고, 지웠다를 반복하는 것이므로.

이러한 반복 렌더링의 단점을 해결하기 위해 `v-show` 가 나왔다.

- 문법은 `v-if` 와 같다.

```jsx
<h1 v-show="ok">안녕하세요!</h1>
```

`v-show` 는 false인 경우, 그 요소를 직접 지우는 것이 아니라 `display=none` 속성을 준다.

- 즉, 렌더링은 하는데 보이질 않게 만들어놓고 토글버튼 식으로 display 속성으로 보여줬다 말았다를 할 수 있는 것이다.

`v-show` 의 장단점은 `v-if` 와 반대라고 보면 된다.

- 초기 렌더링은 필요해서 초기 렌더링 비용이 올라갈 수는 있으나, 반복 렌더링은 단순히 스타일 속성 값만을 왔다갔다 하는 것이기에 반복 렌더링 비용은 적다.
- 즉, 토글... 보여줬다 말았다 하는 값이 자주 변하는 상황이라면. 다시 말해서 유저에게 보여줬다 안보여줬다가 자주 바뀌는 상황이라면 `v-show` 를 사용해서 반복 렌더링 비용을 줄일 수 있는 것이다.

 

이걸 정리하자면, 통상적으로는 `v-if` 를 사용하자. 하지만 만약 토글을 진짜 많이 하는 요소다? 라고 하면 그 땐 `v-show` 를 사용하자.
