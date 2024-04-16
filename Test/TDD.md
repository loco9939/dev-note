# TDD

## 정의

**Test-Driven-Development(TDD)** 테스트 주도 개발은 테스트 코드를 작성하고 테스트 통과 코드를 구현하고 리팩터링 하는 과정을 통해 개발을 진행하는 방법론이다.

## TDD 사용이유

1. 버그 발생 가능성 낮추는 효과

테스트 코드를 먼저 작성하고 실제 코드를 작성하기 때문에 테스트 되지 않는 코드는 실제 코드에 적용될 수 없다. 즉, 프로그램에서 모든 코드가 테스트되기 때문에 버그의 발생 가능성이 줄어든다.

2. 요구사항을 명확히 드러내는 효과

테스트 자체가 요구사항을 분명하게 드러내는 효과가 있어 테스트에 맞게 코딩하다보면 자연스레 프로그램의 디자인이 SimpleDesign이 되는 경향이 있다.

3. 개발의 유연성을 높이는 효과

테스트가 잘 작성되어 있으면 프로그램에 여러 가지 변경 작업을 할 때, 변경으로 인해 다른 부분에서 예상치 못한 문제가 발생하는 것을 쉽게 알아차릴 수 있어 변경 작업을 두려움 없이 할 수 있게 되어 최종적으로 생산성 향상에 기여한다.

## 특징

TDD의 특징은 구현보다 테스트 코드를 먼저 작성하여 인터페이스와 스펙을 먼저 정의한다는 것이 특징이다.

### TDD Cycle

1. Red → 실패하는 테스트 코드를 작성. 인터페이스와 스펙에 집중한다.
2. Green → 최대한 빨리 테스트 코드를 통과하는 코드 구현한다. 올바른 방법이 아니어도 괜찮다. ex) 하드코딩
3. Refactor → 리팩터링을 통해 코드를 올바르게 개선한다. TDD에서 가장 중요한 부분이다. ⭐️

위 사이클을 반복하며 연습하여 익숙해져야 TDD를 올바르게 사용할 수 있고 클린코드도 작성할 수 있게된다.

> **작은 단계를 찾고 코드에서 피드백을 얻는 것이 어렵고 중요하다.** 2번이 어렵다면, 1번으로 돌아가서 더 작고 쉬운 문제를 정의하고 3번을 위해 의도를 드러내고 중복을 찾아 제거하는 연습을 해야한다.

## 사용방법

`Jest`라는 테스트 라이브러리를 사용하여 단위테스트를 할 수 있고, `React-Testing-Library`를 사용하여 E2E 테스트를 진행할 수 있다.

아래는 Jest 라이브러리를 사용하여 `Describe-Context-It` 패턴으로 테스트 코드를 작성한 예시이다.

```ts
function add(x: number, y: number): number {
	return x + y;
}

const context = describe;

describe('add 함수', () => {
	context('두 개의 양수가 주어졌을 때,', () => {
		it('항상 두 개의 숫자보다 큰 값을 돌려준다.', () => {
			expect(add(1, 2)).toBeGreaterThan(1);
			expect(add(1, 2)).toBeGreaterThan(2);
		});
	});

	context('하나의 양수와 음수가 주어졌을 때,', () => {
		it('항상 하나의 양수보다 작은 값을 돌려준다.', () => {
			expect(add(1, -1)).toBeLessThan(1);
			expect(add(1, -2)).toBeLessThan(1);
		});
	});
});
```

## 참조

[Xper: TDD - 바로가기](https://web.archive.org/web/20070628064054/http://xper.org/wiki/xp/TestDrivenDevelopment)

[TDD 수련법 - 바로가기](https://web.archive.org/web/20061012050617/http://xper.org/wiki/xp/TDD_bc_f6_b7_c3_b9_fd)

[TDD FAQ - 바로가기](https://megaptera.notion.site/TDD-FAQ-edcaa36e8cf246d9a2aa546d99810202)

[Jest를 이용한 간단한 TDD 예제 - 바로가기](https://megaptera.notion.site/Jest-TDD-4149c9f23b2d4402ad44d4d569a3ae13)

[이혜승 - 테알못 신입은 어떻게 테스트를 시작했을까?](https://www.slideshare.net/OKJSP/okkycon-120498066)