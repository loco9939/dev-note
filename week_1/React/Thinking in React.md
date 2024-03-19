# Thinking in React

우리가 애플리케이션을 만들기 위해 React를 사용한다. 애플리케이션의 설계를 잘하기 위해선 React가 어떻게 흘러가는지 알아야한다.

React로 UI를 구현하기 위해서는 5가지 단계를 거친다.

### 1. 컴포넌트로 쪼개기

컴포넌트를 쪼개는 단위는 관점에 따라 달라진다.

- 프로그래밍 관점: 새로운 함수나 객체를 만드는 방식처럼 이상적으로는 한 번에 한 가지 일만 해야한다.
- CSS 관점: class 선택자의 계층 구조
- Design 관점: 디자인 계층 구조

![컴포넌트쪼개기](https://ko.react.dev/images/docs/s_thinking-in-react_ui_outline.png)

위 그림처럼 디자인 시안을 두고 네모 박스를 치면서 컴포넌트를 어떻게 쪼개고 어떤 계층 구조를 가질지 생각해보자.

- FilterableProductTable
  - SearchBar
  - ProductTable
    - ProductCategoryRow
    - ProductRow

### 2. 정적인 버전 구현하기

컴포넌트 계층 구조를 만든 후 가장 먼저 해야할 일은 상호작용 기능을 제외한 데이터 모델로부터 UI를 렌더링하는 정적인 버전을 구현하는 것이다.

데이터는 최상단 컴포넌트에서 시작하여 하위 컴포넌트 방향으로 전달되는 `단방향 데이터 흐름`의 특징을 갖는다.

❗️정적인 버전을 사용할 때 주의할 점은 state를 사용할 필요가 없다.

> state는 시간이 지남에 따라 데이터가 변하는 곳에 사용한다.

### 3. 최소한의 데이터만으로 UI State(상태) 표현하기

정적인 UI를 상호작용할 수 있게 만들기 위해선 사용자가 데이터를 변경할 수 있도록 해줘야한다.

위 애플리케이션에서의 데이터들이 어떤 것이 있는지 생각해본다.

1. 제품 원본 목록
2. 사용자가 입력한 검색어
3. 체크박스의 값
4. 필터링된 제품 목록

이 중 state가 되어야하는 것은 아래 질문들을 통해 결정할 수 있다.

> Q. 시간이 지나도 변하지 않는가? 그럼 state가 아니다. ❌

> Q. 부모한테서 props로 전달받는가? 그럼 state가 아니다. ❌

> Q. 컴포넌트 안의 다른 state, props를 이용해 계산이 가능한가? 그럼 state가 아니다. ❌

1. 제품 원본 목록: 부모로부터 props로 전달되었기 때문에, state가 아니다. ❌
2. 사용자가 입력한 검색어: 시간이 지남에 따라 변하고 다른 요소로 부터 계산될 수 없으니 state이다. ✅
3. 체크박스의 값: 2번과 마찬가지이므로 state이다. ✅
4. 필터링된 제품 목록: 원본 제품 목록을 받아서 사용자가 입력한 검색어로 계산이 가능하므로, state가 아니다. ❌

### 4. state 위치 지정하기

데이터 중 state인 것을 알아냈으니, state를 어디에 위치 시킬지 생각해봐야한다.

**🔑 state 위치 정하는 Tip**

1. 해당 state가 렌더링 되는 모든 컴포넌트 찾는다.
2. 그들의 가장 가까운 공통되는 부모 컴포넌트를 찾는다.

공통되는 부모 컴포넌트에 둬도 되고, 공통되는 부모의 상위 컴포넌트에 둬도 된다.

> 만약 정하기 어렵다면, state를 소유하는 컴포넌트를 상위계층에 하나 추가한다.

```js
function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState("");
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar filterText={filterText} inStockOnly={inStockOnly} />
      <ProductTable
        products={products}
        filterText={filterText}
        inStockOnly={inStockOnly}
      />
    </div>
  );
}
```

위치를 지정했으면 state가 필요한 자식 컴포넌트에 props로 전달해준다.

### 5. 역 데이터 흐름 추가하기

지금까지의 데이터 흐름은 부모에서 자식으로 단방향 데이터 흐름이었다. 하지만 사용자 입력에 따라 state 변경을 하기 위해선, 역방향 데이터 흐름이 필요하다.

즉, 자식 컴포넌트에서 state를 업데이트할 수 있어야한다. 이를 위해선 state 변경함수를 props로 전달 받아야한다.

```js
function FilterableProductTable({ products }) {
  const [filterText, setFilterText] = useState('');
  const [inStockOnly, setInStockOnly] = useState(false);

  return (
    <div>
      <SearchBar
        filterText={filterText}
        inStockOnly={inStockOnly}
        onFilterTextChange={setFilterText}
        onInStockOnlyChange={setInStockOnly} />
```

```js
function SearchBar({
  filterText,
  inStockOnly,
  onFilterTextChange,
  onInStockOnlyChange
}) {
  return (
    <form>
      <input
        type="text"
        value={filterText}
        placeholder="Search..."
        onChange={(e) => onFilterTextChange(e.target.value)}
      />
      <label>
        <input
          type="checkbox"
          checked={inStockOnly}
          onChange={(e) => onInStockOnlyChange(e.target.checked)}
```
