# WebClient

[Web on Reactive Stack](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-client)

Spring WebFlux는 HTTP request를 수행하는 client인 `WebClient` 를 포함하고 있다.

# `WebClient`

- HTTP Request를 수행하는 Client.
- Reactor 기반의 Functional, Fluent API 이다.
- 쓰레드, 동시성 문제들을 다룰 필요 없이 비동기 로직의 선언적 형태로 사용이 가능하다.
- 완벽한 논블러킹 형태이며, 스트리밍을 지원한다.
- 서버 사이드의 request/response를 인코딩, 디코딩하는 [코덱](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-codecs)들을 다 지원한다.

결국 쉽게 정리하자면, **WebClient는 비동기/논블로킹을 지원하는 HTTP Request Client(API)** 이다.

<br>

# Configuration

## WebClient 생성

2가지 방법을 지원한다.

### 1. Static Factory methods

- `WebClient.create()`
- `WebClient.create(String baseUrl)`

### 2. `WebClient.builder()` Builder 사용

builder와 함께 사용 가능한 options은 다음과 같다.

- `uriBuilderFactory` : base URL을 사용하기 위한 Customized `UriBuilderFactory`
- `defaultUriVariables` : URI 템플릿을 확장할 때, 사용하는 기본 값
- `defaultHeader` : 모든 요청에 대한 기본 헤더
- `defaultCookie` : 모든 요청에 대한 쿠키 설정
- `defaultRequest` : 모든 요청을 customize하기 위한 `Consumer`
- `filter` : 모든 요청에 대한 Client filter
- `exchangeStrategies`: HTTP message reader/writer customization
- `clientConnector` : HTTP client library settings

예시를 보면, 다음과 같다

```java
WebClient client = WebClient.builder()
			.codecs(configurer -> ... )
	    .build();
```

- **WebClient는 만들어지면 immutable 하다.** 하지만, clone해서 원하는 기능들을 추가할 수는 있다.

```java
WebClient client1 = WebClient.buidler()
			.filter(filterA).filter(filterB).build();
	
WebClient client2 = client1.mutate()
			.filter(filterC).filter(filterD).build();
```

### MaxInMemorySize

Codecs는 메모리 이슈를 피하기 위해서 메모리 버퍼링 데이터 사이즈는 초기에 256KB로 제한되어져 있다. 만약 넘으면 에러가 나기 때문에, 만약 변경하고 싶은 경우 `MaxInMemorySize` 옵션으로 변경할 수 있다.

### Reactor Netty

WebClient에서 Reactor Netty를 사용함에 있어, 만약 Reactor Netty의 세부 설정들을 변경하고 싶다면 `clientConnector` 옵션에 customize한 `HttpClient` 를 제공하면 된다.

```java
HttpClient httpClient = HttpClient.create().secure(sslSpec -> ...);

WebClient webClient = WebClient.builder()
				.clientConnector(new ReactorClientHttpConnector(httpClient)
				.build()
```

`HttpClient` 를 통해 시간초과(timeout) 기능들을 지정할 수 있다.

```java
HttpClient httpClient = HttpClient.create()
				.option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 10000);

WebClient webClient = WebClient.builder()
        .clientConnector(new ReactorClientHttpConnector(httpClient))
        .build();
```

좀 더 상세하게 시간초과(timeout)를 지정할 수도 있다.

- Read/Write
- Response time
- Response time for a specific request

```java
HttpClient httpClient = HttpClient.create()
        .responseTimeout(Duration.ofSeconds(2));

// Create WebClient

// Or for specific request
WebClient.create().get()
        .uri("https://example.org/path")
        .httpRequest(httpRequest -> {
            HttpClientRequest reactorRequest = httpRequest.getNativeRequest();
            reactorRequest.responseTimeout(Duration.ofSeconds(2));
        })
        .retrieve()
        .bodyToMono(String.class);
```

문서에선 `ReactorClientHttpConnector` 를 사용하지 않고 Jetty를 사용하는 방법인 `JettyClientHttpConnector` 도 나온다.

