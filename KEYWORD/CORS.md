# CORS

## 정의

교차 출처 리소스 공유(Cross-Origin Resource Sharing, CORS)란, 한 출처에서 실행중인 웹 애플리케이션이 다른 출처의 자원에 접근할 수 있도록 권한을 부여하도록 브라우저에 알려주는 체제이다.

웹브라우저는 동일 출처 정책(Same Origin Policy)에 따라 웹 페이지와 리소스를 요청한 곳이 서로 다른 출처(포트까지 포함)일 때, **브라우저가 서버에서 얻은 결과를 사용할 수 없도록 막는다.**

> **프로토콜, 포트, 호스트가 같은 경우 두 URL은 동일한 출처를 가진다.**

> 이 때, 서버에 요청하고 응답을 받아오는 것까지는 이미 진행이 완료된 상태인 것을 주의하자❗️

> 브라우저가 동일 출처 정책을 따르는 이유는 악의적인 웹사이트가 브라우저에서 JS를 실행하여 사용자가 로그인 한 타사의 웹메일 서비스나 회사 인트라넷(공용 IP 주소가 없어 공격자의 직접적인 접근으로부터 보호)에서 데이터를 읽고 공격자에게 전달하는 것을 방지하기 위함이다.

## 해결방법

서버에서 Headers에 "Access-Control-Allow-Origin" 속성을 추가하거나 Express를 사용한다면 CORS 미들웨어를 사용하여 해결할 수 있다.

CORS 설치

```bash
npm i cors
npm i -D @types/cors
```

CORS 미들웨어 사용

```js
import express from 'express';
import cors from 'cors';

const app = express();

app.use(cors());
```

## 참조

[교차 출처 리소스 공유(CORS) - MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

[동일 출처 정책 - MDN](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)