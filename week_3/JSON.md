# JSON

JavaScript Object Notation의 약자로, 경량 데이터 교환 방식을 의미한다.

읽고 쓰기 쉽고 직렬화(stringify)와 파싱(parse)의 장점이 있다.

- JSON.parse: JSON 문자열을 객체로 변환하는 메서드
- JSON.stringify: JSON 파일을 문자열로 변환하는 메서드

## 규칙

- JSON에는 주석이 들어가면 안된다.
- JSON의 value 값으로 허용되는 자료형은 Number, String, Object, Boolean, Array, null 이다.

즉, 위 자료형들만 직렬화가 된다. 때문에 undefined는 직렬화가 되지 않기 때문에 value 값을 비어있게 하고 싶다면 null을 사용한다.
