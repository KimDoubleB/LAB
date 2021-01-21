## npm과 JS의 클래스

- npm init: package.json 초기화
    - package.json: 프로젝트 이름부터 외부개발모듈, 실행스크립트 등을 정하는 파일
- npm ls -g : 전역속성의 모듈 리스트 확인
- npm install: package.json 에 있는 외부모듈을 가져와 저장한다.
    - node_modules에 외부모듈을 다 받아온다.
    - npm install -g module: 전역속성으로 설치
    - npm un -g module: 전역속성의 모듈 삭제
    - npm install —D module: 로컬속성 dev 용으로 모듈설치
        - 이걸로 저장되면 아래 package.json에 devDependencies에 등록이 되는 것
- npm info module: 해당 module에 대한 정보(버전들)을 확인가능해진다.
- package.json
    - devDependencies: 개발할 때만 필요한 외부모듈
    - dependencies: 실제 프로덕트(배포하는)에 필요한 외부모듈
    - script: `npm run` 할 때, 뒤에 무엇을 붙였을 때 어떤 명령어를 실행할 것인가를 결정한다.

    ```jsx
    "scripts": {
        "dev": "parcel index.html",
        "build": "parcel build index.html"
    }
    ```

    - 이런식으로 작성하면 `npm run dev` 라고 하면 뒤에 작성되어 있는 명령어가 실행되는 것이다. 그래서 원하는 대로 실행 코드를 작성해서 실행하는 명령어를 정할 수 있는 것이다.

[내 NPM 패키지(모듈) 배포하기](https://heropy.blog/2019/01/31/node-js-npm-module-publish/)

---

Java: 클래스 기반의 객체지향

Javascript: 프로토타입 기반의 객체지향

```jsx
class Hello {
    // 생성자
    constructor({ a, b }) { // 구조분해문법
        this.a = a;
        this.b = b;
    }

    getA() {
        console.log(this.a);
        return this;
    }
    getB() {
        console.log(this.b);
        return this;
    }

		// 정적 메서드
    static getThis() {
        console.log(this);
        return this;
    }
}

const h = new Hello({
    a: 123,
    b: 'ABC'
});
```

- 이렇게 구조분해문법으로 바로 할당을하면 깔끔하다고 함.
    - 더 좋다고 한듯?

위 메서드에서는 this를 리턴하고 있음. 저 객체 자체를 리턴한다고 보면 됨.

- 일반 함수에서 this임으로 호출한 객체를 의미하므로...
- 이렇게 사용하면 메서드 체이닝을 사용할 수 있다.

```jsx
h.getA().getB(); // method chaining
```

- h 객체를 가지고 getA, getB 메서드를 바로 수행

근데 위 class는 사실 실제 클래스가 아니고 흉내낸 것이라고 함.

prototype기반의 객체지향을 사용한다고 함.

```jsx
function Hello({ a, b }) {
    this.a = a;
    this.b = b;
}

Hello.prototype.getA = function () {
    console.log(this.a);
    return this;
}

Hello.prototype.getB = function () {
    console.log(this.a);
    return this;
}

// 정적 메소드
Hello.getThis = function () {
    console.log(this);
    return this;
}

const h = new Hello({
    a: 123,
    b: 'ABC'
});
```

- 사실 위 클래스 기반으로 작성한 것은 바로 위 코드를 wrapping한 것뿐 원래는 이렇게 돌아간다고 한다.
- 객체의 prototype으로 접근해서 메서드들을 만들고, 정적 메소드의 경우엔 프로토타입을 안하고 바로 함수를 만들면된다.

위 코드에서 h를 찍어보면 a, b의 속성값은 있는데 메서드는 보이지않는다.

- __proto__ 아래에 getA(), getB() 메서드가 들어가 있다.
- 클래스로서 존재하고 여러 인스턴스간에 이 메서드를 가져다가 쓴다고 생각하면 될듯?

[Javascript ES6의 클래스, 프로토타입 1](http://guswnsxodlf.github.io/javascript-class-and-prototype-1)

[[JavaScript] 8-1. 객체지향 프로그래밍(클래스 vs 프로토타입)](https://bkdevlog.netlify.app/posts/oop-class-of-js)

## Common.js VS ESM

```jsx
// Common.js
const axios = require('axios')
module.exports = {
}
// ESM
import axios from 'axios'
export default {
}
```

- Frontend에서는 ESM 방식을 사용한다고 함.

[Node.js에서 ES 모듈(import/export) 사용하기](https://www.daleseo.com/js-node-es-modules/)

## Parcel

[Parcel](https://ko.parceljs.org/)

- 알아서 필요한 외부 모듈을 설치해주고, 구동까지 해준다.
- 별다른 설정없이 Vue 파일 import할 때, 확장자 언급할 필요도 없다.
