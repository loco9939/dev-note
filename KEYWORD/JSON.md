# JSON

## 정의

JavaScript Object Notation의 약자로, 경량 데이터 교환 방식을 의미한다.

JSON은 key-value 쌍, 배열 등 모든 데이터 오브젝트를 전달하기 위해 인간이 읽을 수 있는 텍스트를 사용한 **언어 독립적인 데이터 포맷**이다.

## 역사

JSON은 2000년대 초 어도비 플래시, 자바 애플릿 등의 브라우저 플러그인을 사용하지 않는 **무상태**, 실시간 서버 대 브라우저 통신 프로토콜의 요구에 의거하여 성장했다.

더글라스 크록포드는 표준 브라우저 기능을 사용하여 HTTP 연결 이후 추가 데이터 교환이 없으면 표준 브라우저 타임아웃 전에 해당 데이터를 재활용함으로써 웹 서버에 영속적인 반이중 통신을 지원하고 상태를 인지하는(stateful) 추상화 계층인 JSON 데이터 포맷을 제공하였다.

> 크록포드는 기업이나 지나치게 규칙을 따지는 사람을 조롱하고자 '소프트웨어는 선을 위해 쓰여야 하며, 악을 위해 쓰여서는 안된다'라는 조항을 넣기도 했다.

## 특징

텍스트로 이루어져 사람과 기계 모두 읽고 쓰기 용이하고 직렬화(stringify)와 파싱(parse)의 장점이 있다.

- JSON.parse: JSON 문자열을 객체로 변환하는 메서드
- JSON.stringify: JSON 파일을 문자열로 변환하는 메서드

또한, 프로그래밍 언어와 플랫폼에 독립적이므로 서로 다른 시스템간에 객체를 교환하기에 적절하다.

## 사용방법 및 규칙

```json
{
    "이름": "홍길동",
    "나이": 55,
    "성별": "남",
    "주소": "서울특별시 양천구 목동",
    "특기": ["검술", "코딩"],
    "가족관계": {"#": 2, "아버지": "홍판서", "어머니": "춘섬"},
    "회사": "경기 수원시 팔달구 우만동"
 }
```

- JSON에는 주석이 들어가면 안된다.
- JSON의 value 값으로 허용되는 자료형은 Number, String, Object, Boolean, Array, null 이다.

즉, 위 자료형들만 직렬화가 된다. 때문에 undefined는 직렬화가 되지 않기 때문에 value 값을 비어있게 하고 싶다면 null을 사용한다.

## 참조

[JSON - 위키백과](https://ko.wikipedia.org/wiki/JSON)