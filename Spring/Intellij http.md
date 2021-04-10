# Intellij GET/POST HTTP

- Code: https://github.com/KimDoubleB/spring-learning/tree/master/http
- Created: Apr 10, 2021 4:05 PM

개발을 하다보면, 직접 HTTP 요청을 수행해보며 테스트 해보고 싶을 때까 있다. 직접 테스트 코드를 짜는 것이 중요하고 좋지만, 시간 및 상황의 제약으로 인해 말이다.

이럴 땐, 보통 Postman을 사용한다. 워낙 오랫동안 사람들에게 사용되어 왔고, 유명하기도 하고, Collection 형태로 프로젝트 별로 요청들을 관리하기도 편하다.

근데 Intellij를 사용하다보면, 간단한 테스트 요청임에도 Postman을 키고 요청들을 구성해야하기 귀찮을 때가 있다. 그리고 요청을 많이 진행해야할 때는 프로그램 간 계속 왔다갔다 해야되서 더 귀찮아지기도 한다.

이런 경우엔 Intellij 에서도 HTTP 요청을 구성할 수 있도록 제공하고 있으니 그걸 사용하면 된다.

- 기본적으로 제공하고 있어, 다른 Plugins를 설치하지 않아도 된다.
- Postman 에서 제공하는 것처럼 HTTP 헤더의 속성들을 다 설정할 수 있다.
- GET/POST/PUT/DELETE 기본적인 HTTP 메서드들을 사용할 수 있다.
- **HTTP 요청코드들을 파일로 생성해 활용하는 것이여서 git을 활용할 수 있다. → 즉, repo에 push만 해놓는다면 모든 사람들이 활용이 가능하다.**

맨 마지막의 이유가 엄청나게 큰 것 같다. 일단 Postman은 여러 사람과 같이 활용하기 위해선 유료이면서, 무료버전으로 이용하다보면 요청횟수 제한이 걸려있어 이걸로 block된 사례도 있었다.

- 이런 상황에선 Intellij를 활용한다면 엄청 좋을 것 같다.

---

직접 활용해보자.

사용하긴 매우 간단한다.

![http](https://user-images.githubusercontent.com/37873745/114264291-ec5dc300-9a24-11eb-9211-47bea3f562a5.png)


- `.http` 파일을 만들면 intellij에서 자동으로 인식한다.
- `cmd + N` 을 사용해 HTTP를 검색하면 만들 수 있는 파일에 떠서 그걸 활용하면 된다.

파일 형식은 다음과 같다.

```bash
<HTTP-METHOD> <URI>
<HEADER ATTRIBUTES>
```

예제

- GET

```bash
GET localhost:8080/api/product
```

- GET using Cookie

```bash
GET localhost:8080/api/product
Cookie: userId=123
```

- POST

```bash
POST localhost:8080/api/product
Content-Type: application/json

{
  "id": 2,
  "name": "IPhone",
  "price": 1000000
}
```

- POST using Authorization

```bash
POST localhost:8080/api/product
Authorization: Bearer token-token-token
```