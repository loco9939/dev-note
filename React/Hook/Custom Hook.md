# Custom Hook

## 배경

로직을 재사용하기 위해서 Extract Function을 수행하여 Custom Hook으로 사용한다.

## 특징

- 캡슐화를 통해 접근 가능한 항목과 접근 불가능한 항목을 구분할 수 있다.
- 재사용성이 좋아진다.
- 코드가 깔끔해진다.

## 사용방법

```js
function useFetchProducts() {
  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    const fetchProducts = async () => {
      const url = 'http://localhost:3000/products';
      const response = await fetch(url);
      const data = await response.json();
      setProducts(data.products);
    };

    fetchProducts();
  }, []);

  return products;
}
```

- custom hook은 "use"로 시작하면서 camelCase로 작성한다.

## Hook 규칙

### 1. 함수의 최상단에서만 호출이 가능하다.

```js
// don't this ❌
function App() {

	useEffect(() => {
		const products = useFetchProducts();
	},[])
	
	return ...
}
```

```js
// do this ✅
function App() {
	const products = useFetchProducts();

	return ...
}
```

### 2. 함수형 컴포넌트 또는 custom hook에서만 호출 가능하다.

```js
// don't this ❌
function App() {
	const [playing, setPlaying] = useState(false);

	if (playing) {
		const products = useFetchProducts();
	}
}
```

```js
// do this ✅
function App() {
	const products = useFetchProducts();

	if (playing) {
		...
	}
	return ...
}
```

## usehooks-ts 라이브러리

우리가 자주 사용하는 hook을 제공해주는 라이브러리이다.

### useBoolean

참, 거짓값을 다룰 때 용이하다.

```ts
import { useBoolean } from "usehooks-ts";

export default function TimerControl() {
	const { value: playing, toggle } = useBoolean();

	return (
		<div>
			{playing ? <Timer /> : <p>Stop!</p>}
			<button type="button" onClick={toggle}>Toggle</button>
		</div>
	)
}
```

### useInterval

React에서 setInterval을 사용할 때, 주의할 부분을 고려한 커스텀 hook이다.

```ts
import { useInterval, useUnmount } from "usehooks-ts";

function Timer() {
	const savedTitle = document.title

	useInterval(() => {
		document.title = `Now: ${new Date().getTime()}`;
	},100)

	useUnmount(() => {
		document.title = savedTitle
	})

	// 기존 코드
	// useEffect(() => {
	// 	const savedTitle = document.title
  //   const id = setInterval(() => {
  //     document.title = `Now: ${new Date().getTime()}`;
  //   }, 100);
	// 	return () => {
	// 		document.title = savedTitle
	// 		clearInterval(id)
	// 	}
  // });

	return <p>Playing</p>
}
```

`setInterval`을 사용할 때, 외부의 브라우저 API를 사용하여 리액트 값을 변경하므로 사이드 이펙트이다.

React에선 사이드 이펙트를 관리하기 위해 `useEffect` 훅을 통해 관리해준다.

`useEffect` 훅에서 함수를 리턴하면 해당 컴포넌트가 unmount 될 때, 리턴된 함수가 실행된다.

즉, 위 로직에서 `useInterval`을 사용하면 `setInterval`을 사용하기 위해 일련의 과정을 간략하게 사용할 수 있다.

### useEventListener

DOM 요소에 이벤트 리스너를 등록하기 편리한 hook이다.

```ts
export default function TimerControl() {
	const { value: playing, toggle } = useBoolean();
	const { count, increment } = useCounter(0);

	const onScroll = () => {
		console.log("scroll")
	}

	useEventListener('scroll', onScroll)

	return (
		<div>
			{playing ? <Timer /> : <p>Stop!</p>}
			<button type="button" onClick={toggle}>Toggle</button>
			<p>{count}</p>
			<button type="button" onClick={increment}>Increase</button>
		</div>
	)
}
```

DOM 요소에 원하는 이벤트를 등록할 수 있어 유용하다.

### useDarkMode

사용자의 운영체제 환경이 darkMode인지를 알아내고 이를 수정하는데 용이한 hook이다.

```ts
import { useDarkMode } from 'usehooks-ts'

export default function Darkmode() {
  const { isDarkMode, toggle, enable, disable } = useDarkMode()

  return (
    <div>
      <p>Current theme: {isDarkMode ? 'dark' : 'light'}</p>
      <button onClick={toggle}>Toggle</button>
      <button onClick={enable}>Enable</button>
      <button onClick={disable}>Disable</button>
    </div>
  )
}
```

### useLocalStorage

localStorage API를 유용하게 사용할 수 있는 hook이다.

```ts
import { useLocalStorage } from 'usehooks-ts'

export default function Component() {
  const [value, setValue, removeValue] = useLocalStorage('test-key', 0)

  return (
    <div>
      <p>Count: {value}</p>
      <button
        onClick={() => {
          setValue((x: number) => x + 1)
        }}
      >
        Increment
      </button>
      <button
        onClick={() => {
          setValue((x: number) => x - 1)
        }}
      >
        Decrement
      </button>
      <button
        onClick={() => {
          removeValue()
        }}
      >
        Reset
      </button>
    </div>
  )
}
```

## 참조

[Hook의 규칙 바로가기](https://ko.legacy.reactjs.org/docs/hooks-rules.html)

[usehooks-ts 바로가기](https://usehooks-ts.com/)