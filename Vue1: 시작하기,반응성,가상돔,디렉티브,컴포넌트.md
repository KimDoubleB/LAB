# Vue 시작

[설치방법 - Vue.js](https://kr.vuejs.org/v2/guide/installation.html)

문서 좋다 → 문서를 보면서 공부해라 !

다운로드받아서 하는 건 조금 옛날 스타일이라고 함. 그냥 CDN으로 받아서 하는게 좋다고 함. (좋다기보단 그냥 편하고 쉽게 할 수 있다)

외부 라이브러리는 별도의 공지가 없으면 head tag안에 작성을 해주면 된다.

- main.js 같이 작성한 js 코드들은 body 태그의 맨 밑에 작성을 해야만 했으나, defer 속성을 통해 head안에 넣는다는게 요즘 작성방법이라고 한다.
    - head tag에 작성해버리면 DOM을 사용을 못함
        - html을 읽어들일 때, 맨 위부터 읽기 시작. 그러면 body(여러 태그들에 대한 정보)를 읽기 전에 js를 읽어버린다. 그러면 body를 읽기 전이여서 js에서 DOM을 사용해도 그것에 대한 정보를 하나도 모르는 것. 그래서 DOM을 사용해봤자 null이 나오게 된다.
        - 그래서 js는 body태그들을 다 읽고 로딩하게 끔 만들어야한다. 그래야 DOM을 사용해서 태그 요소들에 대한 정보를 가져올 수 있다.
    - 하지만!!! `defer` 속성을 추가해서 html 파일 다 읽고 js를 로딩해라 이런의미로 사용할 수 있고, head 태그 안에 들어갈 수 있게 된다.
        - <script defer src="main.js"></script>

Vue를 cdn으로 가져오면 Vue 생성자 함수가 전역화되어 있다. 그래서 바로 사용하면 된다.

```jsx
var app = new Vue({
	el: '#app',
	data: {
		message: 'Hello'
	}
})
```

- app 아이디 선택자를 통해 이 태그에 대해 이 데이터를 사용하겠다. 이런 의미.
- 이렇게 할당해서 사용하기도 하는데 변수에 할당안하고 선언만 해서 사용하는 경우도 많다고 함.

```html
<div id="app">
  {{ message }}
</div>
```

- 이렇게 사용하면 된다.

저렇게 중괄호 2개 사이 {{ }}에는 JS 표현식을 사용할 수 있다.

- {{ 1 + 2 }} 라고 하면 3이 나온다.

{{ }} 를 mustache 구문이라고 한다.

> 데이터 바인딩의 가장 기본 형태는 “Mustache” 구문(이중 중괄호)을 사용한 텍스트 보간입니다.

[템플릿 문법 - Vue.js](https://kr.vuejs.org/v2/guide/syntax.html)

- 여기서 Vue의 데이터를 가져와 사용할 수 있다.

const app 으로 vue 생성자 객체를 받아 보자.

- message가 있음. 데이터가 속성으로 추가되서 객체로 나오는 듯?
- $ref, $data, $store, $props를 많이 사용하게 될 것
- _뭐시기: 이런식으로 언더바 변수명으로 되어있는 속성들은 private이니깐 왠만하면 건들지마라 이런 의미라고 함.

## 반응성: 데이터가 바뀌면 화면도 바뀌는 개념

여기서 이 message에 있는 데이터를 변경하면 현재 화면에 비춰지고 있는 message 데이터가 자동으로 바뀌게 된다. **이걸 "반응성" 이라고 부른다**.

- `app.message = 'this statement'` 하면 this statement로 글이 바뀌게 되는 것

반응형에 사용되는 데이터들을 반응형 데이터라고 함.

여기서 말하는 반응형은 그 웹에서 유저의 반응에 따라 형태가 바뀌는 반응형과는 다른 정의라고 한다.

## 가상돔 (Virtual DOM)

- 앞서 message를 바꿔서 렌더링이 바로 바뀐 것을 볼 수 있다.
- 이걸 하는 원리가 가상돔을 사용하는 것이라고 한다. 값이 변하면 가상돔을 만들어서 기존 것과 비교하고, 바뀐 부분만 갱신하게 되는 것.
    - **가상 부분에다가 기존 구조를 가지고 있고(스냅샷), 데이터가 바뀌면 기존 구조에다가 데이터를 바꿔 스냅샷을 또 가짐. 이 2개의 스냅샷을 비교하고 바뀐 부분을 돔으로 업데이트하는 것**
- 그래서 뷰를 사용하다보면 **가상돔간의 계속적인 비교를 수행**하게 된다. 리액트도 마찬가지도 가상돔을 가지고 있다. 그렇다보니 이게 메모리를 조금 어느정도 먹는다고 한다. 스벨트는 이 가상돔이 없다고 함.

> Vue문서: Vue.JS 내부에서는 더 많은 작업을 하고 있습니다.

## 디렉티브 (v-): v로 시작하는 모든 것

### v-bind (단방향 바인딩)

- 데이터를 통해 html, css를 관리할 수 있게 된다. 직접적으로 수정을 하는 것은 아닌데, v-bind:class를 통해 데이터로 클래스를 추가하고, 그에 맞게 css를 작성해놓았으면 바로 적용이 되기 때문에 데이터에 따라 html, css 가 수정되게 되는 것이다.
    - `<h1 v-bind:class="{ active }">`: active data가 true라면 active class를 추가한다.
    - `<h1 v-bind:class="{ a: active }">`: active data가 true라면 a class를 추가한다.

    [클래스와 스타일 바인딩 - Vue.js](https://kr.vuejs.org/v2/guide/class-and-style.html)

- v-bind:전역속성

    [HTML 전역 속성 사용하기](https://juein.tistory.com/39)

    - 이렇게 속성들을 다 쓸 수 있다고 함
    - v-bind:title 이런식으로
- **v-bind: ~~~ 하면 이거 계속 쓰기 너무 힘들다. → 엄청 많이 사용하는데 이거 계속 써줘야하나...????**
    - 그래서 v-bind는 생략이 가능하다고 하다. `v-bind:title` 이면 `:title` 로 사용이 가능하다.

### v-if

- if 문처럼 사용이 가능한 것
- `<p v-if="seen"> 볼 수 있을까? 없을까? </p>`
    - Vue - data의 seen이 true냐, false냐에 따라 이 데이터를 보여주냐/마냐 로 결정된다.
- True, False냐 이런걸로 데이터를 표현이 가능하다.
    - 불린값이 아니더라도 만약 서버로부터 데이터를 가져왔는데 메시지가 있어? 없어? 없으면 출력하지마 → 약간 이런용으로 많이 사용한다고 함

    ```jsx
    const app = new Vue({
        el: '#application',
        data: {
            message: '', // False
            active: true
        }
    })

    -------------------
    <h1 v-if="message"
    		class="message">
          {{ message }}
    </h1>
    ```

    - 또는 count 를 사용해 출력

    ```jsx
    const app = new Vue({
        el: '#application',
        data: {
            text: ['12312', '312312'],
            count: 132,
            message: 'Hello World !',
            active: true
        }
    })
    ```

    - `v-if="count"` 또는 `v-if="text.length"` 식으로 제어가 가능하다.

### v-for: 반복문

```jsx
const app = new Vue({
    el: '#application',
    data: {
        message: 'Hello World !',
        active: true,
        fruits: [
            'Cherry',
            'Apple',
            'Pineapple'
        ]
    }
})

-------------

<ul>
    <li
        v-for="f in fruits">
        {{ f }}
    </li>
</ul>
```

- 이렇게 하면 주어진 배열 데이터를 for loop로 돌면서 li 태그도 반복되어 자동으로 그려준다.
- 하지만 사실 위 방법은 맞지 않다. 왜냐, v-for는 무조건 key 속성도 동반해야만한다.
    - 고유된 key 값을 어떻게 주냐? 데이터에 id 값을 줘서 이걸 동반해야된다.
    - 기본적으로 요소를 가져올 때 id 값도 같이 주는 게 좋다. 이게 일반적이라고 함.
- 만약 key 값을 부여안하면 가상 돔에서 데이터 업데이트할 때 비교를 하지 못한다고 함.

```jsx
const app = new Vue({
    el: '#application',
    data: {
        message: 'Hello World !',
        active: true,
        fruits: [
            {
                id: 1,
                name: 'Cherry'
            },
            {
                id: 2,
                name: 'Apple'
            },
            {
                id: 3,
                name: 'Pineapple'
            }
        ]
    }
})

-----------------------------
<ul>
    <li
        v-for="f in fruits"
        :key="f.id">
        {{ f.name }}
    </li>
</ul>
```

- `v-bind:key` 임으로 v-bind가 생략되어 `:key`로만 사용해도 된다.

### v-on:  메소드를 호출하는 이벤트 리스너를 추가할 수 있다.

- `<button v-on:click="methods"> Button </button>` 같이 작성해서 바로 리스너를 추가할 수 있다.
- `v-on:` 뒤에는 이전에 이벤트 리스너에서 배운 속성들이 들어갈 수 있다.
    - click, dblclick, mousedown, mouseup, focus, blur, keydown, keyup, resize, scroll, cut, copy ...
- method에는 Vue에서 methods로 정의한 것들만 가능하다.

```jsx
new Vue({
    el: '.bobae',
    data: {
        message: 'Hello bobae !'
    },
    methods: {
        reverseMessage: function () {
            this.message = this.message
                            .split('')
                            .reverse()
                            .join('');
        }
    }
})
```

### v-model: 양식에 대한 입력과 앱 상태를 양방향으로 바인딩하는 기능

- 데이터가 바뀌면 화면이 바뀐다: 단방향 바인딩
    - 그러면, 반대로 화면이 바뀌면 데이터도 바뀌게 되면??
    - **둘다 연결되어 변경이 변경을 불러오는 기능을 양방향 바인딩이라고 한다**.

```jsx
<div class="bobae">
    <h1>{{ message }}</h1>
    <button v-on:click="reverseMessage">Reverse</button>
    <input
            v-bind:value="message"
            type="text">
</div>
------------------------------
new Vue({
    el: '.bobae',
    data: {
        message: 'Hello bobae !'
    },
    methods: {
        reverseMessage: function () {
            this.message = this.message
                            .split('')
                            .reverse()
                            .join('');
        }
    }
})
```

- 이건 단방향이다.
    - input에 데이터가 자동으로 들어가고, reverse를 누르면 input의 데이터도 변하게 된다.
    - 근데 input의 데이터를 변경하면 아무런 변화가 작동하지는 않는다.
- 이걸 양방향. 즉, input을 변경해도 message 데이터도 변경되어 출력되길 바란다면 `v-model` 을 사용하면 된다.

```jsx
<div class="bobae">
    <h1>{{ message }}</h1>
    <button v-on:click="reverseMessage">Reverse</button>
    <input
            v-model:value="message"
            type="text">
</div>
```

## 컴포넌트

![컴포넌트 이미지](https://user-images.githubusercontent.com/37873745/105347628-82562e80-5c2a-11eb-8f61-9decbae65524.png)

- 똑같이 생긴 요소인데 이게 여러군대서 재사용되는 경우 → 이걸 다 따로 만들 것인가? 비효율적
- 재사용이 되는 UI 라면 하나의 컴포넌트를 만들고 재사용하면 좋지 않을까? → 컴포넌트
    - 근데 사실, 재사용의 목적이 아니더라도 UI 요소를 만들고 이걸 관리를 할 수 있기 때문에 그냥 재사용 하지 않더라도 관리의 목적으로 만든다고 한다.
- **반복되는 UI의 데이터를 묶여 새로운 html 태그를 만들어내는 것 같다...**

```jsx
Vue.component('fruit-item', {
    template: '<div>{{ title }}: {{ fruitName }}</div>',
    props: ['fruitName'],
    data: function () {
        return {
            title: 'Fruit'
        }
    }
})

new Vue({
    el: '.bobae',
    data: {
        message: 'Hello bobae !',
        fruits:
            [{
                    id: 1,
                    name: 'Apple'
                },
                {
                    id: 2,
                    name: 'Banana'
                },
                {
                    id: 3,
                    name: 'Cherry'
              }]
    }
})

---------------------------------

<div class="fruits">
    <fruit-item
        v-for="f in fruits"
        :key="f.id"
        :fruit-name="f.name"></fruit-item>
</div>
```

- **컴포넌트 선언은 무조건 사용할 Vue보다 먼저 선언되어야 한다.**
- Vue로 시작하고 메서드명이 있으니, 정적 메서드 인 것을 알 수 있다.

- **첫 번째 요소: Component 이름**
    - vue component 이름은 '-'를 사용해서 2개의 단어를 붙인 단어로 하는게 일반적이다.
    - 태그 요소들이랑 요소 이름이 겹치지 않게 하기위해 일반적으로 이름을 저렇게 선언한다고 함.

- **두 번째 요소: Component 객체**
    - template 요소: 어떻게 출력할 것인가? 어떤 데이터가 들어오면 어떻게 보여줄 것인지?
    - props (properties) 요소: 반복할 데이터
        - component에서는 camelCase로 적어줘야 한다 ex) fruitName
        - html에서는 `v-bind:` 로 바인딩 해줘야하는데 여기서는 대시형식으로 작성해야한다.
            - ex) `v-bind:fruit-name="f.name"` OR `:fruit-name="f.name"`
    - data 요소: template에서 활용할 데이터 → 무조건 함수여야 한다.

- 컴포넌트의 이름으로 html tag를 만들 수 있다.
    - 근데 만약 컴포넌트의 이름이 대문자다? 그래도 소문자로 적는다!! html tag는 소문자로만 이루어져있기 때문에.

- 컴포넌트는 어느 위치에 있냐에 따라 전역/지역으로 사용가능해진다.
    - (위의 경우) **Vue의 생성자에 속하지 않고 그냥 전역으로 작성한 경우:** 컴포넌트는 한 번 생성해놓으면 전역속성으로 사용이 가능해진다. ****
        - 이말은 즉슨, 위에서 한번 선언만 해놓으면 html의 Vue 속성의 어디든 간에 fruit-name 태그를 통해 컴포넌트를 사용할 수 있다는 것.
        - props(`v-bind:fruit-name`)만 어떻게 넘겨주냐에 따라 저 컴포넌트에 데이터가 들어가서 정해진 형태에 맞게 바로바로 출력이 가능해지는 것!!!!
    - Vue 생성자 안에 'components' 이름으로 선언하면 지역으로 사용해진다.
        - 전역으로 하면 양이 많아졌을 때 너무 복잡해져서 지역으로 많이 사용한다고 한다.

### 싱글 파일 컴포넌트

위에서 컴포넌트를 알아봤는데, 사실 전역으로 선언하여 사용하는 것은 매우 소규모인 경우만 사용한다고 한다. 왜냐하면 이게 좀 많아지면 엄청 복잡해지고 유지보수가 매우 어려워지기 때문이다.

이러한 문제를 컴포넌트를 Vue파일로 만들어 해결하고 있다.

- Vue 파일의 경우, 대문자로 시작하는 이름으로 만드는게 규칙이라고 함.
- 반대로 JS파일은 소문자로 만드는게 규칙이고.

[싱글 파일 컴포넌트 - Vue.js](https://kr.vuejs.org/v2/guide/single-file-components.html)

- 일반적인 틀

```jsx
<template>

</template>

<script>
export default {
  name: "Hello"
}
</script>

<style scoped>

</style>
```

- 이게 엄청 편하다
    - 앞서 template과 그에 해당하는 코드도 따로 파서 관리할 수 있는 것도 편한데, 엄청난건 이 컴포넌트에만 해당하는 css를 지정할 수 있다는 것이다 !
        - `<style scoped> </style>` 를 통해
- template
    - <template></template> 안에는 최상위 태그가 하나만 있어야 한다.
        - 안에 <div> 태그가 2개있다? 안됨.
        - <div> 안에 2개의 <div>를 추가하는 식으로 진행해야한다.

```jsx
// App.vue
<template>
  <div>
    {{ message }}
  </div>
</template>

<script>
export default {
  // data: function () {
  //
  // } 아래가 이것과 같음.
  data() {
    return {
      message: "Hello Vue !"
    }
  }
}

</script>
<style scoped>
</style>
```

```jsx
// main.js
import Vue from 'vue'
import App from './App.vue'

// 싱글 컴포넌트 불러오는 방법
new Vue({
    el: '#app',
    render: (h) => {return h(App)}
})
```

- 위와 같이 컴포넌트를 불러올 수 있다.

```jsx
// Hello.vue -> component
<template>
  <div>
    Hello {{ name }}
  </div>
</template>

<script>
// Vue.component( 이름, 속성 )
// -> 여기서 export 하는 것은 '속성'이라고 보면 된다.
export default {
  props: ['name']
}
</script>

<style scoped>

</style>

=======================================
// App.vue -> entry point
<template>
  <div>
    <hello name="bobae"></hello>
  </div>
</template>

<script>
// 상대 경로
// import Hello from "./components/Hello";

// 경로 별칭 -> 위치가 바뀌어도 찾아낼 수 있다.
import Hello from "~/src/components/Hello";

export default {
  // component를 '지역등록'하는 것
  components: {
    Hello
  },
  data() {
    return {
      message: "Hello Vue !"
    }
  }
}

</script>
```

**Vue.component( 이름, 속성)**

- App.vue 파일에서 export하고 있는 것은 **위에서 속성만을 export** 하고 있다고 보면 된다.
- 이걸 import 하는 곳에선 **component를 지역등록을 통해서 적용**할 수 있다.