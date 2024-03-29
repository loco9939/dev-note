# OverReact by Dan abramov

## Host tree(DOM tree)

React는 시간이 지남에 따라 변하는 트리를 출력한다. 이들은 React 외부의 호스트 환경의 일부이기 때문에 이를 `호스트 트리`라고 부른다.

- DOM 트리
- JSON 객체

`호스트 트리`는 자체명령어 API가 있고 React는 그 위에 있는 레이어이다.

> ex) appendChild() method

> 🎁 React는 `호스트 트리` 위에 있는 레이어이므로, DOM API를 호출하지 않는다.

우리는 이를 통해 UI를 표현하려고 한다.

이 때 React를 사용하면 상호작용, 네트워크 응답, 타이머 등과 같은 외부 이벤트에 응답하여 복잡한 호스트 트리를 예측 가능하게 조작하는 프로그램을 작성하는데 도움된다.

## React Element

`호스트 트리`에서 가장 작은 구성 요소는 `호스트 인스턴스(노드)`이다. React에서 가장 작은 구성 요소는 `React Element` 이다. 그리고 이는 JavaScript 객체이다.

```js
// JSX is a syntax sugar for these objects.
// <button className="blue" />
{
  type: 'button',
  props: { className: 'blue' }
}
```

호스트 트리처럼 React Element도 트리를 형성할 수 있다.

```js
// JSX is a syntax sugar for these objects.
// <dialog>
//   <button className="blue" />
//   <button className="red" />
// </dialog>
{
  type: 'dialog',
  props: {
    children: [{
      type: 'button',
      props: { className: 'blue' }
    }, {
      type: 'button',
      props: { className: 'red' }
    }]
  }
}
```

✅ **주목할 점은 React Element에는 고유한 Id가 없다는 점이다.** 이들은 항상 재생산되고 버려지기 때문이다.

> React Element는 불변이다. 하위 항목이나 속성을 변경할 수 없고 다른 것을 렌더링하고 싶다면 새로운 React Element 트리를 사용해야한다. React Element는 특정 시점에 UI가 어떤 모습인지를 캡처하여 보여준다.

## Entry Point

React 렌더러에는 `호스트 인스턴스(노드)` 내부에 특정 `React Element` 트리를 렌더링하도록 React에게 지시할 수 있는 API가 있다.

```js
ReactDOM.render(
  // { type: 'button', props: { className: 'blue' } }
  <button className="blue" />,
  document.getElementById("container")
);
```

## Reconciliation ⭐️

React의 역할은 호스트 트리가 React Element 트리와 일치하도록 만드는 것이다. 이를 위한 방법은 2가지가 있다.

1. 기존 트리를 초기화하고 처음부터 다시 만든다.

```js
let domContainer = document.getElementById("container");
// Clear the tree
domContainer.innerHTML = "";
// Create the new host instance tree
let domNode = document.createElement("button");
domNode.className = "red";
domContainer.appendChild(domNode);
```

하지만 이럴경우, DOM 접근하기 때문에 속도도 느리고 포커스, 스크롤 등 정보가 손실된다.

우리는 React가 기존에 호스트 인스턴스에서 업데이트 하기를 원한다.

```js
let domNode = domContainer.firstChild;
// Update existing host instance
domNode.className = "red";
```

즉, React는 React Element와 일치하도록 기존 호스트 인스턴스를 업데이트할 시기와 새 요소를 생성할 시기를 결정해야한다.

> 트리의 동일한 위치에 있는 요소 유형이 이전 렌더링과 다음 렌더링간에 일치하면 React는 기존 호스트 인스턴스를 재사용한다.

```js
// let domNode = document.createElement('button');
// domNode.className = 'blue';
// domContainer.appendChild(domNode);
ReactDOM.render(
  <button className="blue" />,
  document.getElementById("container")
);

// Can reuse host instance? Yes! (button → button)
// domNode.className = 'red';
ReactDOM.render(
  <button className="red" />,
  document.getElementById("container")
);

// Can reuse host instance? No! (button → p)
// domContainer.removeChild(domNode);
// domNode = document.createElement('p');
// domNode.textContent = 'Hello';
// domContainer.appendChild(domNode);
ReactDOM.render(<p>Hello</p>, document.getElementById("container"));

// Can reuse host instance? Yes! (p → p)
// domNode.textContent = 'Goodbye';
ReactDOM.render(<p>Goodbye</p>, document.getElementById("container"));
```

## Conditions

React 요소가 업데이트 사이에 일치할 때만 호스트 인스턴스를 재사용한다면, 조건부 컨텐츠를 어떻게 렌더링될까?

```js
// First render
ReactDOM.render(
  <dialog>
    <input />
  </dialog>,
  domContainer
);

// Next render
ReactDOM.render(
  <dialog>
    <p>I was just added here!</p>
    <input />
  </dialog>,
  domContainer
);
```