- 기존에 제공되는 `HttpClient` 를 사용하면 resources (`Executor`, `ByteBufferPool`, `Scheduler`)들이 process exits 또는 `stop()` 이 콜 될 때까지 유지된다.
- Jetty를 사용해서 이러한 resource들을 Spring의 `ApplicationContext` 가 종료되기 전까지 유지시키며 공유할 수 있도록 할 수 있다. `JettyResourceFactory` 사용.

---

# `retrieve()`

`retrieve()` 메서드는 결과(Response)를 어떻게 추출할 것인지 명시하는데 사용한다.

```java
WebClient client = WebClient.create("https://example.org");

Mono<ResponseEntity<Person>> result = client.get()
				.uri("/persons/{id}", id)
				.accept(MediaType.APPLICATION_JSON)
				**.retrieve()**
				.toEntity(Person.class)
```

또는, body만 받아올 수도 있다.

```java
WebClient client = WebClient.create("https://example.org");

Mono<Person> result = client.get()
        .uri("/persons/{id}", id)
				.accept(MediaType.APPLICATION_JSON)
        .retrieve()
        **.bodyToMono(Person.class);**
```

결과 응답에서 에러(4xx, 5xx)를 줄 수도 있지 않을까?

- `WebClient` 에서는 기본 값으로 4xx, 5xx 결과 응답이 온 경우에, `WebClientResponseException` 로 결과를 인식한다.
- 이러한 에러 응답이 온 경우 핸들링 하기 위해선, `onStatus` 핸들러를 사용하자.

```java
Mono<Person> result = client.get()
				.uri("/person/{id}", id)
				.accept(MediaType.APPLICATION_JSON)
				.retrieve()
				.onStatus(HttpStatus::is4xxClientError, response -> ...)
        .onStatus(HttpStatus::is5xxServerError, response -> ...)
        .bodyToMono(Person.class);
```

---

# Exchange

`exchangeToMono()`, `exchangeToFlux()` 메서드들은 response status에 따라 결과를 다르게 디코딩하는 것 처럼 좀 더 advance하게 사용할 때 유용하다.

예를 들어, 

- OK(200)이 왔을 때는 `Person` 클래스로 디코딩하고,
- 4xx이 왔을 때는 `ErrorContainer` 클래스로 디코딩하고,
- 나머지로 온 경우엔 `error`를 발생시키고 싶다면.

```java
Mono<Object> entityMono = client.get()
        .uri("/persons/1")
        .accept(MediaType.APPLICATION_JSON)
        .exchangeToMono(response -> {
            if (response.statusCode().equals(HttpStatus.OK)) {
                return response.bodyToMono(Person.class);
            }
            else if (response.statusCode().is4xxClientError()) {
                // Suppress error status code
                return response.bodyToMono(ErrorContainer.class);
            }
            else {
                // Turn to error
                return response.createException().flatMap(Mono::error);
            }
        });
```

---

# Request Body

Request body는 `Mono` 같은 `ReactiveAdapterRegistry` 에 의해 핸들링되는 비동기 타입으로도 사용할 수 있다. 즉, Reactor 기반의 `Mono` , `Flux`  클래스 타입을 사용할 수 있다.

예제를 보자.

```java
Mono<Person> personMono = ... ;

Mono<Void> result = client.post()
        .uri("/persons/{id}", id)
        .contentType(MediaType.APPLICATION_JSON)
        .body(personMono, Person.class)
        .retrieve()
        .bodyToMono(Void.class);
```

```java
Flux<Person> personFlux = ... ;

Mono<Void> result = client.post()
        .uri("/persons/{id}", id)
        .contentType(MediaType.APPLICATION_STREAM_JSON)
        .body(personFlux, Person.class)
        .retrieve()
        .bodyToMono(Void.class);
```

- 위 예제처럼, `body` 에 비동기 타입 데이터를 넣어주면 된다.

만약 실제 값을 활용하고 싶다면 또는 사용하고 있다면, `bodyValue` 를 통해 body를 구성할 수 있다.

```java
Person person = ... ;

Mono<Void> result = client.post()
        .uri("/persons/{id}", id)
        .contentType(MediaType.APPLICATION_JSON)
        .bodyValue(person)
        .retrieve()
        .bodyToMono(Void.class);
```

