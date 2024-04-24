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

## 5주차 과제 - 푸드코트 키오스크

푸드코트 키오스크 과제를 진행하면서 배웠던 Jest 관련 공부 내용을 기록합니다.

### ByRole

HTMLElement의 role로 찾는 API 이다.

```ts
getByRole(
  // If you're using `screen`, then skip the container argument:
  container: HTMLElement,
  role: string,
  options?: {
    hidden?: boolean = false,
    name?: TextMatch,
    description?: TextMatch,
    selected?: boolean,
    busy?: boolean,
    checked?: boolean,
    pressed?: boolean,
    suggest?: boolean,
    current?: boolean | string,
    expanded?: boolean,
    queryFallbacks?: boolean,
    level?: number,
    value?: {
      min?: number,
      max?: number,
      now?: number,
      text?: TextMatch,
    }
  }): HTMLElement
```

#### 사용예제

```ts
describe('RestaurantTable 컴포넌트', () => {
  // given
  const { restaurants } = fixtures;

  beforeEach(() => {
    render(<RestaurantTable restaurants={restaurants} />);
  });

  // when
  context('RestaurantTable 컴포넌트가 렌더링되면', () => {
    // then
    it('restaurants의 길이만큼 행이 렌더링된다.', () => {
      const rows = screen.getAllByRole('row');
      expect(rows.length).toBe(restaurants.length + 1); // thead의 row있으니 +1
    });
  }
})
```

- table 컴포넌트 내부의 thead, tbody 속 tr(row) 요소를 모두 찾아준다.
- 테스트 코드에서 thead의 tr까지 포함되어 찾아주었으므로, 전달된 restaurants 배열의 길이에 1을 더해준 것과 동일한지 비교하였다.

[ByRole - React-Testing-Library](https://testing-library.com/docs/queries/byrole/)

### toHaveAttribute

attribute 속성을 가지고 있는지 체크하는 API이다.

#### 사용예제

```ts
describe('Orders 컴포넌트', () => {
  // given
  const setReceipt = jest.fn();

  beforeEach(() => {
    setReceipt.mockClear();
  });

  // when
  context('로컬스토리지에 저장된 orders의 menu가 빈 배열이라면', () => {
    beforeEach(() => {
      render(<Orders setReceipt={setReceipt} />);
    });

    // then
    it('주문 버튼은 disabled 표시된다.', () => {
      const orderBtn = screen.getByText(/주문/);
      expect(orderBtn).toHaveAttribute('disabled');
    });
  });
})
```

- 주문정보에 등록된 메뉴가 없으면 "주문"버튼을 disabled 처리한 것을 테스트 코드로 작성하였다.

### waitFor

콜백함수 내부의 테스트들을 테스트 완료할 때 까지 대기하는 API이다.

```ts
export interface waitForOptions {
  container?: HTMLElement
  timeout?: number
  interval?: number
  onTimeout?: (error: Error) => Error
  mutationObserverOptions?: MutationObserverInit
}

export function waitFor<T>(
  callback: () => Promise<T> | T,
  options?: waitForOptions,
): Promise<T>
```

#### 사용예제

```ts
describe('FilterableRestaurantTable 컴포넌트', () => {
  beforeEach(() => {
    render(<FilterableRestaurantTable />);
  });

  context('올바르게 렌더링된다면', () => {
    it('useFetchRestaurants 훅으로 restaurants 데이터를 요청한다.', async () => {
      // NOTE: render함수를 waitFor 내부에 넣으면 테스트 계속 돈다.
      await waitFor(() => {
        expect(screen.getByText(/혹등고래카레/)).toBeInTheDocument();
      });
    });
  });

  context('검색어를 "메"라고 변경하면', () => {
    it('화면에 "메가반점", "메리김밥"은 렌더링되고 "혹등고래카레"는 렌더링되지 않는다.', async () => {
      fireEvent.change(screen.getByLabelText('검색'), {
        target: { value: '메' },
      });
      await waitFor(() => {
        expect(screen.getByText(/메가반점/)).toBeInTheDocument();
        expect(screen.getByText(/메리김밥/)).toBeInTheDocument();
        expect(screen.queryByText(/혹등고래카레/)).toBeNull();
      });
    });
  });
})
```

- 우선 테스트 하기전에 컴포넌트를 렌더링한다.
- 서버에서 데이터를 받아오는 시간이 있으므로 데이터 응답 후 화면에 "혹등고래카레"가 등장할 때 까지 대기한다.
  - 옵션값으로 `timeout`과 `interval`을 설정해줄 수 있다.
- 이벤트를 발생시키고 필터링된 메뉴들을 테스트했다.