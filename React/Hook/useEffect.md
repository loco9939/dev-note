# useEffect

## 정의

리액트가 아닌 외부 시스템(서버 연결, 통계 로그 전달)과 컴포넌트를 동기화 하기 위해 사용하는 hook으로, 컴포넌트가 렌더링 이후 해야할 일을 정한다.

## 특징

- 기본적으로 렌더링때마다 실행된다.
- 의존성 배열에 Effect를 언제 실행할 지 지정할 수 있다.
- 함수를 return 함으로써 종료(clean up)할 수 있다.

## 사용방법

### 1. 처음에 한번만 실행하기

```js
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
```

의존성 배열에 빈 배열을 넘겨주면 렌더링 이후 1회만 실행한다.

> 단, React.StrictMode를 사용할 경우, 개발모드에서 에러가 발생하는지 확인하기 위해 2회 실행한다.

### 2. 특정 상태가 변경되었을 때만 실행하기

```js
const [products, setProducts] = useState<Product[]>([]);
const [playing, setPlaying] = useState(false);
const [count, setCount] = useState(0);

useEffect(() => {
  const fetchProducts = async () => {
    const url = 'http://localhost:3000/products';
    const response = await fetch(url);
    const data = await response.json();
    setProducts(data.products);
  };

  fetchProducts();
}, [playing]);
```

- `playing` 상태값이 변경되었을 때만 Effect를 실행한다.
- `count` 상태값이 변경되면 해당 컴포넌트가 리렌더링 발생하지만 Effect는 실행되지 않는다.

### 3. 컴포넌트 리렌더링 발생할 때마다 실행하기

```js
const [products, setProducts] = useState<Product[]>([]);

useEffect(() => {
  const fetchProducts = async () => {
    const url = 'http://localhost:3000/products';
    const response = await fetch(url);
    const data = await response.json();
    setProducts(data.products);
  };

  fetchProducts();
});
```

- 의존성 배열을 전달하지 않으면 해당 컴포넌트가 리렌더링 발생할 때마다 Effect를 실행한다.

### 4. 컴포넌트 unmount시 Effect 종료하기(clean up)

```js
useEffect(() => {
  const savedTitle = document.title;

  const id = setInterval(() => {
    document.title = `Now: ${new Date().getTime()}`;
  }, 100);

  return () => {
    document.title = savedTitle;
    clearInterval(id);
  };
});
```

- 컴포넌트 렌더링 이후 Effect가 실행되어 timer가 작동한다.
- 이후 컴포넌트가 unmount되었을 때, return한 함수(clean up)가 실행되어 Effect를 종료한다.

### 의존성 배열 이용하여 fetching Data 시 주의사항

예를 들어, userId라는 상태값에 따라 데이터를 fetch 요청하는 로직이 Effect에 있다.

처음 userId가 'alice' 였다가 바로 'jane'으로 바꿨을 때, setTodos는 'alice'의 데이터가 아닌 'jane'의 데이터로 설정되어야 한다.

비동기적으로 발생한 로직에서 어떤 데이터가 먼저 순서로 적용될지 알 수 없으므로 이 때, clean up 함수를 사용하여 userId가 바꼈을 때, 이전에 fetching 요청을 무시할 수 있도록 작성할 수 있다.

```ts
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```

> 이 때, fetch 함수의 위치가 useEffect 안에 넣는 것이 좋다. 그 이유는 fetch 함수 내부에서 또 다른 상태값을 참조하게 될 경우도 있는데, 그 때 Effect의 의존성 배열에 해당 상태값을 넣도록 유도함으로써 예상치 못한 동작을 하지 않는 안전한 코드를 작성할 수 있기 때문이다.

## 참조

[useEffect - React Beta](https://react.dev/reference/react/useEffect)

[useEffect 완벽 가이드](https://overreacted.io/a-complete-guide-to-useeffect/)