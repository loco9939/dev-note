# 온라인 쇼핑몰 프로젝트 개요

그동안의 배웠던 부분을 적용 및 응용하여 온라인 쇼핑몰 프로젝트를 진행한다.

## 용어

- Product: 상품
	- Summary: 상품에 대한 요약 정보
	- Detail: 상품에 대한 상세 정보
	- Image: 상품 이미지
	- Option: 상품에 대한 상세 옵션 종류 (색상, 크기 등)
		- OptionItem: 옵션에 대한 상세 옵션 값 (옵션이 색상이라면 이건 Blue, Red 같은 걸 의미함)

- Category: 상품에 대한 분류
- Cart: 장바구니
	- LineItem: 장바구니에 담긴 것 (상품, 옵션, 수량 등을 동시에 다룸)
	- 여기서도 Option과 OptionItem을 사용한다.
	- 용어는 동일하지만 Product와 다른 구성을 갖기 때문에, 여기서는 Product와 Order라는 접두어를 활용한다.
	- 시스템을 분리할 수 있다면, 근본적으로 나누는 걸 추천(상품 정보 확인 / 장바구니 / 주문).
- Order: 주문
	- 여기서도 동일한 LineItem 활용.
- User: 사용자

## 기능

1. 상품 목록 확인
2. 상품 상세 정보 확인
3. 장바구니에 상품 담기
4. 주문하기 → 배송지 입력, 결제
5. 주문 목록 확인
6. 주문 상세 확인
7. 로그인
8. 회원 가입

## 화면

1. 홈 페이지 - `/`
2. 상품 목록 페이지 - `/products`
3. 상품 상세 페이지 - `/products/{id}`
4. 장바구니 페이지 - `/cart`
5. 주문 페이지 - `/order`
6. 주문 완료 페이지 - `/order/complete`
7. 주문 목록 페이지 - `/orders`
8. 주문 상세 페이지 - `/orders/{id}`
9. 로그인 페이지 - `/login`
10. 회원 가입 페이지 - `/signup`
11. 회원 가입 완료 페이지 - `/signup/complete`

## 개발 환경 셋팅 가이드

[개발환경 셋팅 가이드 바로가기](https://github.com/megaptera-kr/textbook/tree/main/start-megaptera-shop-client)

### 사용하는 라이브러리

- `React Router`
- `styled-components`
- `styled-reset`
- `usehooks-ts`
- `Axios`: REST API 사용을 위한 HTTP 클라이언트.
- `tsyringe`
- `reflect-metadata`
- `usestore-ts`
- `jest-dom`: React Testing Library에서 활용할 수 있는 DOM 확인용 Matcher 모음.
- `MSW`