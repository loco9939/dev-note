# JSX

JSX는 XML 문법으로 작성한 코드에 JavaScript를 포함하여 사용하기 위한 확장 문법이다.

> 오랜 기간 웹은 구조를 위한 HTML, 디자인을 위한 CSS, 로직을 위한 JavaScript를 각각의 별개의 파일로 관리하고 있었다. 하지만, 웹에 UI를 결정하는 로직(상호작용)이 늘어나면서 JavaScript가 HTML에 큰 관여를 하고 있다. 리액트에서 마크업과 렌더링 로직을 `컴포넌트`에 함께 위치시키는 이유가 바로 이 때문이다.

마크업과 JavaScript를 한곳에 위치시켰을 때의 장점은 다음과 같다.

1. 마크업과 로직에 대한 편집이 서로 동기화되어 유지되고 있다는 것을 보장한다.

2. 버튼 마크업과 sidebar 마크업처럼 서로 관련이 없는 상세정보들이 서로 독립적으로 존재하고 이로써 각자의 변경이 더 안전하다.

## 특징

JSX는 HTML처럼 보이지만, 동적인 정보를 나타낼 수 있고 더 엄격한 문법이다.

> JSX와 React는 서로 다른 것이다. JSX는 확장 문법이고 React는 JavaScript 라이브러리이다.

## HTML을 JSX로 변환하기

아래에 나타는 HTML 문법을 React 컴포넌트에 넣어보자.

```html
<h1>Hedy Lamarr's Todos</h1>
<img
  src="https://i.imgur.com/yXOvdOSs.jpg"
  alt="Hedy Lamarr"
  class="photo"
>
<ul>
    <li>Invent new traffic lights
    <li>Rehearse a movie scene
    <li>Improve the spectrum technology
</ul>
```

하지만, 위 문법 그대로 컴포넌트 return 문에 넣으면 에러를 발생한다. 왜냐하면 JSX문법는 HTML보다 더 엄격하기 때문이다.

이를 해결하기 위해서 지켜야할 규칙에 대해 알아보자.

### JSX 규칙

#### 1. 단일 요소를 반환하라

```html
<div>
	<h1>Hedy Lamarr's Todos</h1>
	<img
		src="https://i.imgur.com/yXOvdOSs.jpg"
		alt="Hedy Lamarr"
		class="photo"
	>
	<ul>
			<li>Invent new traffic lights
			<li>Rehearse a movie scene
			<li>Improve the spectrum technology
	</ul>
</div>
```

- 단일 요소로 반환하기 위해 `div` 태그 혹은 `<>` 빈 태그로 감싸서 반환한다. 빈 태그는 `Fragment`라고도 불린다.

#### 2. 모든 태그를 닫아라

HTML 문법의 경우 닫는 태그를 생략해도 되지만, JSX는 닫는 태그가 필수다.

```js
<>
  <img
    src="https://i.imgur.com/yXOvdOSs.jpg"
    alt="Hedy Lamarr"
    class="photo"
   />
  <ul>
    <li>Invent new traffic lights 🚨 닫는 태그 실종
    <li>Rehearse a movie scene</li>
    <li>Improve the spectrum technology</li>
  </ul>
</>
```

#### 3. camelCase로 작성하라

JSX는 JavaScript로 변하고 JSX로 작성된 attributes는 JavaScript 객체의 key 값으로 변한다.

JavaScript에서 이런 변수명을 작성할 때 제한이 있다. 예를 들면, `-`를 포함할 수 없고 `class`같은 이름을 사용할 수 없다.

> 단, `aria-`, `data-` 같은 속성에서는 `-`를 사용할 수 있다.

## JSX 없이 React Element 만들기

React에서 JSX를 꼭 사용하지 않아도 된다. JSX를 사용하고 컴파일 하기 싫다면 `React.createElement`로 React Element를 만들 수 있다.

```js
// JSX 사용
function App(){
	return <div className="app">Hello World!</div>
}

// createElement 사용
function App() {
	return React.createElement('div',{ className:'app'}, 'Hello World!')
}
```

위 두가지 문법 모두 아래와 같은 객체로 변환된다.

```js
{
	type:'div',
	props: {
		className:'app'
	},
	key: null,
	ref: null
}
```

이런 객체를 만드는 것이 컴포넌트를 렌더링하거나 DOM 요소를 생성하는 것이 아니라는 것을 알아야 한다.

> React Element는 React가 나중에 Rendering 하기 위한 설명에 가깝다. element를 생성하는 것은 매우 저렴(성능에 부하가 낮음)하므로, 이를 굳이 최적화 하거나 피할 필요는 없다.

## 참조

[React Beta 공식문서 - JSX로 마크업 작성하기](https://react.dev/learn/writing-markup-with-jsx)

[React Beta 공식문서 - JSX 없이 React Element 생성하기](https://react.dev/reference/react/createElement#usage)