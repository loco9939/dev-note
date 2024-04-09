# GraphQL

**GraphQL**이란, API를 위한 쿼리 언어이다.

<p style="font-size:18px;">🤔 쿼리 언어는 무엇인가?</p>

쿼리언어는 데이터베이스에서 정보를 검색하는데 사용되는 언어인 **Database Query Language(DQL)** 를 의미한다.

가장 널리 사용되는 쿼리 언어는 **구조적 쿼리 언어(SQL)** 이다.

<p style="font-size:18px;">🤔 쿼리는 무엇인가?</p>

넓은 의미에서 쿼리는 데이터베이스나 데이터 리포지토리 시스템에서 데이터를 요청하는 것을 의미한다.

## 👍 장점

1. GraphQL은 API의 데이터에 대한 완벽하고 이해하기 쉬운 설명을 제공한다.

이로써 클라이언트에게 그들이 정확히 원하는 데이터만을 요청할 수 있는 힘을 제공한다.

또한, API를 시간에 따라 발전시키고 강력한 개발자 도구를 사용할 수 있게 도와준다.

## REST API vs GraphQL

```ts
// Query 및 Response 타입 정의
type Query {
  me: User
}
 
type User {
  id: ID
  name: String
}

// Query
{
  me {
    name
  }
}

// JSON Response
{
  "me": {
    "name": "Luke Skywalker"
  }
}
```

REST API를 사용하여 요청 파라미터를 담아서 데이터 요청을 보내면, 백엔드가 미리 지정한 데이터 구조 그대로 내려받을 수 있다.

하지만 GraphQL은 클라이언트가 요청파라미터에 구체적으로 원하는 어떤 응답 파라미터를 받고 싶은지 작성하여 요청하므로, 필요한 데이터만 응답받을 수 있다.


## 요약

- **GraphQL**은 데이터베이스에서 데이터를 요청할 때 사용하는 언어이다.
- **GraphQL**을 사용하면 클라이언트가 요청한 데이터만 얻을 수 있다.

## 참고

[GraphQL 사이트 - 바로가기](https://graphql.org/)