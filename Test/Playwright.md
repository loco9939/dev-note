# Playwright

## 정의

모던 웹 애플리케이션을 위한 E2E 자동화 테스트 라이브러리이다.

## 사용이유

내가 작성한 코드가 브라우저에서도 정상 동작하는지 사람이 매번 이벤트를 실행하거나 화면을 확인하는 방법 대신 자동화 테스트 코드를 작성해두면 나중에 해당 코드를 유지보수 할 때에도 도움이 되기 때문에 사용한다.

예를 들면, 내가 작성한 Input 컴포넌트가 문자만 입력 가능했는지.. 특수문자도 입력 가능했는지 가물가물할 때, 직접 테스트해서 확인하는 것보단 테스트 코드를 작성해두면 바로 확인할 수 있다.

## 특징

- Headless Chrome 기반으로 한 Puppeteer를 계승하여 더 많은 브라우저를 지원한다.
- 동적인 이벤트를 테스트 하기 위해 자동으로 기다렸다가 실행한다.
- 실패했을 경우, 스크린샷을 저장하여 어떤 경우에 실패했는지 확인할 수 있다.

## 사용방법

### 설치

```bash
npm i -D @playwright/test eslint-plugin-playwright
```

### 환경설정

#### 1. config 파일 추가

```ts
// playwright.config.ts
import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
  testDir: './tests',
  retries: 0,
  use: {
    channel:'chrome',
    baseURL: 'http://localhost:8080',
    headless: !!process.env.CI,
    screenshot: 'only-on-failure',
  },
};

export default config;
```

- test를 진행할 폴더 경로를 설정한다.
- channel 값으로 테스트할 브라우저를 설정한다.
- baseURL은 로컬서버 경로를 설정해준다.
- 브라우저 여는 것 없이(headless) 자동화 테스트 진행하기 위해서 CI를 설정한다.
- 실패했을 때만, screenshot을 저장해준다.

#### 2. 테스트 관련 eslint 설정

```ts
// tests/eslintrc.js
module.exports = {
  env: {
    jest: false,
  },
  extends: ['plugin:playwright/playwright-test'],
  rules: {
    'import/no-extraneous-dependencies': 'off',
  },
};
```

- `playwright`를 사용하고 `jest`를 사용하지 않으므로 `jest`는 false로 설정한다.

#### 3. 테스트 코드 작성

```ts
// tests/home.spec.ts
import { test, expect } from '@playwright/test';

test('Filter products', async ({ page }) => {
  await page.goto('/'); // root 경로로 이동해라

  await expect(page.getByText('Apple')).toBeVisible(); // 'Apple' 이 보이는가?

  const searchInput = page.getByLabel('Search'); // Search 라벨을 가진 input을 선택해라

  await searchInput.fill('a'); // input에 'a'를 채워라

  await expect(page.getByText('Apple')).toBeVisible(); // 'Apple' 이 보이는가?

  await searchInput.fill('aa'); // input에 'aa'를 채워라

  await expect(page.getByText('Apple')).toBeHidden(); // 'Apple' 이 안보이는가?
});

test('Click the “Increase” button', async ({ page }) => {
  await page.goto('/');

  const count = 13;

  await Promise.all((
    [...Array(count)].map(async () => {
      await page.getByText('Increase').click();
    })
  ));

  await expect(page.getByText(`${count}`)).toBeVisible();
});
```

- 여러번의 이벤트를 확인하기 위해서 map 함수가 `Promise`객체를 반환하고 지정한 횟수만큼 반복을 마치도록 `Promise.all`메서드를 사용했다.

### 실행

```bash
npx playwright test
```

- 브라우저가 잠깐 등장하면서 테스트를 진행하고 종료된다.

```bash
CI=true npx playwright test
```

- `playwright.config.ts` 파일에 CI 설정을 추가했으므로, 브라우저 등장하지 않고 자동화 테스트도 가능하다.

> 만약 테스트가 실패한 경우, `test-results` 디렉터리에 실패했을 때의 스크린샷이 저장된다. git으로 관리되고 있는 프로젝트라면 .gitignore에 `test-results`를 추가하여 변화를 무시하는 것도 좋은 방법이다.

`Playwright`와 비슷하지만 인간에게 좀 더 친화적인 `CodeceptJS`라이브러리도 있는데 이는 다음 시간에 알아보자.

## 참조

[Playwright - 바로가기](https://playwright.dev/)