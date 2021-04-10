### Vue 디렉터리 구조

![디렉터리구조1](https://user-images.githubusercontent.com/37873745/105353209-50e16100-5c32-11eb-82af-5fa7c6380e80.png)

```jsx
// main.js

import Vue from 'vue'
import App from './App'

new Vue ({
    el: '#app',
    render: h => h(App)
})
```

```jsx
// App.vue

<template>
  <div>
    {{ message }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: "Today Frist Vue !"
    }
  }
}

</script>
```

### ESLint

lint라는게 내가 작성한 규칙에 의해 코드를 맞추는 방법

설치

- npm install -D eslint eslint-plugin-vue
- `.eslintrc.js` 작성

```jsx
// 각자 사용하는 에디터 옵션에서 '자동 수정' 기능을 끌 수 있습니다!
// WebStorm
// Preferences > Languages & Frameworks > JavaScript > Code Quality Tools > ESLint >> Run eslint --fix on save
// VS Code
// ...
module.exports = {
  // 지원하려는 JS 언어 옵션을 지정!
  parserOptions: {
    parser: '@babel/eslint-parser'
  },
  extends: [
    // https://eslint.vuejs.org/rules/#priority-b-strongly-recommended-improving-readability-for-vue-js-3-x
    'plugin:vue/strongly-recommended'
  ],
  rules: {
    // https://eslint.vuejs.org/rules/html-closing-bracket-newline.html
    'vue/html-closing-bracket-newline': ['error', {
      multiline: 'never'
    }],
    // https://eslint.vuejs.org/rules/html-self-closing.html
    'vue/html-self-closing': ['error', {
      html: {
        void: 'always', // E.g, <br />
        normal: 'never', // E.g, <div></div>
        component: 'never' // E.g, <my-comp></my-comp>
      }
    }]
  }
}
```

- 아래와 같이 인텔리제이 속성을 주면 저장할 때 자동으로 린팅을 맞춰준다고 한다.

![인텔리제이 속성](https://user-images.githubusercontent.com/37873745/105353215-52128e00-5c32-11eb-94bd-130e191a1730.png)

- babel eslint parser: ECMAScript 6+을 하위 JS 엔진에서 실행할 수 있도록 변환하는 데 사용되는 오픈 소스
    - 어느정도는 옛날 브라우저버전에서도 돌아갈 수 있게 알아서 맞춰준다.
    - 속성 설정해야하는 게 진짜 개많다.....
- 최종 디렉토리 구조는 다음과 같다.
    - 빨간색이 린팅을 위한 파일들....
    - 내부 코드는 study4를 참조하도록 하자.

![디렉터리구조 2](https://user-images.githubusercontent.com/37873745/105353212-50e16100-5c32-11eb-8a31-68213382c6ff.png)
