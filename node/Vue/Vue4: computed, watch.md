
## computed, watch

### computed

{{ }} 템플릿 안에 자바스크립트 연산식을 넣어서 연산이 가능하다.

- 예제로 `{{ message.split('').reverse().join('') }}` 가 있다.
- 하지만 근데 이렇게 복잡해지는 연산들은 사용하면 나중에 유지보수하기 힘들다. 별로 안좋다고 함.
- 그래서 computed 속성을 사용하라고 함.

**예제**

- fruits 라는 배열 데이터를 받았다고 하자. 요소로 과일 스트링들이 들어있다.
    - 이걸 v-for로 돌리려는데, 데이터에 아이디 값이 없다. 이걸 백엔드한테 모든걸 요청할 수는 없다.
    - 그럼 이걸 데이터를 고쳐야하는데, 어떻게 할까?
- 이럴 때 computed를 사용한다.
    - 기존 변수가 있는데, 그 변수를 새로 연산을 수행해 사용할 것이다. 하는 것.
    - 약간 기존의 변수의 데이터를 변형한 **새로운 변수**를 만드는 것이라고 생각하면 될 듯.
    - map 연산 등 배열, 객체 연산과 찰떡으로 사용 될 수 있을 것 같다.
- map( (value, index) ⇒ () )
    - 리스트 데이터일 때, (value, index)로 하면 기존 값은 value로 매칭되고 그것에 맞게 인덱스가 자동으로 들어가게 된다. 문법 유의 !

```jsx
<template>
  <div>
    <ul>
      <li
        v-for="fruit in computedFruits"
        :key="fruit.id">
        {{ fruit.name }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      fruits: [
          'Apple',
          'Banana',
          'Cherry'
      ]
    }
  },
  computed: {
    computedFruits() {
      return this.fruits.map((f, i) => ({id: i, name: f}))
    }
  }
}
</script>
```

computed가 빛을 발하는 부분은 데이터가 바뀜에 따라 computed로 정의된 데이터들도 자동으로 바뀐다는 것이다. 반응성 있게 작업이 가능하다 !

```jsx
<template>
  <div>
    <div
      @click="changeMessage">
      {{ message }}
    </div>
    <div>
      {{ computedMessage }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: "HELLO !"
    }
  },
  computed: {
    computedMessage() {
      return this.message + '~ computed Message !';
    }
  },
  methods: {
    changeMessage () {
      this.message = "GOOD !"
    }
  }
}
</script>
```

- 위 예제에서 click을 하면 아래 computedMessage도 변하게 된다 !

### computed cache

공식문서에는 다음과 같임 명시되어 있다.

> computed 속성은 종속 대상을 따라 저장(캐싱)된다는 것 입니다. computed 속성은 해당 속성이 종속된 대상이 변경될 때만 함수를 실행합니다. 즉 message가 변경되지 않는 한, computed 속성인 reversedMessage를 여러 번 요청해도 계산을 다시 하지 않고 계산되어 있던 결과를 즉시 반환합니다.

- 즉, computed에서 연산하고 있는 데이터 자체가 수정되지 않는 한 computed는 연산을 한 번만 수행하고 호출을 계속 할지언정 연산을 수행하지 않는다는 것이다. 그냥 한 번 연산해둔 값을 계속 호출하게 되는 것.
    - 즉, 계산된 값을 그냥 이름지은 computed 변수에 결과를 넣어놓고 계속 활용한다는 말인 것 같다.

다음 예제를 보자.

```jsx
<template>
  <div>
    <div>{{ reverseMessage() }}</div>
    <div>{{ reverseMessage() }}</div>
    <div>{{ reverseMessage() }}</div>
    <div>{{ reverseMessage() }}</div>
    <div>=========================</div>
    <div>{{ reversedMessage }}</div>
    <div>{{ reversedMessage }}</div>
    <div>{{ reversedMessage }}</div>
    <div>{{ reversedMessage }}</div>
  </div>
</template>
<script>
export default {
  data (){
    return {
      message: 'hello bobae!'
    }
  },
  computed: {
    reversedMessage() {
      return this.message
          .split('')
          .reverse()
          .join('')
    }
  },
  methods: {
    reverseMessage() {
      return this.message
          .split('')
          .reverse()
          .join('')
    }
  }
}
</script>
```

