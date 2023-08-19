# 43. Ajax

## 43.1 Ajax란?

- Ajax(Asynchronouse JavaScript and XML)
-  바스크립트를 사용하여 **브라우저가 서버**에게 **비동기 방식**으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 **동적으로 갱신**하는 프로그래밍 방식
- 브라우저에서 제공하는 WebAPI인 `XMLHttpRequest` 객체를 기반으로 동작
  - `HMLHttpRequest`는 `HTTP`비동기 통신을 위한 메서드와 프로퍼티를 제공
  - 1999년 마이크로소프트가 개발 but 2005년 **구글 맵스**를 통해 웹 어플리케이션 개발 프로그래밍 언어로서 자바스크립트의 가능성 확인
    - 웹 브라우저에서, 자바스크립트와 Ajax를 기반으로 동작하는 구글 맵스가 데스크톱 애플리케이션과 손색이 없을 정도의 퍼포먼스와 부드러운 화면 전환 효과를 보여줌
  - 이전에는 **완전한 HTML**(`<html></html>`)을 서버로부터 전송받아 웹페이지 전체를 처음부터 다시 렌더링
    - 단점
      - 이전 웹페이지와 차이가 없어서 변경할 필요가 없는 부분까지 포함된 완전한 HTML을 서버로부터 매번 다시 전송받아 불필요한 데이터 통신 발생
      - 변경할 필요가 없는 부분까지 처음부터 다시 렌더링. 따라서 화면 전환시 화면이 순간적으로 깜박임
      - 클라이언트와 서버와의 통신이 동기 방식으로 동작하므로, 서버로부터 응답이 있을 때 까지 다음 처리는 블로킹
  - Ajax로 인한 변화
    - 변경할 필요가 있는 부분만 한정적으로 렌더링 하는 방식이 가능해 불필요한 데이터 통신 줄임
    - 변경할 필요가 없는 부분은 재렌더링하지 않아 화면 깜박임 없음
    - 클라이언트와 서버와의 통신이 비동기 방식으로 동작하기 때문에 서버에 요청을 보낸 후 블로킹이 발생하지 않음
    - 브라우저에서도 **빠른 퍼포먼스**와 **부드러운 화면 전환** 가능





## 43.2 JSON

> JSON(JavaScript Object Notation)은 **클라이언트와 서버** 간의 **HTTP 통신**을 위한 **텍스트 데이터 포맷.** 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷으로, 대부분의 프로그래밍 언어에서 사용할 수 있다.

### 43.2.1 JSON 표기 방식

- **키와 값**으로 구성된 순수한 텍스트

- **키**와 **문자열**은 반드시 **큰따옴표(`" "`)**

  ```javascript
  {
    "name": "Lee",
    "age": 20,
    "alive": true,
    "hobby": ["traveling", "tennis"]
  }
  ```

  

### 43.2.2 JSON.stringify

> 객체를 JSON 포맷의 문자열로 변환
>
> 이를 직렬화(serializing)라고 함

- **객체**를 직렬화

```javascript
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis']
}

// 객체를 JSON 포맷의 문자열로 변환
const json = JSON.stringify(obj);
console.log(typeof json, json);
// string { "name": "Lee", "age": 20, "alive": true, "hobby": ["traveling", "tennis"]}

// 객체를 JSON 포맷의 문자열로 변환하면서 들여쓰기 한다
const prettyJson = JSON.stringify(obj, null, 2);
console.log(typeof prettyJson, prettyJson)
/*
string {
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": [
  	"traveling", 
  	"tennis"
  ]
}
*/

// replacer 함수. 값의 타입이 Number이면 필터링되어 반환되지 않는다
function filter(key, value) {
  return typeof value === 'number' ? undefined : value;
}

// JSON.stringify 메서드에 두 번째 인수로 replace 함수를 전달
const strFilteredObject = JSON.stringify(obj, filter, 2);
console.log(typeof strFilteredObject, strFilteredObject);
/* 
	string {
  "name": "Lee",
  "alive": true,
  "hobby": [
  	"traveling", 
  	"tennis"
  ]
}
*/
```

- **배열**을 직렬화

```javascript
const todos = [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'JavaScript', completed: false }
]; 

// 배열을 JSON 포맷의 문자열로 변환
const json = JSON.stringify(todos, null, 2);
console.log(typeof json, json);

/* 
string [
	{
		"id": 1,
		"content": "HTML",
		"completed": false
	},
	{
		"id": 2,
		"content": "CSS",
		"completed": true
	},
	{
		"id": 3,
		"content": "JavaScript",
		"completed": false
	},
]
*/
```



### 43.2.3 JSON.parse

> JSON 포맷의 문자열을 객체로 변환
>
> 이를 역직렬화(deserializing)라고 한다

- JSON 포맷의 문자열로 변환된 **객체**를 역직렬화

```javascript
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis']
}

const json = JSON.stringify(obj);

// JSON 포맷의 문자열을 객체로 변환
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
// object {name: "Lee", age: 20, alive: true, hobby: ["traveling", "tennis"]}
```

