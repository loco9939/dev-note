# GlobalStyle & Theme

## 정의

기본적으로 브라우저마다 다르게 적용된 스타일을 전부 초기화한다.

styled-components 라이브러리에서 제공하는 ResetCSS를 사용하고 Global styles로 전역 스타일을 지정한다.

추가로, theme을 통해 디자인 시스템에 기반을 적용해보자.

## 사용방법

### 설치

```bash
npm i styled-reset
```

- ResetCSS를 라이브러리로 제공하므로 설치한다.

### Global Style 및 ResetCSS 적용하기

```ts
// App.tsx
import { Reset } from 'styled-reset';
import Greeting from '../components/Greeting';
import Switch from '../components/Switch';
import GlobalStyle from '../styles/GlobalStyle';

function App() {
  return (
    <div>
      <Reset />
      <GlobalStyle />
      <Greeting />
      <Switch />
    </div>
  );
}

export default App;
```

- 먼저 `Reset`을 시켜준 뒤 `GlobalStyle`을 적용한다.

```ts
// /src/styles/GlobalStyle.ts
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  html {
    box-sizing: border-box;
  }

  *,
  *::before,
  *::after {
    box-sizing: inherit;
  }

  html {
    font-size: 62.5%;
  }

  body {
    font-size: 1.6rem;
  }

  :lang(ko) {
    h1, h2, h3 {
      word-break: keep-all;
    }
  }
`;

export default GlobalStyle;
```

- `box-sizing: border-box;`는 박스모델의 너비와 높이를 패딩과 보더까지 포함하여 계산
- `*, *::before, *::after`에 inherit을 준 이유는 혹시나 다른 곳에서 `box-sizing:content-box`를 사용할 때 부모의 것을 상속받겠다고 선언
- `html {font-size: 62.5%}`는 rem은 루트 글꼴 크기를 의미하는데, 이를 계산하기 쉬운 10px로 맞춰주기 위해 사용
- `word-break: keep-all`은 한중일(CJK) 텍스트에서는 줄을 바꿀 때 단어를 끊지 않습니다.

### Theme

이 모든 것이 Theme을 활용할 때 빛을 발한다.

디자인 시스템의 근간을 마련하는데 활용된다.

#### 기본 theme 정의 및 타입 정의

```ts
// /src/styles/defaultTheme.ts
const defaultTheme = {
	colors: {
		background: '#FFF',
		text: '#000',
		primary: '#F00',
		secondary: '#00F'
	}
}

export default defaultTheme;
```

- App 컴포넌트에서 사용해보자

```ts
// App.tsx
import { Reset } from 'styled-reset';
import Greeting from '../components/Greeting';
import Switch from '../components/Switch';
import GlobalStyle from '../styles/GlobalStyle';
import defaultTheme from '../styles/defaultTheme';

function App() {
	const theme = defaultTheme;
  return (
		<ThemeProvider theme={theme}>
			<div>
				<Reset />
				<GlobalStyle />
				<Greeting />
				<Switch />
			</div>
		</ThemeProvider>
  );
}

export default App;
```

- `styled-components`에서 `ThemeProvider`를 제공한다.
- `ThemeProvider`를 사용하면 하위 컴포넌트에서 `props.theme`을 사용할 수 있다.

```ts
// /src/styles/GlobalStyle.ts
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  html {
    box-sizing: border-box;
  }

  *,
  *::before,
  *::after {
    box-sizing: inherit;
  }

  html {
    font-size: 62.5%;
  }

  body {
    font-size: 1.6rem;
		background: ${(props) => props.theme.colors.background};
		color:${(props) => props.theme.colors.text}
  }

	button,
  input,
  select,
  textarea {
    background: ${(props) => props.theme.colors.background};
    color: ${(props) => props.theme.colors.text};
  }

  :lang(ko) {
    h1, h2, h3 {
      word-break: keep-all;
    }
  }
`;

export default GlobalStyle;
```

- 하지만 이렇게만 사용하면 타입 에러가 발생하므로 styled.d.ts 파일로 타입을 정의해준다.

```ts
// /src/styles/defaultTheme.ts
const defaultTheme = {
  colors:{
    background:'#FFF',
    text:'#000',
    primary:'#F00',
    secondary:'#00F'
  }
}

export default defaultTheme
```

- `defaultTheme`을 정의한다.
- 디자인 시스템에 근간하여 배경색에는 검정색을 사용할거야라고 하는 것보단 용어를 통일하기 위해서 위와 같이 사용한다.

```ts
// /src/types/theme.ts
import defaultTheme from "../styles/defaultTheme";

type Theme = typeof defaultTheme

export default Theme
```

- `theme`에 대한 타입을 정의한다.

```ts
// /src/styles/darkTheme.ts
import Theme from "../types/theme"

const darkTheme:Theme = {
  colors:{
    background:'#000',
    text:'#FFF',
    primary:'#F00',
    secondary:'#00F'
  }
}

export default darkTheme
```

- `darkModeTheme`을 정의한다.

```ts
// /src/styles/styled.d.ts
import 'styled-components';
import Theme from '../types/theme';

declare module 'styled-components' {
	export interface DefaultTheme extends Theme {
	}
}
```

- `Theme` 타입을 지정해두었으므로 `DefaultTheme`에서 타입을 굳이 작성해주지 않아도 된다.

#### DarkMode 적용하기

```ts
// App.tsx
import { ThemeProvider } from 'styled-components';
import { Reset } from 'styled-reset';
import { useDarkMode } from 'usehooks-ts';
import Button from '../components/Button';
import Greeting from '../components/Greeting';
import Switch from '../components/Switch';
import GlobalStyle from '../styles/GlobalStyle';
import darkTheme from '../styles/darkTheme';
import defaultTheme from '../styles/defaultTheme';

function App() {
  const {isDarkMode,toggle } = useDarkMode()
  const theme = isDarkMode ? darkTheme : defaultTheme;
  return (
    <ThemeProvider theme={theme}>
			<div>
				<Reset />
				<GlobalStyle />
				<Button onClick={toggle}>Toggle DarkMode</Button>
				<Greeting />
				<Switch />
			</div>
    </ThemeProvider>
  );
}

export default App;
```

## 참조

[createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle)

[The 62.5% Font Size Trick](https://www.aleksandrhovhannisyan.com/blog/62-5-percent-font-size-trick/)

[word-break - MDN](https://developer.mozilla.org/ko/docs/Web/CSS/word-break)