- methods 와 computed는 똑같은 연산을 수행한다. 하지만, 내부적으로 효율성을 따져보면 확연히 다르다.
    - methods는 호출할 때마다 계속 연산을 수행
    - computed는 호출을 하면 첫 호출 시 (또는 첫 셋업시) 한번만 수행하고, 이걸 캐싱해놓고 계속 활용
- 즉, 데이터를 수정하는 또 이게 반복되는 코드라면 computed로 구현하여 효율성을 극대화시키는 것이 좋다.
- 앞서 이야기했지만, computed도 this로 참조하고 있는 본 데이터의 수정이 이루어지게 되면 다시 연산을 수행하여 캐싱해놓게 된다.
    - 만약 computed인데 해당 this를 활용하지 않는 경우 (즉, 데이터에 의존하지 않는 경우)라면 한 번만 수행하고 절대 업데이트 되지 않음을 뜻한다.

### computed setter

사실 위해서는 계산 된 값을 가져오는 용으로만 사용했다. 즉, getter 용으로만 사용했다고 할 수 있다.

하지만 computed는 setter로도 사용할 수 있다. → computed에서 정의했던 변수명?에 값을 넣게 되면 setter로 작동하게 된다.

그럼 getter, setter 동작을 다르게 어떻게 만들 것인가?

```jsx
computed: {
    reversedMessage: {
      get() {
        return this.message
            .split('')
            .reverse()
            .join('')
      },
      set(newValue) {
        this.message = newValue
      }
    }
  }
```

- 이렇게 getter, setter를 나누어 만들 수 있다.
    - 이전까지 만들었던 것은 자동으로 getter만 만들고 있었던 것
- reversedMessage에 데이터를 할당하게 되는 경우, 자동으로 setter가 동작하게 된다.

일반적인 상황에서는 이 setter가 사용되지 않는다. 하지만 vuex를 사용하게 되면 달라진다.

- vuex란 데이터를 한 저장소에서 활용하고 이걸 가져다가 사용하는 건데 (현재는 한 뷰파일에서 따로 데이터를 선언해 사용하고 있는 것과 반대), 가져다 사용하는 데이터에 할당하는 것은 허용이 안된다고 한다.
- 그래서 setter를 통해서 데이터 수정을 신청한다는 데 사실 와닿지 않는다 ㅎ하하.

### watch: 데이터를 감시하는 기능

data에 선언된 값을 지켜보면서 값이 수정되었을 때, 어떤 행위를 하겠어 라고 명시하는 것

- 그 안에서 값이 바뀌어서 새로운 값을 다시 대입하려하는? 그런건 안된다.

```jsx
<template>
  <div @click="reverseMessage">
    {{ message }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: 'Hello Bobae !'
    }
  },
  watch: {
    message () {
      console.log('MESSAGE CHANGED :', this.message)
    }
  },
  methods: {
    reverseMessage() {
      this.message = this.message
          .split('')
          .reverse()
          .join('')
    }
  }
}
</script>
```

코드를 보면 알겠지만 그 데이터의 이름의 함수를 만들면 된다.

- 그러면 그 데이터가 바뀔 때, watch에 명시된 함수가 동작한다.
- 하지만 watch 함수 내에서 새로운 값을 대입하려하면 또 그것 자체가 값을 바뀌게 하는 것이므로 무한 루프의 위험성이 있어 watch내에선 값을 변경시켜서는 안된다.

근데 사실 많이 사용안한다고 한다.

- 단순히 값을 지켜보며 어떠한 역할을 하게 만드는 것인데, 사실 이것에 있어서 그 값을 다시 바꾸지 못하기도 하고 computed를 통해 그 값의 변화에 따라 다시 값을 변경시키는 동작을 수행할 수 있기 때문에 오히려 computed가 훨씬 많이 사용된다고 한다.
