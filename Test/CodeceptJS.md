# CodeceptJS

## 정의

CodeceptJS는 BDD 스타일 문법을 지원하는 E2E 자동화 테스팅 라이브러리이다.

## 사용이유

`Playwright` 라이브러리를 사용하여 E2E 테스트를 구체적으로 작성할 수도 있지만, 개발자에게 친숙한 언어로 구성되어 있다.

다른 직군이나 테스팅 라이브러리를 모르는 개발자도 쉽게 이해할 수 있는 테스트 코드를 작성하기 위해 `CodeceptJS`를 사용한다.

## 특징

- 시나리오를 작성하여 사용자 관점에서 가독성이 좋은 테스트를 작성할 수 있다.
- Playwright, Puppeteer 등과 같은 다른 라이브러리를 통해 테스트를 실행할 수 있다.
- 유용한 Locators를 사용하여 CSS, XPath 같은 요소를 찾을 수 있다.

## 사용방법

### 설치

```bash
npx create-codeceptjs .
```

### Init

```bash
npx codeceptjs init
```

```bash
? Do you plan to write tests in TypeScript? 'n'
? Where are your tests located? './tests/**./*_test.js'
? What helpers do you want to use? 'Playwright'
? Where should logs, screenshots, and reports to be stored? './output'
? Do you want to enable localization for tests? 'English (no localization)'
? [Playwright] Base url of site to be tested 'http://localhost:8080'
? [Playwright] Show browser window 'y'
? [Playwright] Browser in which testing will be performed. Possible options: chromium, firefox, webkit or electron 'chromium'
```

- test할 폴더의 경로를 알맞게 설정한다.
- 테스트를 실행할 사이트의 경로는 현재 FE URL과 동일하게 작성한다.

### 테스트 작성

설정이 완료되면 `tests` 폴더에 `google_test.js` 파일이 생성된 것을 확인할 수 있다.

```js
Feature('Google');

Scenario('Search “CodeceptJS”', ({ I }) => {
  I.amOnPage('<https://www.google.com/ncr>');
  I.fillField('[name="q"]', 'CodeceptJS');
  I.click('Google Search');
  I.see('codecept.io');
});
```

실행
```bash
npm run codeceptjs
```

### 5주차 과제 - 푸드코트 키오스크 타입 오류

```ts
// /tests/assignment_test.ts
Feature('과제 테스트')

Scenario('메뉴판 필터링', ({ I }) => {
  I.amdOnPage('/')
  ...
})
```

위 코드에서 `I` 형식에 대한 타입을 불러올 수 없다는 문제가 있었다.

### 해겳

```ts
// step_files.ts
export = () => actor({
  ...
});
```

- `step_files.ts` 파일을 위와 같이 수정하면 된다.

## 참조

[CodeceptJS](https://codecept.io/)

[CodeceptJS 사용 예시](https://megaptera.notion.site/CodeceptJS-da08dc50ba9549f69be468ab8ebc13bf)