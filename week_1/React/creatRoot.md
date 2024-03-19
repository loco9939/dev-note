# createRoot

브라우저 DOM 노드안에 React 컴포넌트를 나타낼 root를 생성해주는 역할을 한다.

```js
import ReactDOM from "react-dom/client";

const element = document.getElementById("root");

const root = ReactDOM.createRoot(element);

root.render(<App />);
```

### parameters

```js
createRoot(domNode, options?)
```

- domNode: DOM 요소. 리액트는 DOM 요소를 위한 root를 만들고 root에서 `render`와 같은 함수를 호출할 수 있도록 제공한다.
- options?
  - onRecoverableError: 리액트가 자동으로 에러 해결했을 때, 호출되는 콜백함수
  - identifierPrefix: useId로 생성된 id를 위해 리액트가 사용하는 prefix. 복수의 root가 같은 페이지에 있을 때 충돌을 피하기 위해 사용된다.

### returns

`createRoot`는 `render`, `unmount` 메소드를 반환한다.

> 여러 개의 root를 만들거나 여러번 render를 해도 문제 없지만, React에선 1개의 root와 1번의 render만 호출한다.

### 예제

리액트는 변경된 부분만 업데이트하기 때문에 변경되지 않는 부분은 그대로 재사용한다.

```js
import { createRoot } from "react-dom/client";
import "./styles.css";

function App({ counter }) {
  return (
    <>
      <h1>Hello, world! {counter}</h1>
      <input placeholder="Type something here" />
    </>
  );
}

const root = createRoot(document.getElementById("root"));

let i = 0;
setInterval(() => {
  root.render(<App counter={i} />);
  i++;
}, 1000);
```

위 예제에서 App 컴포넌트 안에서 counter가 변경되고 있음에도 App 컴포넌트 내부의 input의 로컬 상태(포커싱, 사용자 입력 등)은 유지된다.