위 예에선 `input` 태그의 위치가 `p` 태그로 바뀌었으므로 재사용이 불가능하고 기존 호스트 인스턴스인 `input`을 제거하고 새로운 호스트 인스턴스인 `p`를 생성한다.

그리고 다음 라인에 `input` 호스트 인스턴스를 새로 추가한다.

하지만 우리는 DOM 재생성으로 인한 input에 대한 포커스 손실이 발생하길 원하지 않는다.

```js
function Form({ showMessage }) {
  let message = null;
  if (showMessage) {
    message = <p>I was just added here!</p>;
  }
  return (
    <dialog>
      {message}
      <input />
    </dialog>
  );
}
```

위 예에서는 앞선 문제가 발생하지 않는다. 왜냐하면 showMessage가 무엇이든지간에 input 태그의 트리 위치를 변경하지 않기 때문이다.

## Lists ⭐️

호스트 인스턴스가 동일한 위치에서 요소를 비교하면 재사용할지 새로 생성할지 결정한다는 것을 알게되었다.

그러나 이는 하위 요소가 정적이고 재정렬되지 않는 경우에서만 잘 작동한다.

```js
function ShoppingList({ list }) {
  return (
    <form>
      {list.map((item) => (
        <p>
          You bought {item.name}
          <br />
          Enter how many do you want: <input />
        </p>
      ))}
    </form>
  );
}
```

`list` 항목의 순서가 변경되면 React는 p, input이 동일한 유형임으로 판단하고 순서를 이동하지 못한다.

React는 key값이 동일해야지만 동일하다고 간주합니다.

```js
function ShoppingList({ list }) {
  return (
    <form>
      {list.map((item) => (
        <p key={item.productId}>
          You bought {item.name}
          <br />
          Enter how many do you want: <input />
        </p>
      ))}
    </form>
  );
}
```

순서가 변경되더라도 해당 품목이 동일하다고 말할 수 있는 근거가 되는 값을 key값으로 등록해줘야합니다.

## Recursion

컴포넌트 속 컴포넌트는 어떻게 사용되는지 알아보자.

컴포넌트는 함수이므로 호출이 가능하다. 단, 컴포넌트가 직접 호출하는 것이 아닌 **React에게 호출을 위임한다.**

그 과정을 나타내면 다음과 같다.

> You: `ReactDOM.render(<App />, domContainer)`

> React: 안녕하세요. App, 무엇을 렌더링하나요?

> App: 나는 Content를 포함하는 Layout을 렌더해

> React: 안녕 Layout, 무엇을 렌더링하나요?

> Layout: 나는 `<div>` 안에 자식들을 렌더해. 내 자식들은 Content야.

> React: 안녕 Content, 무엇을 렌더링하나요?

> Content: 나는 Footer 안에 몇몇 글자를 포함한 `<article>`을 렌더링해

> React: 안녕 Footer, 무엇을 렌더링하나요?Footer: 나는 몇몇 글자를 가진 `<footer>`를 렌더링해

## Inversion of Control

❓ **왜 컴포넌트를 직접 호출하면 안될까?**

그 이유는 React가 컴포넌트에 대해 알고 있으면 작업을 더 잘 수행할 수 있기 때문이다.

이로써 얻을 수 있는 장점은 다음과 같다.

1. 컴포넌트가 단순히 기능적인 것 이상의 것이된다.

리액트가 우리가 만든 컴포넌트를 관리하면서 컴포넌트에 여러 기능을 추가해주고 컴포넌트가 어디에 위치했는지 알려준다. 그래서 우리가 직접 컴포넌트를 호출하지 않아도 된다. 직접 호출하면 우리가 원하는 기능을 직접 만들어야 한다.

2. 컴포넌트 사용시 리액트가 트리를 관리할 수 있다.

리액트가 컴포넌트 호출하면 우리가 만든 트리의 개념적 구조에 대해 더 많은 정보를 알 수 있다.

예를 들면, `<Feed>`를 렌더링하다가 `<Profile>`를 렌더링할 때, 리액트는 내부의 호스트 인스턴스를 재사용하지 않아. 이것은 일반적으로 다른 뷰를 렌더링할 때 좋다.

`<PasswordForm>`과 `<MessangeChat>`사이에 input 위치가 우연히 일치한다고 하더라도 입력상태를 유지하고 싶지 않기 때문이다.

3. 리액트가 조정을 지연시킬 수 있다.

리액트가 컴포넌트 호출 제어하면 컴포넌트 호출 사이에 브라우저가 일부 작업을 처리하도록 할 수 있어서 큰 컴포넌트 트리를 다시 렌더링해도 메인 스레드를 차단하지 않을 수 있다.

## State ⭐️

앞서 요소의 개념적 위치에 따라 리액트에게 호스트 인스턴스를 재사용할지 새로 생성할지 알려준다고 말했다.

호스트 인스턴스는 포커스, 선택, 입력 등과 같은 로컬 상태를 가질 수 있다.

