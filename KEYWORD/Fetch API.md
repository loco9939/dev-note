# Fetch API

## 정의

네트워크 통신을 포함한 리소스 취득을 위한 인터페이스를 제공하는 API

## 사용법

```js
const response = await fetch('http://localhost:3000/products');
```

이 때, response.body는 `ReadableStream` 값이다. 이를 읽기 위해서는 decode 로직을 실행해줘야한다.

> `ReadableStream`이란, 바이트 데이터를 읽을 수 있는 스트림을 제공한다. `getReader()` 메소드를 사용하여 Reader를 만들고 그 Reader에 스트림을 고정한다. 스트림이 고정되어 있는 동안 다른 Reader를 얻을 수 없다.

```js
// decode
const reader = response.body.getReader();

const chunk = await reader.read();
// → chunk.value는 Uint8Array 타입.
// → 원래는 chunk.done이 true일 때까지 반복해야 한다.

const body = new TextDecoder().decode(chunk.value);

const data = JSON.parse(body);
```

하지만 Fetch API는 json이라는 메소드를 지원한다.

```js
const response = await fetch('http://localhost:3000/products');
const data = await response.json();
```

추가로 다른 HTTP 메서드를 사용하고 싶거나 옵션들을 설정할 수 있다.

## Post 메서드 예제

```js
// POST 메서드 구현 예제
async function postData(url = "", data = {}) {
  // 옵션 기본 값은 *로 강조
  const response = await fetch(url, {
    method: "POST", // *GET, POST, PUT, DELETE 등
    mode: "cors", // no-cors, *cors, same-origin
    cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
    credentials: "same-origin", // include, *same-origin, omit
    headers: {
      "Content-Type": "application/json",
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect: "follow", // manual, *follow, error
    referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
    body: JSON.stringify(data), // body의 데이터 유형은 반드시 "Content-Type" 헤더와 일치해야 함
  });
  return response.json(); // JSON 응답을 네이티브 JavaScript 객체로 파싱
}

postData("https://example.com/answer", { answer: 42 }).then((data) => {
  console.log(data); // JSON 데이터가 `data.json()` 호출에 의해 파싱됨
});
```

## 참조

[Fetch API 사용하기 - MDN](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)

[ReadableStream - MDN](https://developer.mozilla.org/ko/docs/Web/API/ReadableStream)

[TextDecoder - 모던자바스크립트](https://ko.javascript.info/text-decoder)