# Express

## 정의

Node.js를 위한 웹 프레임워크이다.

## 특징

- 자유롭게 활용할 수 있는 HTTP 메소드, 미들웨어를 통해 API를 작성할 수 있다.
- 기본적인 웹 애플리케이션으로 구성된 얇은 계층을 제공하여 Node.js 기능을 모호하게 하지 않는다.

## 사용방법

우선, 기본적으로 typescript, eslint를 설치해준다.

**TypeScript**

```bash
npm i -D typescript
npx tsc --init
```

**ESlint**

```bash
npm i -D eslint
npx tsc --init
```

**ts-node**

```bash
npm i -D ts-node
```

Node.js 환경에서 타입스크립트 실행을 도와주는 라이브러리이다.

타입스크립트를 자바스크립트로 컴파일하는 과정없이 Node.js에서 즉시 실행해주기 위해 사용한다.

**Nodemon**

```bash
npm i -D nodemon
```

코드를 수정할 때마다 서버를 재실행하도록 도와주는 라이브러리인 nodemon도 설치한다.

**Express**

```bash
npm i express
npm i -D @types/express
```

- typescript를 같이 사용하기 위해서 types 파일도 같이 설치해준다.

```ts
// app.ts 예제

import express from 'express';

const port = 3000;

const app = express();

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```