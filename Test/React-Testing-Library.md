# React-Testing-Library

## 정의

React 컴포넌트 테스트 라이브러리이다.

## 사용이유

React 컴포넌트를 유지 및 관리하는데 용이한 테스트를 작성하려는 목적으로 탄생하였다.

테스트에는 컴포넌트의 구현 세부정보를 포함하지 않고 의도한대로 동작하는지 확인하는데 집중한다.

이를 통해, 리팩터링 시 컴포넌트를 수정하는데 두려움을 줄이고 유지보수 속도를 높이는데 기여하기 위해 사용한다.

## 특징

- 컴포넌트가 화면에 어떻게 노출되는지 테스트할 수 있다.
- 컴포넌트에 이벤트를 실행하여 이벤트가 잘 동작하는지 테스트할 수 있다.
- Jest 라이브러리와 연동이 좋아 다양한 테스트를 해볼 수 있다.
	> ex) 컴포넌트의 특정 함수가 호출되었는지...

## 사용방법

### 설치
```bash
npm install --save-dev @testing-library/react
```

### 일반적인 컴포넌트 테스트 예제

```ts
import { fireEvent, render, screen } from '@testing-library/react';

describe('Textfield', () => {
	// given
	const label = 'Name';
	const text = 'Tester';

	const setText = jest.fn();

	beforeEach(() => {
		// setText.mockClear();  특정 함수만 초기화
		jest.clearAllMocks(); // 모든 jest 함수 초기화
	})

	// 중복이 되는 코드는 함수로 빼낸다.
	function renderTextField() {
		render(
			<TextField
				label={label}
				placeholder="Input your name"
				text={text}
				setText={setText}
			>
		)
	}

	// it만 사용
	it('renders elements', () => {
		// when
		renderTextField()

		// then
		screen.getByLabelText(label); // label이 연결된 input을 찾는다.
		screen.getByPlaceholderText(/name/)
		screen.getByDisplayValue(text)
	})

	// context 사용
	context('when user enter name', () => {
		beforeEach(() => {
			renderTextField()
		})

		it('it calls setText ', () => {
			// given
			// renderTextField() // given이 it 내부에 있는 게 싫다면 beforeEach

			// when
			fireEvent.change(screen.getByLabelText(label), {
				target: {value: 'New Name'}
		})

		expect(setText).toHaveBeenCalledWith('New Name')
	})

	})
})
```

- `jest.fn` 사용하여 특정 함수를 호출할 수 있다.
- 현재 setText를 `it`, `context`에서 사용하고 있는데, 매번 테스트 때마다 초기화 해줘야지만 각 테스트에 독립적으로 적용이 가능하다.
	- 매번 테스트 때마다 초기화해주는 방법은 `beforeEach`를 사용하면 된다.
	- `context`를 사용하는 부분에서 `fireEvent`를 확인하기 위해서 우선 `TextField`가 렌더링되어야하는 조건이 주어져야한다. 이를 `it` 내부에서 직접 렌더링시켜줄 수 도 있지만, `given` 조건이 `it` 내부에 있는 것이 싫다면 `beforeEach`를 사용하여 초기화 해줄 수 있다.
- `setText`가 호출되었는지 확인하는 방법은 `toHaveBeenCalled`, `toHaveBeenCalledWith`는 해당 인자로 호출되었는지 확인할 수 있다.

### useFetch 테스트 예제

```ts
// App.test.tsx
import { render, screen } from '@testing-library/react';

jest.mock('./hooks/useFetchProducts', () => () => fixture.products)

test('App', () => {
	render(<App />);

	screen.getByText("Apple")
})
```

- App 컴포넌트에서 호출하는 useFetchProducts 경로와 App.test.tsx 기준으로 경로를 맞춰줘야한다.
	- 만약, App.test.tsx과 App.tsx가 동일한 위치기 아니라면 경로를 수정 해야한다.

#### mock 데이터 관리하기

```ts
// fixture/products.ts
const products = [
	{category:'Fruits',price:'$1',stocked:true,name:'Apple'}
]

export default products
```

- fixture 폴더를 만들어서 mock useFetchProducts의 결과값을 관리할 수 있다.

#### mock 함수 관리하기

```ts
// hooks/__mocks__/useFetchProducts.ts
import fixture from "../../../fixture";

const useFetchProducts = jest.fn(() => fixture.products);

export default useFetchProducts
```

- mock 함수들만 따로 폴더에서 관리해줄 수 있다.
- App.test.tsx에서는 아래와 같이 사용할 수 있다.

```ts
// App.test.tsx
import { render, screen } from '@testing-library/react';

jest.mock('./hooks/useFetchProducts')

test('App', () => {
	render(<App />);

	screen.getByText("Apple")
})
```

- 만약 fetch 테스트가 다양해진다면, 위와 같은 로직이 방대해지므로 이를 관리하기 위해 `MSW(Mock Service Worker)`를 사용하는데 이는 다음시간에 알아보자.

## 참조

[React-Testing-Library](https://github.com/testing-library/react-testing-library)