- JSON 포맷의 문자열로 변환된 **배열**을 역직렬화

```javascript
const todos = [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'JavaScript', completed: false }
]; 

const json = JSON.stringify(todos);

// JSON 포맷의 문자열을 배열로 변환. 배열의 요소까지 객체로 변환
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
/*
object [
	{id: 1, content: 'HTML', completed: false },
	{id: 2, content: 'CSS', completed: true },
	{id: 3, content: 'JavaScript', completed: false }
]
*/
```



## 43.3 XMLHttpRequest

> 자바스크립트를 사용하여 HTTP 요청을 전송하려면 `XMLHttpRequest` 객체를 사용.
>
>  Web API인 XMLHttpRequest는 HTTP 요청 전송과 HTTP 응답 수신을 위한 다양한 메서드와 프로퍼티 제공

### 43.3.1 XMLHttpRequest 객체 생성

- `XMLHttpRequest` 생성자 함수를 호출해 생성
- 브라우저에서 제공하는 WebAPI 이므로 **브라우저 환경에서만** 정상적으로 실행됨

```javascript
const xhr = new XMLHttpRequest();
```



### 43.3.2 XMLHttpRequest 객체의 프로퍼티와 메서드

#### XMLHttpRequest 객체의 프로토타입 프로퍼티

| 프로토타입 프로퍼티     | 설명                                                         |
| ----------------------- | ------------------------------------------------------------ |
| <u>**readyState**</u>   | **HTTP 요청의 현재 상태를 나타내는 정수<br />`UNSENT` : 0<br />`OPENED` : 1<br />`HEADERS_RECEIVED` : 2<br />`LOADING` : 3<br />`DONE` : 4** |
| **<u>status</u>**       | **HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수<br />예) `200`, `404`** |
| **<u>statusText</u>**   | **HTTP 요청에 대한 응답 메시지를 나타내는 문자열<br />예) "OK"** |
| **<u>responseType</u>** | **HTTP 응답 타입 <br />예) `document`, `json`, `text`, `blob`, `arraybuffer`** |
| **<u>response</u>**     | **HTTP 요청에 대한 응답 몸체(response body).<br />response Type에 따라 타입이 다름** |
| responseText            | 서버가 전송한 HTTP 요청에 대한 응답 문자열                   |



#### XMLHttpRequest 객체의 이벤트 핸들러 프로퍼티

| 이벤트 핸들러 프로퍼티        | 설명                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| **<u>onreadystatechange</u>** | **`readyState` 프로퍼티 값이 변경된 경우**                   |
| onloadstart                   | HTTP 요청에 대한 응답을 받기 시작한 경우                     |
| onprogress                    | HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생            |
| onabort                       | `abort` 메서드에 의해 HTTP 요청이 중단된 경우                |
| <u>**onerror**</u>            | **HTTP 요청에 에러가 발생한 경우**                           |
| **<u>onload</u>**             | **HTTP 요청이 성공적으로 완료한 경우**                       |
| ontimeout                     | HTTP 요청 시간이 초과한 경우                                 |
| Unloaded                      | HTTP 요청이 완료된 경우<br />HTTP 요청이 성공 또는 실패하면 발생 |



#### XMLHttpRequest 객체의 메서드

| 메서드                      | 설명                                     |
| --------------------------- | ---------------------------------------- |
| **<u>open</u>**             | **HTTP 요청 초기화**                     |
| **<u>send</u>**             | **HTTP 요청 전송**                       |
| **<u>abort</u>**            | **이미 전송된 HTTP 요청 중단**           |
| <u>**setRequestHeader**</u> | **특정 HTTP 요청 헤더의 값을 설정**      |
| getResponseHeader           | 특정 HTTP 요청 헤더의 값을 문자열로 반환 |



#### XMLHttpRequest 객체의 정적 프로퍼티

| 정적 프로퍼티    | 값   | 설명                                  |
| ---------------- | ---- | ------------------------------------- |
| UNSENT           | 0    | `open` 메서드 호출 이전               |
| OPENED           | 1    | `open` 메서드 호출 이후               |
| HEADERS_RECEIVED | 2    | `send` 메서드 호출 이후               |
| LOADING          | 3    | 서버 응답 중(응답 데이터 미완성 상태) |
| DONE             | 4    | 서버 응답 완료                        |



### 43.3.3 HTTP 요청 전송

> 1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청을 초기화한다.
> 2. 필요에 따라 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 특정 HTTP 요청의 헤더 값을 설정한다
> 3. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청을 전송한다.

```javascript
const xhr = new XMLHttpRequest();

// 1. HTTP 요청 초기화
xhr.open('GET', '/users');

// 2. HTTP 요청 헤더 설정
xhr.setRequestHeader('content-type', 'application/json');

// 3. HTTP 요청 전송
xhr.send();
```



#### XMLHttpRequest.prototype.`open`

- 서버에 전송할 HTTP 요청을 초기화

```javascript
xhr.open(method, url[, async])
```

