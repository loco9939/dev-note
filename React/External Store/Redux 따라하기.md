# Redux 따라하기

이전 시간에 배웠던 [TSyringe](/React/External%20Store/TSyringe.md)를 사용하여 만든 Store를 가지고 Redux를 따라해보자.

## Counter 예제 코드

### BaseStore

Store의 인터페이스용 BaseStore를 생성한다.

```ts
type Listener = () => void;

export type Action<Payload> = {
	type:string;
	payload:Payload
}

type Reducer<State, Payload> = (state:State, action:Action<Payload>) => State

type Reducers<State> = Record<string, Reducer<State, any>>

export default class BaseStore<State> {
	state:State;

	reducer:Reducer<State, any>;

	listeners = new Set<Listener>()

	constructor(initialState:State, reducers:Reducers<State>) {
		this.state = initialState;

		this.reducer = (state:State, action:Action<any>) => {
			const reducer = Reflect.get(reducers, action.type)

			if (!reducer) {
				return state
			}
			return reducer(state, action)
		}
	}

	dispatch(action:Action<any>) {
		this.state = this.reduce(this.state, action);
		this.publish()
	}

	publish() {
		this.listeners.forEach((listener) => listener())
	}

	addListerner(listerner:Listener) {
		this.listeners.add(listener)
	}

	removeListener(listener:Listener) {
		this.listeners.delete(listener)
	}
}
```

- state: 초기 상태값을 등록한다.
- reducer: state값과 action을 받아서 action type에 맞는 로직을 수행하고 변경된 state를 반환한다.
- constructor: 초기 상태값과 reducer들의 모은 객체 'reducers'를 받는다.
	- reducer를 재정의하는데, reducers에서 action type과 일치하는 reducer를 찾아서 로직을 실행한다.
- listeners, publish, addListener, removeListener은 store의 상태값의 변경에 대한 구독을 담당하는 로직이다.
- dispatch: action 객체를 받아서 reducer에 현재 state와 함께 전달하여 반환된 state 값으로 업데이트한 후 리렌더링을 위한 publish 로직을 실행한다.

### Store

BaseStore를 기반으로 초기 상태값과 reducers를 관리하는 Store 생성한다.

```ts
import { singleton } from 'tsyringe';

const initialState = {
	count:0,
	name:'Tester'
}

export type State = typeof initialState

const reducers = {
	increase(state:State, action:Action<number>) {
		return { ...state, count: state.count + (action.payload ?? 1)}
	},
	decrease(state:State, action:Action<number>) {
		return { ...state, count: state.count - (action.payload ?? 1)}
	}
}

export function increase(step?:number) {
	return { type: 'increase', payload: step }
}

export function decrease(step?:number) {
	return { type: 'decrease', payload: step }
}

@singleton()
export default class Store extends BaseStore<State> {
	constructor() {
		super(initialState, reducers)
	}
}
```

- reducers: action type을 key로 등록하여 해당하는 action type에 따라 전달받은 state와 payload를 가지고 로직을 실행 후 state를 반환하는 reducer들을 관리하는 객체
- increase, decrease는 action creator 메서드라고 생각하면 된다. action 객체를 생성하는 역할을 한다.
- BaseStore를 기반으로 Store 클래스를 확장하여 초기값과 reducers만 전달하여 싱글톤 패턴으로 작성했다.

### Hooks

#### useSelector ⭐️

Store의 state 중 원하는 상태값만 반환해주는 hook

```ts
import { useEffect, useRef } from 'react';
import { container } from 'tsyringe';
import Store, { State } from '../stores/Store';
import useForceUpdate from './useForceUpdate';

type Selector<T> = (state:State) => T

export default function useSelector<T>(selector:Selector<T>):T {
	const store = container.resolve(Store)

	const state = useRef(selector(store.state))

	const forceUpdate = useForceUpdate();

	useEffect(() => {
		const update = () => {
			const newState = selecter(store.state)

			if (newState !== state.current) {
				forceUpdate();
				state.current = newState
			}
		}

		store.addListener(update)
		
		return () => store.removeListener(update);
	},[store, forceUpdate])

	return selector(store.state)
}
```

- 상태값 반환하는 로직내부에서 해당 상태값에 대한 listener를 등록하고 제거하는 로직을 포함했다.
	- 왜냐하면 개발자는 useSelector를 통해서만 상태값을 참조할 수 있어 상태값을 참조하는 순간 해당 상태값에 대한 변경을 감지 및 리렌더링 발생하는 로직도 포함했다.

##### useRef 대신 useState를 사용하면 안되는 이유 ❗️

`useEffect` 내부의 `update` 함수 정의는 의존성 배열 `store`, `forceUpdate`가 바뀌지 않는 이상 최초에 1회만 실행된다.

그리고 `update` 함수 내부에서 참조하는 `state` 값은 최초 실행시 초기값을 참조하는 **JavaScript Closure(클로저)** 이다.

`store`의 상태값이 변경되면 `newState` 값이 변경되지만, `state`는 클로저이므로 계속 초기값만 바라보게된다. 즉, `newState !== state`가 항상 True가 된다.

이 경우 `store`의 name을 한번 변경한 이후, count 값만 변경하더라도 name을 참조하는 컴포넌트도 리렌더링이 발생하는 문제가 발생한다.

이를 해결하기 위해서는 클로저인 state 값을 `useRef`로 참조값을 참조하도록 구현하고 `newState !== state.current`이 True인 경우에만 리렌더링 발생(`forceUpdate`) 및 `state.current` 값 변경하도록 수정하면 해결된다.

#### useDispatch

action 객체를 받아 reducer를 기반으로 상태값 변경을 명령하는 dispatch를 반환한다.

```ts
import { container } from 'tsyringe';
import { Action } from '../stores/BaseStore';
import Store from '../stores/Store';

export default function useDispatch() {
  const store = container.resolve(Store);
  return (action:Action<any>) => store.dispatch(action);
}
```

- 반드시 store를 먼저 선언한 후 dispatch를 참조하여야만 사용이 가능하다.

ex) `const { dispatch } = container.resolve(Store);`로 사용하면 에러 발생 ❗️

### useDispatch, useSelector 사용법

```ts
// Counter.tsx
import useSelector from '../hooks/useSelector';

export default function Counter() {
  const count = useSelector((state) => state.count);

  return (
    <div>
      <h1>
        Counter 컴포넌트
      </h1>
      <p>
        Count:
        {count}
      </p>
    </div>
  );
}
```

```ts
// CounterControl.tsx
import useDispatch from '../hooks/useDispatch';
import useSelector from '../hooks/useSelector';
import { decrease, increase } from '../stores/Store';

export default function CounterControl() {
  const dispatch = useDispatch();
  const count = useSelector((state) => state.count);

  return (
    <div>
      <h1>Counter Control 컴포넌트</h1>
      <p>
        Count:
        {count}
      </p>
      <button
        type="button"
        onClick={() => dispatch(increase())}
      >
        Increase
      </button>
      <button
        type="button"
        onClick={() => dispatch(increase(10))}
      >
        Increase 10
      </button>
      <button
        type="button"
        onClick={() => dispatch(decrease())}
      >
        Decrease
      </button>
      <button
        type="button"
        onClick={() => dispatch(decrease(10))}
      >
        Decrease 10
      </button>
    </div>
  );
}
```