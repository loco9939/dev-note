# useRef

## 정의

컴포넌트 생애주기에 걸쳐 유지되는 객체로, 렌더링에 필요하지 않은 값을 참조할 수 있는 React hook이다.

## 특징

- 컴포넌트가 없어질 때까지 동일한 객체가 유지된다.
- `ref` 객체의 `current` 프로퍼티값을 사용하여 참조한다.
- current 프로퍼티 값이 화면에 노출되어 있고 current 값을 변경하여도 즉각적으로 화면에 반영되지 않고 다른 state 값으로 리렌더링일 발생했을 때, 변경된다.

## 사용방법

### 1. id 값 관리하기

```js
export default function CheckboxField({
	label,
	inStockOnly,
	setInStockOnly }:CheckboxFieldProps) {
	const id = useRef<string>(`checkbox-${label.toLowerCase().replace(/ /g, '-')}`)

	const handleChange = () => {
		setInStockOnly(!inStockOnly)
	}
	return (
		<div>
			<input
				type="checkbox"
				id={id.current}
				checked={inStockOnly}
				onChange={handleChange}/>
			<label htmlFor={id.current}>{label}</label>
		</div>
	)
}
```
ref는 리렌더링이 발생하더라도 변하지 않기 때문에, id 값을 생성할 때 유용하다.

### 2. ref로 DOM 조작하기

```js
export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

ref로 DOM에 접근하여 focus를 설정해줄 수 있다.

### 3. ref로 컨텐츠 재생성 피하기

```js
function Video() {
  const playerRef = useRef(null);
  if (playerRef.current === null) {
    playerRef.current = new VideoPlayer();
  }
	// ...
```

비싼 객체를 렌더링때마다 생성하는 것은 낭비일 수 있기 때문에 위와 같이 null일 때, 즉 초기 렌더링 때에만 생성하도록 구현할 수 있다.

## 커스텀 컴포넌트의 ref 전달하기

커스텀 컴포넌트에 props로 ref를 전달하기 위해서는 커스텀 컴포넌트를 forwardRef로 감싸줘야 한다.

```js
import { forwardRef } from 'react';

const MyInput = forwardRef(({ value, onChange }, ref) => {
  return (
    <input
      value={value}
      onChange={onChange}
      ref={ref}
    />
  );
});

export default MyInput;
```

