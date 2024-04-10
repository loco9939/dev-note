# REST API

## 정의

REST API는 REST 원칙에 의거하여 프로그램간 통신 규약을 작성하는 것을 말한다.

## 역사

REST(REpresentational State Transfer)는 WWW와 같은 분산된 하이퍼미디어 시스템을 위한 소프트웨어 아키텍처의 한 형식으로, '로이 필딩'이라는 교수의 박사학위 논문에서 소개되었다.

**REST API**는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처로, REST API는 REST를 기반으로 서비스 API를 구현한 것이다.

## 특징

**REST API**는 우리가 웹 개발에서 통신을 위해서 HTTP 통신을 할 때, HTTP의 장점을 최대한 활용할 수 있는 아키텍처로써 HTTP 프로토콜을 의도에 맞게 디자인하도록 유도한다.

![REST](/img/REST.png)

> REST란, REpresentational State Transfer의 약어로, 표현된 자원의 상태를 의미한다. 이는 자원의 현재상태일수도, 미래 상태일수도 있다.

## REST API 구성

- 자원: URI 엔드 포인트 (**변하지 않는 식별자 사용**)
- 행위: 자원에 대한 행위 (HTTP 요청 메서드)
- 표현: 행위의 구체적 내용 (Payload)

## REST API 설계 원칙

1. URI는 리소스를 표현하는데 집중한다.

```js
# Bad
GET /getTodos/1
GET /todos/show/1

# Good
GET /todos/1
```

- 자원의 상태는 변할 수 있지만, 식별을 위해서 변하지 않는 식별자(URI)를 사용해야한다.
	- URI에는 동사는 최대한 사용하지 않고 명사를 사용하는것을 지향한다.
	- 예측 가능해야하며, 일관성을 유지해야한다.
	- 띄어쓰기는 '-'를 사용한다.

2. 행위에 대한 정의(CRUD)는 HTTP 요청 메서드를 통해 해야한다.

- GET: 모든/특정 리소스 취득
- POST: 리소스 생성
- PATCH: 리소스 전체 교체
- PUT: 리소스 일부 수정
- DELETE: 모든/특정 리소스 삭제

## Thinking In React 예제

기본 리소스 URL: `/products`

1. Read (Collection): `GET/products` → 상품 목록 확인
2. Read (Item): `GET/products/{id}` → 특정 상품 정보 확인
3. Create (Collection Pattern 활용): `POST/products` → 상품 추가 (JSON 정보 함께 전달)
4. Update (Item): `PUT 또는 PATCH/products/{id}` → 특정 상품 정보 변경 (JSON 정보 함께 전달)
5. Delete (Item): `DELETE/products/{id}` → 특정 상품 삭제

```ts
// Express 설치 후..
app.get('/products', (req, res) => {
  const products = [
    {
      category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
    },
    {
      category: 'Fruits', price: '$1', stocked: true, name: 'Dragonfruit',
    },
    {
      category: 'Fruits', price: '$2', stocked: false, name: 'Passionfruit',
    },
    {
      category: 'Vegetables', price: '$2', stocked: true, name: 'Spinach',
    },
    {
      category: 'Vegetables', price: '$4', stocked: false, name: 'Pumpkin',
    },
    {
      category: 'Vegetables', price: '$1', stocked: true, name: 'Peas',
    },
  ];

  res.send({ products });
});
```

## 참조

[RESTAPI - 위키백과](https://ko.wikipedia.org/wiki/REST)