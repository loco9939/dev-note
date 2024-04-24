# TSyringe

## 정의

TypeScript를 지원하는 의존성 주입(DI) 컨테이너를 제공하는 라이브러리이다.

## 사용이유

TSyringe 라이브러리를 사용하여 External Store 관리하는데 활용할 수 있고 Props drilling 문제를 우아하게 해결할 수 있다.

> 나중에 Redux를 배울 때 이러한 구조로 설계되었구나~ 라고 이해하는데 도움이 될 것이다.

## 사용방법

### 설치

```bash
npm i tsyringe reflect-metadata
```

- Reflect API의 polyfill을 추가해줘야한다.

### 환경설정

```json
// tsconfig.json
{
	"experimentalDecorators": true,
	"emitDecoratorMetadata": true
}
```

- tsconfig의 decorator 옵션값을 true로 바꿔준다.

```ts
// main.tsx와 src/setupTests.ts
import 'reflect-metadata';
```

- 소스코드 작성하는 곳의 진입점에 `import reflect-metadata`를 추가해줘야한다.

> test 코드에서도 에러가 발생하지 않게 똑같이 추가해준다.

### singleton 예제

```ts
// src/store/CounterStore.ts
import { singleton } from 'tsyringe';

type Listener = () => void

@singleton()
export default class CounterStore {
	count = 0

	listeners = new Set<Listener>()

	increase() {
		this.counter += 1
		this.publish()
	}

	decrease() {
		this.counter -= 1
		this.publish()
	}

	publish() {
		listeners.forEach((listener) => listener())
	}

	addListener(listener: Listener) {
		this.listeners.add(lisetener)
	}

	removeListener(listener: Listener) {
		this.listeners.delete(lisetener)
	}
}
```

- class로 CounterStore를 선언해준다.
- 상태값 count를 선언한다.
- CounterStore의 인스턴스를 사용할 곳에서 addListener를 해줘야지만 싱글톤 클래스가 관리하는 listeners에 등록된다.
	- 만약 인스턴스를 사용하는 컴포넌트가 unmount되면 리스너도 제거해주기 위해 removeListener도 선언한다.
- count 값을 변경할 함수를 캡슐화하기 위해 class 내부에 선언한다.
	- 함수 내부에는 listeners를 모두 호출하는 로직이 포함되어 있다. 이유는 다음 단락에서 설명한다.



```ts
// useCounterStore.ts
import { useEffect } from 'react';
import { container } from 'tsyringe';
import CounterStore from '../store/CounterStore';
import useForceUpdate from './useForceUpdate';

export default function useCounterStore() {
  const store = container.resolve(CounterStore);

  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);

    return () => {
      store.removeListener(forceUpdate);
    };
  }, [store, forceUpdate]);

  return store;
}
```

- store를 사용하여 store 내부의 값을 변경할 때마다 리렌더링을 강제하는 로직을 포함하여 hook으로 만들었다.
	- useForceUpdate hook은 강제로 리렌더링을 발생시키는 함수이다.
	- useState를 사용하지 않기 때문에 count 값이 변경될 때마다 강제로 리렌더링을 발생시켜주기 위해 addListener 메서드로 forceUpdate를 등록해준다.
- 앞서 increase, decrease 캡슐화 로직에 모든 listeners를 호출하는 로직이 포함된 이유가 바로 강제 리렌더링을 발생시키기 위함이다.

```ts
// Counter.tsx
import useCounterStore from '../hooks/useCounterStore';

export default function Counter() {
  const store = useCounterStore();

  return (
    <div>
      <h1>
        Count:
        {store.counter}
      </h1>
    </div>
  );
}
```

- Counter 컴포넌트에서는 상태값만 보여준다.

```ts
// CounterControl.tsx
import useCounterStore from '../hooks/useCounterStore';

export default function CounterControl() {
  const store = useCounterStore();

  const handleClickIncrease = () => {
    store.increase();
  };

  const handleClickDecrease = () => {
    store.decrease();
  };

  return (
    <div>
      <button type="button" onClick={handleClickIncrease}>Increase</button>
      <button type="button" onClick={handleClickDecrease}>Decrease</button>
    </div>
  );
}
```

- CounterControl 컴포넌트에서는 상태변경로직만 담당한다.

#### 태스트 코드 작성

```ts
// App.test.tsx
import {
  fireEvent,
  render,
  screen,
} from '@testing-library/react';
import { container } from 'tsyringe';

const context = describe;
describe('App ', () => {
  beforeEach(() => {
    container.clearInstances();
  });

  context('when press increase button once', () => {
    test('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));

      expect(screen.getAllByText('Count:1')).toHaveLength(2);
    });
  });

  context('when press increase button twice', () => {
    test('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));
      fireEvent.click(screen.getByText('Increase'));

      expect(screen.getAllByText('Count:2')).toHaveLength(2);
    });
  });
});
```

- 테스트 코드를 작성할 때 주의할 점은 싱글톤으로 작성한 CounterStore는 전역변수처럼 동작하기 때문에 테스트 하기전에 클래스 내부를 초기화 해줘야한다.

> 그렇지 않으면 위 테스트에서 Increase 버튼 1회 클릭 테스트와 Increase 버튼 2회 클릭 테스트가 독립적으로 발생하지 않아 명확한 테스트가 어렵다. 🥲

## 참조

[TSyringe - github](https://github.com/microsoft/tsyringe)