# 재조정(Reconciliation)

선언적 API를 사용하면 개발자는 내부에서 어떤 일이 발생하는지 알지 못하여도 손쉽게 개발을 해나갈 수 있다.

하지만 내부에서 어떻게 트리를 비교하여 효과적으로 트리를 업데이트 하는지 알게되면 리액트를 더 잘 이해할 수 있을 것이다.

하나의 트리를 가지고 다른 트리로 변환하기 위한 최소한의 연산 수를 구하는 알고리즘도 n개의 엘리먼트가 있는 트리에서 시간복잡도가 `O(n^3)`이다.

그래서 React는 두 가지 가정을 기반으로 `O(n)`의 시간복잡도의 휴리스틱 알고리즘을 구현했다.

- 가정 1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만든다.
- 가정 2. 개발자가 key prop을 통해 여러 렌더링 사이에 어떤 자식 엘리먼트가 변경되지 않아야 하는지 표시해줄 수 있다.

## 비교 알고리즘(Diffing Algorithm)

두 개의 트리를 비교할 때, React는 두 element의 root element부터 비교한다. 이후 동작은 root element 타입에 따라 달라진다.

### 1. element 타입이 다른 경우

두 root element 타입이 다르면 **React는 이전 트리를 버리고 완전히 새로운 트리를 구축한다.**

> ex) `<a>` 태그에서 `<img>`로, `<article>`에서 `<comment>`로, `<button>`에서 `<div>`로 바뀌는 경우

트리를 버릴 때, 이전 DOM 노드들은 모두 파괴되고 이 때, `componentWillUnmount()`가 실행된다.

새로운 트리가 만들어질 때, 새로운 DOM 노드가 삽입되고 이 때, `componentWillMount()`가 실행되고 `componentDidMount()`가 이어서 실행된다.

root element 하위의 모든 컴포넌트 역시 `unmount`된다.

### 2. element 타입이 같은 경우

```js
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```

두 element를 비교하면, 타입이 `div`로 동일하므로, React는 현재 DOM에서 className만 수정한다.

## React가 state를 보존하거나 초기화 하는 방법 ⭐️

앞서 언급한 타입에 따라 비교하는 것은 과거의 React 공식문서의 버전이다.

최신 React 공식문서에는 React가 두 트리를 비교하는 기준은 **렌더트리에서 해당 컴포넌트의 위치이다.**

```js
function Count() {
	const [score, setScore] = useState(0)

	return (
		<div>
			<h1>{score}</h1>
			<button onClick={() => setScore(score+1)}>Add One</button>
		</div>
	)
}

function App() {
	const [showSecondCount, setShowSecondCount] = useState(true);
	return (
		<div>
			<Count />
			{showSecondCount && <Count />}

			<label>
				<input
					type="checkbox"
					checked={showSecondCount}
					onChange={(e) => setShowSecondCount(e.target.checked)}
				/>
				Render the second counter
			</label>
		</div>
	)
}
```

위 코드에서 `Render the second counter`를 체크 해제하면, 두번째 `Count` 컴포넌트는 렌더링이 중단되고 즉시 state가 사라진다. 

왜냐하면 React가 컴포넌트를 제거할 때, 컴포넌트의 state도 제거하기 때문이다.

그리고 `Render the second counter`를 체크하면, `Count` 컴포넌트와 본인의 state를 초기화하고 DOM에 추가한다.

✅ **React는 컴포넌트가 UI에서 동일한 위치에 렌더링되는 한 컴포넌트의 state는 보존한다.**

만약, 컴포넌트가 사라지거나 동일한 위치에 다른 컴포넌트가 렌더링되면, React는 기존 컴포넌트의 state를 제거한다.

### React는 동일한 위치에서 동일한 컴포넌트의 state를 보존한다.

```js
function Count({isFancy}) {
	const [score, setScore] = useState(0)

	let className = 'counter';
	if (isFancy) {
		className += ' fancy';
	}
	return (
		<div
		className={className}
		>
			<h1>{score}</h1>
			<button onClick={() => setScore(score+1)}>Add One</button>
		</div>
	)
}

function App() {
	const [isFancy, setIsFancy] = useState(false);
	return (
		<div>
			{isFancy ? (
				<Count isFancy={true} />
			) : (
				<Count isFancy={false} />
			)}
			<label>
				<input
					type="checkbox"
					checked={isFancy}
					onChange={(e) => setIsFancy(e.target.checked)}
				/>
				Use fancy styling
			</label>
		</div>
	)
}
```

위 예제는 `Count` 컴포넌트의 스타일만 변경해주고 있다. checkbox를 아무리 바꿔도 `Count` 컴포넌트의 위치는 변경되지 않기 때문에 컴포넌트의 state를 보존한다.

### React는 동일한 위치에서 다른 컴포넌트의 state를 초기화한다.

```js
function App() {
	const [isFancy, setIsFancy] = useState(false);
	return (
		<div>
			{isFancy ? (
				<div>
					<Count isFancy={true} />
				</div>
			) : (
				<section>
					<Count isFancy={false} />
				</section>
			)}
			<label>
				<input
					type="checkbox"
					checked={isFancy}
					onChange={(e) => setIsFancy(e.target.checked)}
				/>
				Use fancy styling
			</label>
		</div>
	)
}
```

위 예제의 경우, 동일한 위치에 `isFancy` 상태값에 따라 다른 컴포넌트 `div` 또는 `section`이 렌더링되고 있으므로 기존의 state와 하위 트리를 모두 초기화한다.

### 동일한 위치에서 state 초기화 하기

기본적으로 React는 동일한 위치에서 동일한 컴포넌트가 렌더링 되면 컴포넌트의 state를 보존한다.

하지만 때로는 컴포넌트의 state를 초기화하고 싶을 때도 있다.

```js
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Count person="Taylor" />
      ) : (
        <Count person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

위 예제에서는 `Taylor`의 score와 `Sarah`의 score가 각각 유지되었으면 좋겠어. 하지만, 현재는 `Count` 컴포넌트의 위치가 동일하기 때문에 state가 보존되고 있다.

우리가 원하는대로 score를 각각 유지하기 위해서는 다음 방법을 고려해볼 수 있다.

#### 1. 다른 위치에 컴포넌트 렌더링하기

```js
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Count person="Taylor" />
      }
      {!isPlayerA &&
        <Count person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

이렇게 하면, `Taylor`인 컴포넌트의 위치와 `Sarah`인 컴포넌트의 위치는 시작부터 다르기 때문에 state가 보존되지 않는다.

하지만, `Next player!` 버튼을 클릭할 때마다 각자의 컴포넌트가 위치에서 제거되기 때문에 `Taylor`와 `Sarah`의 기존 state가 초기화되고 있다.

#### 2. key를 사용한 state 초기화

```js
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Count key="Taylor" person="Taylor" />
      ) : (
        <Count key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

`key` prop을 사용하면, 개발자가 의도적으로 동일한 위치의 컴포넌트라도 서로 다른 `key`를 비교하여 state를 초기화하도록 구현할 수 있다.

즉, 서로 다른 key를 사용하면 동일한 위치에 동일한 컴포넌트라고 할지라도 React는 두 컴포넌트를 서로 다르다고 판단하고 state를 초기화하여 state를 공유하지 않는다.

## 참고

- [React 공식문서 - Reconciliation](https://ko.legacy.reactjs.org/docs/reconciliation.html)
- [React Beta 공식문서 - Preserving and Resetting State](https://react.dev/learn/preserving-and-resetting-state)