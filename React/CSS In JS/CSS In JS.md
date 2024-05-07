# CSS In JS

## 정의

말 그대로 CSS를 JS파일 안에 작성하는 스타일링 기법을 말한다.

## 역사 및 배경(사용이유)

CSS를 사용하는데 많은 문제점들을 해결하기 위해 등장했다. 대표적인 문제로는 아래 7가지가 있다.

1. 전역변수

- CSS는 전역변수를 사용하므로 이는 개발의 근본적인 문제가 된다.

2. 의존성

- 수많은 CSS 파일은 하나의 파일로 번들링할 수 있어야 하며, 다시 쪼갤 수 도 있어야한다. 이를 다루기 위해선 의존성이 필요한다.

> 이는 `cx` 라이브러리를 통해 해결할 수 있다.

3. 죽은 코드 제거

- 사용되지 않는 CSS를 제거하는 방법도 까다롭다.

> 하지만 앞서 cx를 사용하는 것이 변수명을 만드는 유일한 방법이기 때문에, 코드베이스에서 검색되지 않는 CSS 코드는 제거하면 된다.

4. 최소화(Minification)

- 클래스이름을 최소화하는 방법 또한, cx 도입 이전까지는 개발자들이 매우 힘들어 하는 부분이었다.

5. 상수 공유(Sharing Constants)

- CSS와 JS가 서로 상수를 공유하는 것은 이상적이지 않다. 그동안은 CSS와 JS파일에 각각 주석을 달아서 서로 동기화 하도록 했지만, 대부분의 개발자들은 이를 무시한다.

> 이를 해결하기 위해 PHP에서 사용하는 방법인 cssVar 함수를 사용했다.

```css
/* button.css */
.button/container {
	padding:var(button-padding);
}
```

```js
/* button.js */
var buttonPadding = cssVar('button-padding')
```

```php
/* CSSVar.php */
$CSSVar = array(
	'button-padding' => 5
);
```

지금까지 CSS를 사용하면서 맞닥뜨릴 수 잇는 문제를 어느정도 해결할 수 있지만 이는 CSS를 사용하기 위해 배보다 배꼽이 더 큰 도구를 사용하는 꼴이 되어버렸다.

또한, 아직 해결하지 못한 문제가 2개나 더 있다...😅

6. 비결정론적 결의(Non-deteministic Resolution)

- CSS는 동일한 값을 적용하면 마지막에 사용한 값을 적용하는 특징이 있다. 하지만 이는 번들링 시 악몽이 될 수 있다.

> 하나의 페이지로 갔다가 다른 페이지로 이동하여 동적으로 CSS를 로딩할 때 버그가 발생한다.

7. 고립 파괴(Breaking Isolation)

- CSS는 오버라이딩이 가능한데, 하지만 이는 lint 코드리뷰에서 주로 잡히지 않는다.

즉, 위 7가지 문제 중 2가지 문제가 해결되지 않았기 때문에, CSS In JS가 필요하다.

## 특징

```js
var styles = {
	container: {
		backgroundColor:'black',
		border: '1px solid #fff',
	},
	depressed: {
		backgroundColor:'blue',
		borderColor:'yellow'
	}
}

<div style={styles.conatainer}>
```

- inline style을 사용한다.
	- inline style이 그렇게 나쁘지는 않다.
	- 그 이유 첫째. 파일 안에서 참조할 규칙을 제공하고 이를 사용하여 inline style을 적용하고 있다.
	- 두번째, 스타일이 사실 클래스보다 더 낫다. 왜냐하면 우리는 스타일 요소에 적용하길 원하기 때문에...
	- 마지막으로, 이건 style을 직접적으로 적용하는 것이 아니라 React Virtual DOM을 사용하는 것이므로 다르다.

이렇게만 하더라도 툴 사용 없이 앞서 5가지 문제를 해결할 수 있다.

남은 2가지 문제를 해결하기 위해 몇가지 할일이 더 남아있다.

```js
/* button.js */
propTypes: {
	isDepressed: React.PropTypes.bool
}

<div style={m(
	styles.container,
	this.props.isDepressed && styles.depressed
)} />
```

- propsTypes를 통해 isDepressed 속성이 있을 경우에만 style을 적용하여 조건부로 렌더링할 수 있다.

```js
function m() {
	var res = {};
	for (var i = 0; i < arguments.length; ++i) {
		if (arguments[i]) {
			Object.assign(res, arguments[i])
		}
	}
	return res;
}
```

- `m` 함수를 통해 우리는 결과적으로 style 속성을 가진 JS 객체를 반환하는 JS 함수를 사용할 수 있다.

이를 통해 6,7번 문제도 해결할 수 있다.

### 장점

#### 1. 범위가 지정된(scoped) 스타일

CSS를 효율적으로 구조화 하는 것은 매우 어렵다. 이를 해결하기 위한 궁극적인 방법으로 BEM이다. BEM은 `.Block_element--modified` 패턴으로 제한함으로써 최적화를 위한 이름만들기 규칙이다.

그런데 꼭 왜 이런 규칙하나에만 의존하여 스타일링을 작업해야하는가?