우리는 동일한 UI를 렌더링하는 상황에서는 로컬 상태를 유지하고 싶고, 다른 컴포넌트를 렌더링할 때에는 로컬 상태를 유지하고 싶지 않는다.

로컬 상태는 매우 유용하므로 리액트는 컴포넌트가 로컬상태를 갖도록 해준다.

즉, 리액트는 UI에 유용한 기능을 덧붙여준다. 이를 hook이라고 부른다.

```js
function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

## Consistency

우리가 조정 단계를 non-blocking 작업으로 나누고 싶더라도, 실제 호스트 트리 작업은 일관성있게 하나의 동기적인 작업으로 수행해야한다.

이렇게 하면, 사용자가 절반만 업데이트된 UI를 못보게 할 수 있고 브라우저가 사용자가 보지 않아야 할 중간 상태에 대한 불필요한 레이아웃과 스타일을 다시 계산하지 않아도 되서 좋다.

리액트가 모든 작업을 `렌더링 단계`와 `커밋 단계`로 나누는 이유가 바로 이 때문이다.

- 렌더링 단계: 리액트가 컴포넌트를 호출하고 조정하는 단계로, 중단되도 안전하며 나중에는 비동기적으로 처리된다.
- 커밋 단계: 리액트가 호스트 트리를 수정하는 단계로, 항상 동기적으로 처리된다.

## Batch(일괄처리) ⭐️

여러 컴포넌트가 동일한 이벤트에 대해 상태를 업데이트할 경우, 다음과 같은 문제가 발생할 수 있다.

```js
function Parent() {
  let [count, setCount] = useState(0);
  return (
    <div onClick={() => setCount(count + 1)}>
      Parent clicked {count} times
      <Child />
    </div>
  );
}

function Child() {
  let [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      Child clicked {count} times
    </button>
  );
}
```

이벤트가 전달되면 하위 컴포넌트의 onClick이 먼저 실행된다. 그 후 부모의 onClick을 호출한다.

만약 React가 호출에 대한 응답으로 컴포넌트를 즉시 리렌더링한다면, setState 항목을 두번 렌더링하게 된다.

setState로 인해 컴포넌트가 즉시 리렌더링이 발생하지 않는 대신 React는 모든 이벤트 핸들러를 먼저 실행한 뒤 다음 모든 업데이트를 함께 일괄 처리하는 단일 리렌더링을 발생시킨다.

이렇게하면 성능에는 도움이 될 수 있지만 다음과 같은 상황에 당황할 수 있다.

```js
const [count, setCount] = useState(0);

function increment() {
  setCount(count + 1);
}

function handleClick() {
  increment();
  increment();
  increment();
}
```

분명 증가를 3번해주었지만, 1번만 증가한다. 이를 해결하기 위해서는 update 값을 함수로 전달해줘야한다.

```js
const [count, setCount] = useState(0);

function increment() {
  setCount((c) => c + 1);
}

function handleClick() {
  increment();
  increment();
  increment();
}
```

## Effect

React 컴포넌트가 렌더링 동안에 작업을 수행하기 위해 사용한다.

```js
function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

effect는 한번만 실행되지 않는다. 처음 컴포넌트가 보여질 때와 이후 업데이트 후에도 실행된다.

effect는 클린업이 필요할 수 있다. 구독과 같은 경우에는 클린업이 필요하다. 클린업을 위해 함수를 반환할 수 있다.

```js
useEffect(() => {
  DataSource.addSubscription(handleChange);
  return () => DataSource.removeSubscription(handleChange);
});
```

리액트는 다음번 위 effect를 적용하기 전에 반환된 함수를 먼저 실행한다.

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]);
```

종속성 배열에 들어간 값이 업데이트 될 때만 실행되도록 지시할 수 있다. 하지만 JavaScript의 클로저가 익숙하지 않은 경우 문제가 발생할 수 있다.

```js
useEffect(() => {
  DataSource.addSubscription(handleChange);
  return () => DataSource.removeSubscription(handleChange);
}, []);
```

위 코드는 `[]`로 다시 실행하지 않도록 한다. 그런데, handleChange 함수는 외부에서 정의되었고 handleChange 함수 내부에서 props, state를 참조 할 수 있어 버그가 있다.

> ❗️ props, state가 바뀌게 되면 handleChange 함수가 새로 생성되는데 새로 생성되면 이전 구독했던 이벤트핸들러를 제거할 수 없다.

```js
useEffect(() => {
  DataSource.addSubscription(handleChange);
  return () => DataSource.removeSubscription(handleChange);
}, [handleChange]);
```

이를 해결하기 위해서는 effect 내부에 함수를 포함하여 변경될 수 있는 모든 항목이 포함되어 있는지 확인해야한다.

### 참조

[OverReact - dan abramov](https://overreacted.io/react-as-a-ui-runtime/)
