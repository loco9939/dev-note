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