<br>

### Form Data

Form data를 전송할 때는 body에 `MultiValueMap<String, String>` 을 사용해야만 한다. 

- `FormHttpMessageWriter` 에 의해서 자동으로 content가 `application/x-www-form-urlencoded` 로 설정된다.

```java
MultiValueMap<String, String> formData = ... ;

Mono<Void> result = client.post()
        .uri("/path", id)
        .bodyValue(formData)
        .retrieve()
        .bodyToMono(Void.class);
```

<br>

### Multipart Data

multipart data를 보내기 위해선 body에 `MultiValueMap<String, ?>` 을 사용해야 한다. 여기서 Value에는 part content를 나타내는 `Object` instance 또는 part의 컨텐츠, 헤더를 나타내는 `HttpEntity` 인스턴스가 들어가야 한다.

multipart data를 편하게 사용하기 위해서 `MultipartBodyBuilder` API가 존재한다.

```java
MultipartBodyBuilder builder = new MultipartBodyBuilder();
builder.part("fieldPart", "fieldValue");
builder.part("filePart1", new FileSystemResource("...logo.png"));
builder.part("jsonPart", new Person("Jason"));
builder.part("myPart", part); // Part from a server request

MultiValueMap<String, HttpEntity<?>> parts = builder.build();

// OR

Mono<Void> result = client.post()
        .uri("/path", id)
        .body(builder.build())
        .retrieve()
        .bodyToMono(Void.class);
```

`MultipartBodyBuilder` 대신에 `BodyInserters` 를 사용하면 인라인 스타일로 multipart content를 제공할 수 있다.

```java
import static org.springframework.web.reactive.function.BodyInserters.*;

Mono<Void> result = client.post()
        .uri("/path", id)
        .body(fromMultipartData("fieldPart", "value").with("filePart", resource))
        .retrieve()
        .bodyToMono(Void.class);
```

---

# Filters

요청 처리 전 Request들을 변경하거나, 인터셉트하고 싶은 경우, `WebClient.Builder` 에서 client filter(`ExchangeFilterFunction`)를 사용하면 된다.

특정 클라이언트에서 항상 요청에 특정 헤더가 들어가야 한다고 하자. 그럼 다음 예제처럼 작성할 수 있다.

```java
WebClient client = WebClient.builder()
				.filter((request, next) -> {

						ClientRequest filtered = ClientRequest.from(request)
									.header("foo", "bar")
									.build();

						return next.exchange(filtered);
				})
				.build();
```

