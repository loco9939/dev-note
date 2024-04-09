# VDOM

Virtual DOM(VDOM)은 UI의 가상적인 표현을 메모리에 저장하고 ReactDOM과 같은 라이브러리에 의해 실제 DOM과 동기화하는 프로그래밍 개념이다.

이러한 접근방식이 **React의 선언적 API를 가능하게 한다.** React에게 원하는 UI 상태를 알려주면 DOM이 그 상태와 일치하도록 한다.

VDOM은 특정 기술이 아닌, 패턴에 가깝기에 **React에서 VDOM은 UI를 나타내는 객체이기 때문에 React Element와 연관된다.**

## VDOM 사용하는 이유

🤔 **VDOM을 사용하는 것이 빨라서 사용한다.** 이게 맞는걸까?

속도도 충분히 빠른 것은 맞지만, VDOM을 사용하는 주된 이유는 **maintainable(유지보수성)** 이 좋기 때문이다.

공식문서에서도 VDOM을 사용하면 속도가 빠르다는 이야기는 없고 React를 선언형으로 사용할 수 있게 만든다. 👍

`<div id='root'></div>` 태그만 존재하는 HTML 파일에서 DOM에 접근하여 아래와 같은 구조를 JavaScript와 React로 만들어보자.

```html
<div id="demo" class="container">
	<div class="box">
		<p>JavaScript is Best Computer Language</p>
	</div>
</div>
```

만약 자바스크립트로 DOM을 조작해보면 다음과 같이 해야한다.

```js
// JavaScript 사용
const demo = document.getElementById('demo');
const divElement = document.createElement('div')
const pElement = document.createElement('p')

pElement.textContent = 'JavaScript is Best Computer Language'

divElement.className = 'box'
divElement.appendChild(pElement)

demo?.classList.add('container')
demo?.appendChild(divElement)
```

프로그래밍을 하는 사람만이 이해할 수 있고 프로그래밍을 알더라도 복잡한 구조가 되거나 코드 스타일을 준수하지 않는다면 이해하기 어려워진다.

```js
// React.createElement 사용
React.createElement(
	'div',
	{className: 'container'},
	React.createElement(
		'div',
		{className: 'box'},
		React.createElement(
			'p',
			null,
			'JavaScript is Best Computer Language'
		)
	)
)
```

리액트로 구현하면 선언적 API를 사용하여 프로그래밍할 수 있으므로 유지보수가 용이하다.

만약 `p 태그`에 class를 추가하거나 텍스트를 바꾸려고 한다하면 선언형으로 작성된 부분을 추가 및 수정해주면 된다.

그리고 이렇게 선언형으로 사용하는 것보다 더 직관적이고 유지보수성을 높이는 방법이 바로 `JSX`를 사용하는 것이다.

```js
// JSX 문법 사용
function App() {
	return (
		<div id="demo" className="container">
			<div className="box">
				<p>JavaScript is Best Computer Language</p>
			</div>
		</div>
	)
}
```

이렇게만 작성해주면 리액트가 재조정 알고리즘을 통해서 알아서 실제 DOM에 바뀐 부분만 업데이트해주기 때문에 편리하게 사용할 수 있다.