# React Router

## 정의

일반적인 웹 사이트는 url에 따라 요청한 데이터를 응답받아 화면을 보여준다. url 경로에 따라 웹 페이지를 변경하는 것을 Routing(라우팅)이라고 한다.

React에서는 CSR을 제공하기 때문에 이를 편리하게 하기 위해 React-Router라는 라이브러리를 사용한다.

## 역사 및 배경(사용이유)

전통적인 웹 사이트는 웹 서버로부터 문서(CSS, JavaScript, 렌더링된 HTML 등)를 요청하여 브라우저에 보여준다. 유저가 link를 클릭하게되면 새로운 페이지도 이러한 과정을 통해서 전달받는다.

CSR(Client-Side-Rendering)은 애플리케이션이 서버에 대한 어떠한 요청 없이도 URL을 수정하는 것이 가능하다. 대신 애플리케이션은 즉각적으로 새로운 UI, Page를 업데이트할 새로운 데이터를 요청한다.

이러한 것들이 사용자 경험을 향상시킨다. 왜냐하면 브라우저는 완전한 페이지를 다시 요청할 필요가 없기 때문이다.

즉, React Router는 CSR을 사용하여 사용자 경험을 향상 시키기 위해 사용한다.

## 특징

- Router 객체를 사용하여 관심사 분리를 통해 Router를 따로 관리하기 용이하다.
- MemoryRouter를 사용하여 테스트 환경에서 라우팅 테스트를 할 수 있다.
- Link를 사용하여 a 태그와 동일하게 렌더링되지만 페이지 전체를 다시 요청하지는 않게 구현할 수 있다. (reload Document 어트리뷰트로 속성으로 a 태그와 동일한 기능을 하도록 구현할 수도 있다.)
- 중첩 라우팅이 가능하다.
- NavLink를 사용하여 isActive, isPending 상태에 대한 스타일링 처리를 할 수 있다.
- Navigation hook을 사용하여 라우팅을 쉽게 처리할 수 있다.

## 사용방법

### 설치

```bash
npm install react-router-dom
```

### 라우터 객체로 라우트(경로) 관리하기

```ts
// routes.tsx
import AboutPage from './components/AboutPage';
import HomePage from './components/HomePage';
import LogoutPage from './components/LogoutPage';
import { Outlet } from 'react-router-dom';
import Footer from './components/Footer';
import Header from './components/Header';

function Layout() {
	return (
		<div>
			<Header />
			<main>
				<Outlet />
			</main>
			<Footer />
		</div>
	);
}

const routes = [
	{
		element: <Layout />,
		children: [{
			path: '/', element: <HomePage />,
		},
		{
			path: '/about', element: <AboutPage />,
		},
		{
			path: '/logout', element: <LogoutPage />,
		}],
	},
];

export default routes;
```

- routes라는 파일을 따로 만들어서 이곳에서는 라우트만 관리해준다.
- Layout 컴포넌트의 중첩라우팅을 설정하여 공통으로 사용되는 Header, Footer에 대한 레이아웃을 설정할 수 있다.
- 라우트 관련 파일에서 라우트만 따로 모아두었으니 라우트 관련 테스트 작성도 용이하다.

```ts
// routes.test.tsx
import { render, screen } from '@testing-library/react';
import { RouterProvider, createMemoryRouter } from 'react-router-dom';
import routes from './routes';

const context = describe;
describe('Routes', () => {
	function renderRouter(path: string) {
		const router = createMemoryRouter(routes, {initialEntries: [path]});
		render(<RouterProvider router={router} />);
	}

	context('when the current path is "/"', () => {
		it('renders the HomePage', () => {
			renderRouter('/');

			screen.getByText('HomePage');
		});
	});

	context('when the current path is "/about"', () => {
		it('renders the AboutPage', () => {
			renderRouter('/about');

			screen.getByText('AboutPage');
		});
	});

	context('when the current path is "/logout"', () => {
		it('redirect to the homePage', () => {
			renderRouter('/logout');

			screen.getByText('HomePage');
		});
	});
});
```

- 중복되는 `RouterProvider`을 렌더링하는 로직을 함수로 빼내어 준다.

#### 라우팅 테스트 에러 시 해결 ✅

`ReferenceError: Request is not defined` 이런 에러가 발생하면, 'whatwg-fetch' 폴리필을 설치해줘서 해결할 수 있다.

```ts
// setupTests.ts
import 'whatwg-fetch';
```

```ts
// App.tsx
import {
	RouterProvider,
	createBrowserRouter,
} from 'react-router-dom';
import routes from './routes';

const router = createBrowserRouter(routes);

function App() {
	return (
		<RouterProvider router={router}/>
	);
}

export default App;
```

- App 컴포넌트에서 라우트를 불러와서 하위 컴포넌트들에게 router를 제공하는 역할을 한다.

> 위 로직을 main.tsx로 옮길 수도 있어서 굳이 App이 있으나 마나하다.

```ts
// main.tsx
import ReactDOM from 'react-dom/client';
import App from './App';

function main() {
	const element = document.getElementById('root');

	if (!element) {
		return;
	}

	const root = ReactDOM.createRoot(element);

	root.render(<App />);
}

main();
```

### NavLink를 사용하여 현재 링크 스타일링

```ts
import { NavLink, useMatch, useNavigate } from 'react-router-dom';
import './Header.css';

function Header() {
	const aboutMatch = useMatch('/about');

	return (
		<header>
			<h1>Header</h1>
			<ul>
				<li>
					<NavLink to='/' style={({isActive, isPending}) => ({
						color: isActive ? 'red' : 'inherit',
					})}>메인페이지</NavLink>
				</li>
				<li>
					<NavLink to='/about' className={aboutMatch ? 'active' : ''}>About 페이지</NavLink>
				</li>
			</ul>
		</header>
	);
}

export default Header;
```

- `useMatch`를 사용하여 현재 경로와 일치하는지 boolean 값을 얻을 수도 있다.

### useNavigation으로 경로 이동하기

```ts
import { NavLink, useMatch, useNavigate } from 'react-router-dom';
import './Header.css';

function Header() {
	const navigate = useNavigate();
	const handleClickLogout = () => {
		navigate('/');
	};

	const aboutMatch = useMatch('/about');

	return (
		<header>
			<h1>Header</h1>
			<ul>
				<li>
					<NavLink to='/' style={({isActive, isPending}) => ({
						color: isActive ? 'red' : 'inherit',
					})}>메인페이지</NavLink>
				</li>
				<li>
					<NavLink to='/about' className={aboutMatch ? 'active' : ''}>About 페이지</NavLink>
				</li>
				<li>
					<button type='button' onClick={handleClickLogout}>Log out</button>
				</li>
			</ul>
		</header>
	);
}

export default Header;
```

- logout 버튼을 클릭하면 `useNavigate` hook을 사용하여 '/' 경로로 이동할 수 있다.

## 참조