이런 기능들은 [횡단 관심사(Cross-cutting concerns)](https://ko.wikipedia.org/wiki/%ED%9A%A1%EB%8B%A8_%EA%B4%80%EC%8B%AC%EC%82%AC)에서 유용하게 사용될 수 있다. 예를 들어, 인증(Authentication) 같은 기능들에서 말이다.

<br>

```java
import static org.springframework.web.reactive.function.client.ExchangeFilterFunctions.basicAuthentication;

WebClient client = WebClient.builder()
        .filter(basicAuthentication("user", "password"))
        .build();
```

Filter를 등록하면 해당 client에서의 요청은 다 적용되게 된다. 근데 특정 요청에서만 Filter를 등록하고 싶다면?Client를 수정해야할 것이다. 

근데 앞에서 `WebClient` 가 immutable 하다 했던 것이 기억나는가? 그래서 같은 기능을 하는 `WebClient` 에 기능을 추가하고 싶으면 clone해서 수행을 했었다. Filter도 마찬가지로 기존 만들어져있던 `WebClient` 에 새로운 Filter를 더하고 싶다면 clone해서 기능을 추가해줘서 만든 새로운 `WebClient` 를 활용하면 된다.

<br>

예제로 기존 존재하던 `WebClient` instance에 filter의 인덱스 0번에 basic authentication을 추가하고자 한다하면, 다음과 같이할 수 있다.

```java
// ....  webClient 만들어졌다.

WebClient webclient2 = webClient.mutate()
				.filter(firstList -> {
								filterList.add(0, basicAuthentication("user", "password")
				})
				.build();

```

---

# Attributes

`WebClient` 사용 시, Request에 attributes를 추가할 수도 있다.

Attribute는 filter와 함께 사용할 때 유용하면서 편리하게 사용될 수 있다. 주어진 요청에 특정 attribute 값이 있을 때, 특정 행동을 하게 한다거나 하는 등 기능을 추가할 수 있다.

```java
WebClient client = WebClient.builder()
        .filter((request, next) -> {
            Optional<Object> usr = request.attribute("myAttribute");
            // usr가 값이 있고 특정 값을 가진 경우, ~~~ 로직을 수행하게 할 수 있다.
        })
        .build();

client.get().uri("https://example.org/")
        .attribute("myAttribute", "...")
        .retrieve()
        .bodyToMono(Void.class);

    }
```

---
# Context

Attributes는 필터에 정보를 전달하는 쉬운 방법이지만, 현재 실행되는 요청에만 영향을 줄 수 있다.

만약 `flatMap` 을 통해 중첩되거나, `concatMap` 을 통해 실행되는 추가 요청으로 정보를 전파하고 싶은 경우엔, Reactor `Context` 를 사용해야 한다.

- Context 개념은 [여길](https://godekdls.github.io/Reactor%20Core/advancedfeaturesandconcepts/#98-adding-a-context-to-a-reactive-sequence) 참조하자.

```java
WebClient client = WebClient.builder()
        .filter((request, next) ->
                Mono.deferContextual(contextView -> {
                    String value = contextView.get("foo");
                    // ...
                }))
        .build();

client.get().uri("https://example.org/")
        .retrieve()
        .bodyToMono(String.class)
        .flatMap(body -> {
                // perform nested request (context propagates automatically)...
        })
        .contextWrite(context -> context.put("foo", ...));
```

---
# Synchronous Use

`WebClient` 는 기본적으로 비동기로 작동한다. 하지만, 만약 동기로 작동하게 만들고 싶다면 `block()` 을 사용하면 된다.

```java
Person person = client.get().uri("/person/{id}", i)
				.retrieve()
		    .bodyToMono(Person.class)
		    .block();

List<Person> persons = client.get().uri("/persons")
				.retrieve()
		    .bodyToFlux(Person.class)
		    .collectList()
		    .block();
```

하지만 만약 여러개의 `WebClient` 를 활용하면서 각각 블로킹을 하며 사용하는 것은 비효율적이다. 효율적으로 사용하기 위해 다양한 reactive pipeline을 사용하는 것이 좋다. 잘 사용한다면 블로킹을 최대한 줄이면서 작동할 수 있게 만들 수 있다. 아래는 `Mono.zip`  을 사용해 하나의 블로킹으로 구현한 하나의 예이다.

```java
Mono<Person> personMono = client.get().uri("/person/{id}", personId)
        .retrieve().bodyToMono(Person.class);

Mono<List<Hobby>> hobbiesMono = client.get().uri("/person/{id}/hobbies", personId)
        .retrieve().bodyToFlux(Hobby.class).collectList();

Map<String, Object> data = Mono.zip(personMono, hobbiesMono, (person, hobbies) -> {
            Map<String, String> map = new LinkedHashMap<>();
            map.put("person", person);
            map.put("hobbies", hobbies);
            return map;
        })
        .block();
```

동기적 사용방법을 설명했지만, Spring MVC/Webflux - `Flux`, `Mono` 를 사용하는 환경에서는 절대(Never) block을 사용해서는 안된다. 그냥 단순히 controller 메서드에서 reactive type을 넘기는 식으로 진행해야 한다. 

---
# Testing

`WebClient` 를 테스트 하기 위해서는 `[OkHttp MockWebServer`](https://github.com/square/okhttp#mockwebserver) 같은 mock web server를 사용할 수 있다.

예로는

- Spring Framework의 `[WebClientIntegrationTests](https://github.com/spring-projects/spring-framework/blob/master/spring-webflux/src/test/java/org/springframework/web/reactive/function/client/WebClientIntegrationTests.java)`
- OkHttp repository의 `[static-server](https://github.com/square/okhttp/tree/master/samples/static-server)` sample

을 참고하자.