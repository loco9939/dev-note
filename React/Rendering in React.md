# Rendering in React

## 렌더링이란?

문맥에 따라 다양하게 해석될 수 있지만, 리액트에선 **이미지를 생성하는 과정**으로 설명된다.

우리는 DOM API를 활용하여 DOM에 접근하고 조작하며 UI를 변경해왔다.

하지만 React는 DOM보다 한층 더 위의 레이어에서 실행되는 라이브러리이므로, DOM API를 사용하지 않고 `VDOM`이라는 또 다른 추상화 계층을 조작한다.

## VDOM

리액트가 VDOM을 조작하여 업데이트하면 이전의 VDOM 스냅샷과 비교한 뒤 변경된 내용만 실제 DOM에 업데이트한다.

이전 VDOM과 새 VDOM을 비교하는 프로세스를 `diffing`이라고 한다.

실제 DOM을 업데이트 하는 것은 UI를 다시 그리기 때문에 느리지만 VDOM은 UI를 다시 그리지 않기 때문에 효율적이다.

## React는 언제 리렌더링이 발생하는가?

React는 setState 함수를 실행하여, 컴포넌트의 상태가 변경될 때마다 렌더링을 예약한다.

하지만 렌더링을 예약하는 것이므로 ❗️**즉시 렌더링이 발생하는 것은 아니다.**

```js
const App = () => {
  const [message, setMessage] = React.useState("");
  return (
    <>
      <Tile message={message} />
      <Tile />
    </>
  );
};
```

React가 렌더링 된다는 것은 컴포넌트의 render 함수가 호출될 뿐만 아니라 **props 변경여부와 상관없이** 모든 자식 컴포넌트들도 리렌더링된다는 것을 의미한다.

그렇게되면, 자식 컴포넌트의 render 함수도 실행되므로 예상보다 많은 JavaScript를 실행하게 된다.

## 렌더링 최적화 방법

위와 같이 부모 컴포넌트의 상태가 변경되어 모든 자식 컴포넌트가 리렌더링되는 것을 해결하기 위해 `React.memo`를 사용한다.

### 1. React.memo

`React.memo`는 props가 변경되지 않았을 때만 리렌더링을 방지하는 함수이다.

```js
const TileMemo = React.memo(({ children }) => {
  let updates = React.useRef(0);
  return (
    <div className="black-tile">
      Memo
      <Updates updates={updates.current++} />
      {children}
    </div>
  );
});
```

#### React.memo 주의점 ❗️

리렌더링을 방지하기 위해 `React.memo`를 남발하게 될 경우, 해당 컴포넌트를 명시적으로 캐시하는 일이므로 이는 VDOM의 메모리를 과도하게 소비할 수 있게된다.

때문에 큰 컴포넌트를 memo하는 것은 피해야하며, 또한 props가 자주 변경되는 컴포넌트의 대해서도 memo 사용을 피해야한다.

### 2. 컴포넌트 구조 재구성

이전 예제에서는 App 컴포넌트에서 useState로 상태값을 가지고 있어서 상태값이 변경될 때마다 모든 자식들이 리렌더링이 발생했다.

하지만, 상태를 처리하는 코드를 별도의 컴포넌트로 이동시킨다면 불필요한 리렌더링을 방지할 수 있다.

```js
const InputSelfHandling = () => {
  const [text, setText] = React.useState("");
  return (
    <input
      value={text}
      placeholder="Write something"
      onChange={(e) => setText(e.target.value)}
    />
  );
};
```

## key와 재조정

리액트가 컴포넌트를 식별하는 방법 중 하나는 key prop을 사용하는 것이다.

key prop는 형제요소에서 고유한 값을 사용해야하고, 배열의 인덱스를 사용하면 안된다고 하는 이유에 대해 알아보자.

```js
import { useState } from "react";

const mock = [
  {
    name: "Item 1",
  },
  {
    name: "Item 2",
  },
  {
    name: "Item 3",
  },
  {
    name: "Item 4",
  },
  {
    name: "Item 5",
  },
  {
    name: "Item 6",
  },
  {
    name: "Item 7",
  },
  {
    name: "Item 8",
  },
  {
    name: "Item 9",
  },
  {
    name: "Item 10",
  },
];

function List() {
  const [list, setList] = useState(mock);

  const addItem = () => {
    const newItem = {
      name: `Item ${list.length + 1}`,
    };
    setList([...list, newItem]);
  };

  const removeItem = (index) => {
    setList(list.filter((item, i) => i !== index));
  };

  return (
    <ul>
      <button type="button" onClick={addItem}>
        Add List
      </button>
      {list.map((item, index) => (
        <li key={index}>
          {item.name}
          <input type="text" />
          <button type="button" onClick={() => removeItem(index)}>
            Remove Item
          </button>
        </li>
      ))}
    </ul>
  );
}

export default List;
```

위 예시에서 key prop을 index로 사용했을 경우 아이템을 제거했을 경우, index로 비교하기 때문에 index가 같은 노드를 재사용한다.

![key prop example 1](/img/key%20prop%20example1.png)

위 상황에서 `item 4`를 제거하면 `item 5`의 input 값이 `item 5`에 유지되어야 하지만 index를 key prop으로 사용하게 되면 다음과 같은 문제가 발생한다.

![key prop example 2](/img/key%20prop%20example2.png)

`item 5`의 index를 재사용하여 `item 6`의 input에 적용되는 것을 볼 수 있다.

## 참조

[(번역) React는 컴포넌트를 언제 리렌더링 할까요?](https://megaptera.notion.site/React-c5911858618c47d2bd716718721bff6d)

[React는 컴포넌트를 언제 리렌더링 할까요?](https://felixgerschau.com/react-rerender-components/)

[React 배열의 index를 key prop으로 사용하면 안되는 이유](https://medium.com/sjk5766/react-%EB%B0%B0%EC%97%B4%EC%9D%98-index%EB%A5%BC-key%EB%A1%9C-%EC%93%B0%EB%A9%B4-%EC%95%88%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-3ce48b3a18fb)
