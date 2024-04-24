# Flux Architecture

## 정의

(구)Facebook (현)Meta에서 `MVC` 대안으로 내세운 아키텍처이다.

단방향 데이터 흐름을 활용해 `View` 컴포넌트를 구성하는 React를 보완하는 역할을 한다.

## 역사 및 배경(사용이유)

`SPA(Single Page Application)`에서 `MVC` 패턴을 사용할 때, 하나의 `View`에서 여러 `Model`을 변경할 수 있게 되고 그 반대인 경우도 발생할 수 있다.

이렇게 생겨난 수많은 `View`, `Model`을 하나의 `Controller`로 관리하다보면 복잡해질 수 있다.

예를 들면 아래 사진처럼 데이터가 어디에서 어느 방향으로 전달될지 알기 어렵게 된다.

![MVC 문제점](/img/mvc%20problem.webp)

페이스북 팀에서는 데이터가 중구난방으로 전달되는 상황을 해결하고자 **단방향 데이터 흐름**을 활용하는 `Flux` 패턴을 도입했다.

## 특징

![Flux 패턴](/img/flux.png)

- Dispatcher, Stores, Views(React 컴포넌트)로 구성되어 있다.
- React View에서 사용자가 상호작용시, View는 중앙의 Dispatcher를 통해 Action을 전파하게된다.
	- Action은 Dispatcher에게 action creator 메소드를 제공한다.
		- action creator 메소드는 action을 생성하고 type을 설정하거나 Dispatcher에게 제공하는 역할을 한다.
	- Dispatcher는 Store를 등록하기 위한 콜백을 실행한 이후 Action을 모든 Store로 전달한다.
- 애플리케이션의 데이터와 비즈니스 로직을 가진 Store는 action이 전파되면 action에 영향이 있는 모든 View를 갱신한다.

### Action

- 새로운 데이터를 포함한 간단한 객체로, type 프로퍼티로 구분할 수 있다.
- action creator 메서드로 사용자 이벤트 발생 시 해당 이벤트 발생한 정보를 담은 Action 객체를 만들어 Dispatcher에게 전달하는 역할을 한다.

### Dispatcher

- 전달받은 `Action` 객체로 어떤 행동을 할지 결정하는 공간이다.
- 주로 `switch`문으로 `action`의 `type`을 구분해 미리 작성해준 로직을 실행한다.

### Store

- 데이터, 상태를 담고 있는 공간이다.
- 개발자는 `Dispatcher`에 `Store`에 접근할 수 있도록 callback 명령을 제공할 수 있다.

### View

- `View`는 `Store`에 제공받은 데이터 및 상태를 화면에 제공하는 역할을 하는 공간이다.
- React에서 `View`는 보여주기만 하는 역할뿐 아니라 `Controller-View`라는 상위에서 하위 `View`들에게 어떤 상태나 `Dispatcher` 등을 제공하기 위해 하위 `View`를 감싸는 형태로 존재한다. 이를 통해 `Props drilling` 문제를 해결할 수 있다.

## 사용방법(예제)

```ts
// store/counter.ts
 const initState = {
	count:0
}

 function reducer(state:{count:number},action:{type:'ADD'}) {
	switch (action.type) {
		case 'ADD':
			return {
				count:state.count + 1
			}

		default:
			return state
	}
}

export {
	initState,
	reducer
}
```

- store 디렉터리에 데이터(상태)와 action type에 따라 callback 함수를 호출할 로직을 담담하는 dispatcher(reducer)를 관리한다.

```ts
// Counter.tsx
import { useReducer } from "react"
import { initState, reducer } from "../store/counter"

function Counter() {
	const [state, dispatch] = useReducer(reducer, initState)

	const handleClick = () => {
		dispatch({type:'ADD'})
	}

	return (
	<div>
		<h1>{state.count}</h1>
		<button type="button" onClick={handleClick}>Add</button>
	</div>
	)
}

export default Counter
```

- `handleClick` 함수 내부에 `{type:'ADD'}`인 `Action` 객체를 만들어서 `dispatcher`에게 전달하는 로직이 들어있다.
- 즉, `View`에서 사용자의 상호작용으로 `action` 객체가 생성되어 `dispatcher`로 전달되고 `dispatcher`에서 `action type`에 따라 `callback` 함수를 호출할 로직을 실행하여 해당 state를 변경한다.
- 다시 `View`에서는 `state`를 통해서 `store`의 데이터가 변경된 것을 감지하여 화면을 리렌더링한다.

## 참조

[Flux 한국어 사이트](https://haruair.github.io/flux/docs/overview.html)
[Flux 패턴 - medium](https://medium.com/hcleedev/web-react-flux-%ED%8C%A8%ED%84%B4-88d6caa13b5b)