대부분의 CSS In Js 라이브러리들은 BEM의 사고방식을 따르면서 각각의 UI에 스타일을 지정하려고 한다. 내부적으로는 서로 다른 방식을 구현하고 있다.

**여기서 중요한 점은 인라인(inline) 스타일이 아닌 CSS 직접 생산한다는 점이다.**

초기에 CSS In JS 라이브러리들은 각각의 요소에 스타일을 직접 붙이는 방식을 사용했다. 하지만 스타일 속성이 CSS가 할 수 있는 모든 것을 할 수 없다는 것이 문제이다.

ex) pseudo-elements, pseudo-selector는 inline-style에서 사용할 수 없다.

그래서 아예 스타일을 강제하여 컴포넌트에 사용할 수 있도록 `styled-components` 같은 라이브러리가 등장하였다.

#### 2. 필수적인(critical) CSS

페이지를 렌더링할 때 필수적인 스타일을 document head에 추가하여 로딩시간을 향상하는 방법은 비교적 최근 모범사례이다.

CSS In JS 라이브러리에서는 기본적으로 필수적인 CSS가 우선해서 작동하도록 요구된다.

즉, 필수적인 CSS를 렌더링하는 모범 사례는 이제 선택 가능한 옵션에서 시스템에 포함된 요소가 되었다.

#### 3. 더 똑똑한 최적화

CSS 최적화 목표는 CSS 번들을 가능한 낭비없게 만들어서 클래스의 재활용성을 극대화하고 클래스를 인라인 스타일처럼 효과적으로 사용하는 것이다.

그동안 이는 순수 수작업으로 이루어졌지만 이제는 라이브러리에 의해 완전히 자동화 되었다.

#### 4. 패키지 관리

CSS 패키지 관리자인 Bower에 비해 npm이 기하급수적으로 늘수 있었던 이유는 npm은 의존성을 알아서 해결해주지만, Bower는 사용자가 직접 의존성을 해결해야만했다.

Bower도 의존성 관리를 위한 적절한 모듈 포맷이 존재해야한다. 즉, CSS의 경우 어떻게 작고 재사용성이 있는 패키지를 조합하여 거대한 스타일 컬렉션을 구성하는 자바스크립트와 같은 수준의 오픈소스를 기대할 수 있을까?

CSS를 다른 언어에 내장하고 자바스크립트 모듈을 최대한 활용하는 방법을 통해 가능할 것이다.

#### 5. 브라우저가 아닌 환경의 스타일링

지금까지 살펴본 내용은 표준 CSS가 없다면 불가능하다. 

React native를 예로 들어보면, 자체적으로 StyleSheet API를 가지고 있어서 다음과 같이 스타일링을 한다.

```ts
var styles = StyleSheet.create({
  container: {
    borderRadius: 4,
    borderWidth: 0.5,
    borderColor: '#d6d7da',
  },
  title: {
    fontSize: 19,
    fontWeight: 'bold',
  },
  activeTitle: {
    color: 'red',
  }
})
```

브라우저가 아님에도 불구하고 React native는 flexbox를 네이티브 환경에서 사용할 수 있는 구현체를 탑재하고 있다.

CSS In JS 라이브러리도 이와 같은 방법으로 표준 CSS가 없이 CSS를 사용할 수 있다.

컴포넌트 기반 구조에서는 특정 컴포넌트와 관련된 요소들을 최대한 같은 위치에 두는 일에 우선순위를 둔다. 또한, 이는 컴포넌트 관점에서 생각할 수 있도록 도와준다.

그리고 이러한 점은 디자이너와 개발자가 협력할 수 있는 방식을 완전히 바꿔버릴 수 있는 잠재력이 있다.

앞으로 디자인에서 반복적으로 사용되는 컴포넌트를 언급할 때 디자이너와 개발자가 각각 어느 도구를 사용하든 같은 컴포넌트를 참조할 수 있게 된다.

### 단점

#### 성능 이슈

JS 크기를 줄이는 것이 성능에 유리하므로 CSS를 별도로 분리하는 것이 성능적인 면에서는 더 좋다.

그렇다고 JS 파일에서 CSS 코드를 다 없애야 하는 것은 아니고 styled-components를 [르내리아(Linaria)](https://linaria.dev/)로 교체하고 빌드할 때 CSS를 뽑아내면 된다.

[vanilla-extract](https://github.com/vanilla-extract-css/vanilla-extract)나 [페이스북 스타일링 라이브러리](https://www.youtube.com/watch?v=9JZHodNR184)도 살펴보면 좋다.

## 참조

[React:CSS in JS - NationJS](https://blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.html)

[번역 - 통합스타일링 언어](https://megaptera.notion.site/1bda981f17224c058b612598d4323c63)

[번역 - CSS In JS에 관해 알아야 할 모든 것](https://megaptera.notion.site/CSS-in-JS-d5904960ac564b0c8cdf5aa3bc2df5da)

[CSS In JS 성능](https://megaptera.notion.site/CSS-in-JS-0afa302408f74f7a9712924ead6318be)

[번연 - 우리가 CSS In JS와 헤어지는 이유](https://megaptera.notion.site/CSS-in-JS-4022ede93d06407ea6474a40be4cd906)