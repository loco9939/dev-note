# TypeScript

### 타입스크립트 사용 이유

- Javascript에서는 동적타이핑 언어를 사용하므로, 오류가 많이 발생할 수 있는데 이를 엄격한 타입스크립트를 선언함으로써 오류를 미연에 방지한다.
- VSCode에서 매우 편하다.
  - 자동완성 + 오류
- 라이브러리 별로 타입을 선언한 파일을 따로 제공한다. 아래는 React 타입 파일 (index.d.ts)

[React-DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react)

### 타입정의

```tsx
// no-inferrable-types 오류 발생
function add(x: number, y?: number = 0) {
  return x + y;
}

function add(x: number, y = 0) {
  return x + y;
}
```

- 옵셔널한 파라미터라면 기본값을 추가해주는게 좋다.

> 위와 같이 사용할 경우, Type number trivially inferred from a number literal, remove type annotation.eslint[@typescript-eslint/no-inferrable-types](https://typescript-eslint.io/rules/no-inferrable-types) 타입 오류를 발생한다.

### 타입 확장

```tsx
type Human = {
  name: string;
  age: number;
};

type Creature = {
  hp: number;
  mp: number;
};

type Person = Human & Creature;

let person: Person;

person = { name: "홍길동", age: 30, hp: 256, mp: 30 };
```

- `&` 연산자로 두가지 타입을 확장할 수 있다.
