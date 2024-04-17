# MSW

## 정의

MSW(Mock Service Worker)는 코드 레벨에서의 테스트가 아닌 백엔드와의 네트워크 단계에서의 테스트를 오프라인 작업 환경에서 지원하는 라이브러리이다.

> Service Worker란, 브라우저와 네트워크 사이의 프록시 서버 역할을 한다. 서비스 워커의 주된 목적은 오프라인 경험을 생성하고 네트워크 요청을 가로채서 네트워크 사용 가능 여부에 따라 적절한 행동을 취할 수 있게 도와주는 것이다.

## 사용이유

오프라인 상황에서 API 네트워크 통신을 테스트 해볼 수 있으므로, API 스펙은 나왔지만 아직 백엔드가 완성되기 전에 가볍게 네트워크 테스트를 해볼 수 있어 유용하다.

직접 Express 서버를 만들 수 있지만 MSW를 사용하면 테스트 코드도 지원할겸 웹 브라우저를 지원하는 용도로 사용하기에 적절하다.

## 특징

- 오프라인에서 사용할 수 있다.(클라이언트단에서 실행)
- REST API 및 GraphQL 지원
- TypeScript 지원
- 네트워크 단계에서의 가로챈다.
- 함수형 문법 사용

## 사용방법

### 설치

```bash
npm install -D msw@latest
```

### jest.config.ts 환경설정

```ts
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: [
    '@testing-library/jest-dom/extend-expect',
    '<rootDir>/src/setupTests.ts', // setupTests.ts 파일 경로 추가
  ],
  transform: {
    '^.+\\.(t|j)sx?$': ['@swc/jest', {
      jsc: {
        parser: {
          syntax: 'typescript',
          jsx: true,
          decorators: true,
        },
        transform: {
          react: {
            runtime: 'automatic',
          },
        },
      },
    }],
  },
};
```

### 환경 설정 기본 파일 추가

#### 1. 프록시 서버 파일 추가

```ts
// src/mocks/server.ts
import { setupServer } from 'msw/node';

import handlers from './handlers';

const server = setupServer(...handlers);

export default server;
```

- 서버를 설치한다.

#### 2. 프록시 서버 연결

```ts
// src/setupTests.ts
import 'whatwg-fetch';
import server from './mocks/server';

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));

afterAll(() => server.close());

afterEach(() => server.resetHandlers());
```

- `beforeAll`을 통해 테스트를 실행하기 전 서버를 켜준다.
- `afterAll`을 통해 모든 테스트가 끝나면 서버를 닫아준다.
- `afterEach`을 통해 각 테스트가 끝날 때마다 handlers를 초기화해준다.

> Jest는 node 환경에서 실행되는데, node 환경에서는 fetch API가 없어 사용할 수 없다. 이를 해결하기 위해 fetch API의 polyfill을 추가해준다.

fetch-polyfill 설치

```bash
npm install whatwg-fetch --save
```

#### 3. 테스트 API 작성

```ts
// src/mocks/handlers.ts
import { rest } from 'msw';

const BASE_URL = 'http://localhost:3000';

const handlers = [
  rest.get(`${BASE_URL}/products`, (req, res, ctx) => {
    const products = [
      {
        category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
      },
    ];

    return res(
      ctx.status(200),
      ctx.json({ products }),
    );
  }),
];

export default handlers;
```

- REST API를 사용할 것이므로 `rest`를 불러온다.
- 서버에서 응답해주는 것처럼 `context(ctx)`를 사용하여 `status` 값과 `json` 데이터를 `res`함수의 인자로 호출한다.

## 참조

[Service Worker - MDN](https://developer.mozilla.org/ko/docs/Web/API/Service_Worker_API)

[Github에서 만든 fetch polyfill](https://github.com/JakeChampion/fetch)