| 매개변수 | 설명                                                         |
| -------- | ------------------------------------------------------------ |
| method   | HTTP 요청 메서드("GET", "POST", "PUT", "DELETE" 등)          |
| url      | HTTP 요청을 전송할 URL                                       |
| async    | 비동기 요청 여부<br />옵션으로 기본값은 true<br />비동기 방식으로 동작 |

| HTTP 요청 메서드 | 종류           | 목적                  | 페이로드 |
| ---------------- | -------------- | --------------------- | -------- |
| GET              | index/retrieve | 모든/특정 리소스 취득 | X        |
| POST             | create         | 리소스 생성           | O        |
| PUT              | replace        | 리소스의 전체 교체    | O        |
| PATCH            | modify         | 리소스의 일부 수정    | O        |
| DELETE           | delete         | 모든/특정 리소스 삭제 | X        |



#### XMLHttpRequest.prototype.`send`

- **`open` 메서드로 초기화된 HTTP 요청**을 서버에 전송
  - `GET` 요청 메서드인 경우 데이터를 URL의 일부분인 **쿼리 문자열(query string)**로 서버에 전송
  - `POST` 요청 메서드인 경우 데이터(페이로드)를 **요청 몸체(request body)에 담아** 전송

- payload가 **객체**인 경우 반드시 `JSON.stringify` 메서드를 사용해 직렬화해서 전달해야 함

  ```javascript
  xhr.send(JSON.stringify({ id: 1, content: 'HTML', completed: false }));
  ```

- HTTP 요청 메서드가 `GET`인 경우, send 메서드에 페이로드를 전달한 인수는 무시되고, 요청 몸체는 null로 설정된다



#### XMLHttpRequest.prototype.`setRequestHeader`

- 특정 HTTP 요청의 헤더 값을 설정
- 반드시 `open` 메서드를 호출한 이후에 호출

- 자주 사용하는 HTTP 요청 헤더(`Content-type`, `Accept`)

  - `Content-type`

    - 요청 몸체에 담아 **전송할 데이터**의 MIME타입의 정보를 표현

      | MIME 타입   | 서브타입                                           |
      | ----------- | -------------------------------------------------- |
      | text        | Text/plain, text/html, text/css, text/javascript   |
      | application | application/json, application/x-www-form-urlencode |
      | multipart   | multipart/formed-data                              |

    - 요청 몸체에 담에 전송할 페이로드의 MIME 타입 지정 예

      ```javascript
      const xhr = new XMLHttpRequest();
      
      xhr.open('POST', '/users');
      
      // HTTP 요청 헤더 설정
      // 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
      xhr.setRequestHeader('content-type', 'application/json');
      
      xhr.send(JSON.stringify({ id: 1, content: 'HTML', completed: false}))
      ```

  - `Accept`

    - HTTP 클라이언트가 서버에 요청시 **서버가 응답할 데이터**의 MIME 타입은 Acceptfㅗ 지정

    - 서버가 응답한 데이터의 MIME 타입을 지정하는 예

      ```javascript
      // 서버가 응답할 데이터의 MIME 타입 지정: json
      xhr.setRequestHeader('accept', 'application/json');
      ```

    - Accept 헤더를 설정하지 않으면, send 메서드가 호출될 때 Accept 헤더가 `*/*`로 전송

  

### 43.3.4 HTTP 응답 처리

- 서버가 전송할 응답을 처리하려면, XMLHttpRequest 객체가 발생시키는 이벤트를 캐치해야 함

- XMLHttpRequest 객체가 가지는 이벤트 핸들러 프로퍼티 중 `readyState` 프로퍼티 값이 변경된 경우 발생하는 `readystatechange` 이벤트를 캐치해 HTTP 응답을 처리할 수 있다

- `readystatechange` 이벤트 캐치 예시

  ```javascript
  const xhr = new XMLHttpRequest();
  
  xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');
  
  xhr.send();
  
  // readystatechange 이벤트는 HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티가 변경될 때마다 발생
  xhr.onreadystatechange = () => {
    // readyState 프로퍼티는 HTTP 요청의 현재 상태를 나타냄
    // readyState 프로퍼티 값이 4(XMLHttpRequest.DONE)가 아니면, 서버 응답이 완료되지 않은 상태
    
    // 서버 응답이 완료되지 않았으면 아무것도 반환하지 않는다
    if (xhr.readyState !== XMLHttpReuqest.DONE) return;
    
    if (xhr.status === 200) {
      // 정상적으로 응답되었다면, response 프로퍼티에 서버의 응답 결과가 담김
      console.log(JSON.parse(xhr.response));
  		// {userId: 1, id: 1, title: "delectus aut autem", completed: false}
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
  ```

- `load` 이벤트 캐치 예시

  ```javascript
  const xhr = new XMLHttpRequest();
  
  xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');
  
  xhr.send();
  
  // load 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생
  xhr.onload = () => {
    if (xhr.status === 200) {
      console.log(JSON.parse(xhr.response));
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  }
  ```

  

