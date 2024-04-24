# Seperation of Concern(SoC, 관심사의 분리)

## 정의

관심사란, 프로그램 코드에 영향을 미치는 정보의 집합을 말한다. 관심사의 분리는 이러한 정보들의 집합을 잘 정의된 인터페이스를 사용하여 캡슐화시킴으로써 달성한다.

> 캡슐화는 정보 숨기기의 하나의 수단이다.

## 사용이유

관심사 분리를 사용하면 각 정보의 집합에서 높은 자유도가 생긴다. 가장 일반적으로는 코드의 단순화 및 유지보수의 자유도가 증가한다.

인터페이스 뒤에서 관심사의 세세한 부분을 숨길 수 있기 때문에, 자유도가 높아지고 이에 따라 코드의 세세한 부분을 모르더라도 자유롭게 사용이 가능하다는 장점이 있다.

## 특징

- 관심사 분리는 추상화의 일종이기 때문에 대부분 추상화에서 그렇듯 인터페이스 추가는 필수적이다.

## 어떤 기준으로 관심사를 분리할 수 있을까?

흔히 사용되는 `Layered Architecture`에서는 사용자에게 가까운 것과 먼 것으로 구분한다.

사용자와 가장 가까운 것은 UI를 다루는 부분이고 그 다음은 Business Logic을 다루는 부분, 그 너머에는 데이터에 접근하고 저장하는 부분으로 나눌 수 있다.

각 부분은 하나의 역할, 하나의 관심사로 구분 및 분리함으로써 복잡도를 낮추고 자유도를 높인다.

크게 `Input → Process → Output` 3단계로만 코드를 구분하더라도 코드를 이해하고 유지보수성을 높이는데 큰 도움이 된다.

하나의 `Output`은 사용자에게 다시 `Input`을 요청하게 되고 프로그램이 순환하는 구조를 갖게 된다.

## 참조

[관심사의 분리 - 위키백과](https://ko.wikipedia.org/wiki/%EA%B4%80%EC%8B%AC%EC%82%AC_%EB%B6%84%EB%A6%AC)