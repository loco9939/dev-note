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