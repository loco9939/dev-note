# styled-components

## 정의

대표적인 CSS In JS 라이브러리

## 역사 및 배경(사용이유)

앞서 [CSS In JS를 사용해야 하는 이유](../CSS%20In%20JS/CSS%20In%20JS.md)에 대한 글을 보고 오자.

컴포넌트와 동일한 위치에 CSS를 추가하여 컴포넌트적으로 생각할 수 있게 도와주며, 제한된 스코프를 가져서 전역 변수 CSS보다 유지보수가 편리하고 JavaScript 변수를 사용할 수 있다는 장점이 있다.

## 특징

- 

## 사용방법

### 설치

```bash
npm i styled-components
npm i -D @types/styled-components @swc/plugin-styled-components
```

> 추가로 VS Extension `vscode-styled-components-extension`도 같이 사용하자. 그래야 문자열로 인식하지 않고 CSS로 인식하여 편하다.

```json
// .swcrc
{
  "jsc": {
    "experimental": {
      "plugins": [
        [
          "@swc/plugin-styled-components",
          {
            "displayName": true,
            "ssr": true
          }
        ]
      ]
    }
  }
}
```

- Babel Plugin을 SWC에서 사용할 수 있도록 swcrc 파일도 작성한다.

> SSR 지원을 위한 공식 권장사항이다.

### 기본적인 사용법

```ts
import styled from 'styled-components';

const Paragraph = styled.p`
  color: #00F;
`;

export default function Greeting() {
  return (
    <Paragraph>
      Hello, world!
    </Paragraph>
  );
}
```

### 기존 컴포넌트에 스타일 입히기

```ts
import React from 'react';
import styled from 'styled-components';

const Paragraph = styled.p`
  color: #00F;

	// Nesting 지원
  strong {
    color: red;
  }
`;

const BigParagraph = styled(Paragraph)`
  font-size: 2rem;
`;

function HelloWorld({ className }: React.HTMLAttributes<HTMLElement>) {
  return (
    <BigParagraph className={className}>
      Hello, world
      <strong>!</strong>
    </BigParagraph>
  );
}

const SmallHelloWorld = styled(HelloWorld)`
  font-size: .1em;
`;

function Greeting() {
  return (
    <SmallHelloWorld />
  );
}
```

- SCSS에서만 사용가능했던 Nesting도 지원한다.

### props 활용

활성화 여부 및 특정 스타일 적용하고 싶을 때 유용함

```ts
import styled, { css } from "styled-components"
import { useBoolean } from "usehooks-ts"

type ButtonProps = {
  active?:boolean
}

const Button = styled.button<ButtonProps>`
  background:#fff;
  color:#000;
  border:1px solid ${props => props.active ? '#F00' :'#000'};

  ${props => props.active && css`
    background: #00F;
    color:#fff;
  `}
`

const PrimaryButton = styled(Button)`
  background: #eee;

  // 해당 부분은 상속되지 않음
  ${props => props.active && css` // CSS 인것을 알고 쉽게 사용하기 위해 css 추가(안써도됨)
    background: #F0F;
  `}
`

export default function Switch() {
  const {value:active, toggle} = useBoolean()

  return (
    <PrimaryButton onClick={toggle} active={active}>On/Off</PrimaryButton>
  )
}
```

### attr 활용

기본 속성을 추가할 수 있다. 불필요하게 반복되는 속성을 처리할 때 유용하다. 적극 활용하자. 👍

```ts
import styled, { css } from "styled-components"
import { useBoolean } from "usehooks-ts"

type ButtonProps = {
	type?:'button'|'submit'|'reset';
	active?:boolean;
}

const Button = styled.button.attr<ButtonProps>((props) => {
	return {type:props.type ?? 'button'}
})<ButtonProps>`
	background:#fff;
	color:#000;
	border: 1px solid ${props => props.active ? '#F00' : '#000'};

	${props => props.active && css`
		background: #00F;
		color:#fff;
	`}
`

const PrimaryButton = styled(Button)`
  background: #eee;

  // 해당 부분은 상속되지 않는다.
  ${props => props.active && css` // CSS 인것을 알고 쉽게 사용하기 위해 css 추가(안써도됨)
    background: #F0F;
  `}
`

export default function Switch() {
  const {value:active, toggle} = useBoolean()

  return (
    <PrimaryButton onClick={toggle} active={active}>On/Off</PrimaryButton>
  )
}
```

- `attr` 바로 뒤에 type을 선언하여 내부에서 `props`를 추론하는데 사용한다.
- `attr` 뒤에 2번째 type 선언은 `styled-components` 내부에서 `props`를 추론하는데 사용된다.

## 참조
