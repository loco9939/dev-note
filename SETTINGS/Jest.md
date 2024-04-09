# Jest

Jest는 자바스크립트 테스팅 라이브러리로, TypeScript, React 등의 프로젝트에서 사용할 수 있다.

## 함수 테스트 코드 작성하기

내가 만든 함수가 버그 없이 정상 작동한다는 것을 보장하기 위해 테스트 코드를 작성한다.

다른 사람이 만든 테스트 코드만 보고도 해당 함수가 어떤 역할을 하고 혹시나 버그가 발생하진 않을지 예측이 용이하다는 장점이 있다.

- Go 언어에서 탄생한 RSpec Best Practice를 참고하여 `BDD(Behavior-Driven-Development) 스타일`로 작성한다.
- `Given-When-Then` 구조로 작성하여 가독성과 이해를 돕는다.
  - 주어진 것은 무엇인가?
  - 언제(어떤 상황일 때) 발생하는가?
  - 그래서 어떤 결과가 나오는가?

```ts
// 작성 예시
describe('함수명', () => {
  context('파라미터 설명', () => {
    it('결과 설명', () => {
      expect(함수호출).관련 API 호출; // 해당 함수 호출 시 테스트 결과 비교
    })
  })
})
```

### add 함수 코드 작성 예시

```ts
function add(x: number, y: number): number {
  return x + y;
}

const context = describe;

describe('add 함수', () => {
  context('두 개의 양수가 주어졌을 때', () => {
    it('항상 두 개의 숫자보다 큰 값을 돌려준다.', () => {
      expect(add(1, 2)).toBeGreaterThan(1);
      expect(add(1, 2)).toBeGreaterThan(2);
    });
  });

  context('하나의 양수와 하나의 음수가 주어졌을 때', () => {
    it('항상 하나의 양수보다 작은 값을 돌려준다.', () => {
      expect(add(1, -1)).toBeLessThan(1);
      expect(add(3, -2)).toBeLessThan(3);
    });
  });
});
```

- `describe`: 함수명을 적어준다.
- `context`: 상황(인자)를 설명한다.
- `it`: 결과를 설명한다.

## 컴포넌트 UI 테스트 코드 작성하기

React UI 테스트에 특화된 `React-Testing-Library`를 사용한다.

```ts
import {screen, render} from '@testing-library/react';
import Greeting from './Greeting';

test('Greeting', () => {
  render(<Greeting name='world' />);

  screen.getByText('Hello, world!');

  screen.getByText(/Hello/);
});
```

- `render`: 컴포넌트 렌더링이 제대로 되는지 테스트한다.
- `screen`: 브라우저 스크린에서 테스트할 API를 호출한다.

### screen API

#### getBy...

인자가 **정확히 1개** 매치 되는게 있으면 노드를 반환한다. 즉, 2개 이상있어도 테스트 실패한다.

❗️ 주의할 점은 문자열을 전달했을 경우, 해당 문자열과 토시하나라도 다르면 테스트에 실패한것으로 판단한다.

ex) 화면에 `Hello world!`가 렌더링 되고 있고 `getByText('Hello world')`를 호출하면 `!`가 빠져있으므로 테스트에 실패한다.

만약 텍스트 일부분만 포함하고 있는지 체크하고 싶다면, 정규표현식을 사용한다.

```ts
screen.getByText(/Hello/)
```

#### queryBy...

화면에 매치되는 노드를 반환하고 매치되는 것이 없다면 null을 반환한다. 2개 이상 노드를 찾는다면, 테스트에 실패한다.

`queryBy API`는 주로 존재하지 않는 요소를 단언하는데 유용하다.

```ts
screen.queryByText(/Hi/);
```

- `Hi`에 매칭되는 것이 없으므로 `null`을 반환한다.

#### findBy...

매치되는 노드를 찾았을 때 resolve 되는 Promise 객체를 반환한다. 매치되는 요소가 없거나 2개 이상이면 Promise 객체는 기본 시간인 1000ms 후에 reject 된다.

![쿼리 타입 종류](../img/type%20of%20queries.png)