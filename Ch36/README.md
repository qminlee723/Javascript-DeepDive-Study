# 36장. 디스트럭처링 할당

> Destructuring assignment(구조 분해 할당)이란 구조화된 배열과 같은 이터러블 또는 객체를 destructuring(비구조화, 구조 파괴)하여 1개 이상의 변수에 개별적으로 할당하는 것



## 36.1 배열 디스트럭처링 할당

```javascript
// ES6
const arr = [1, 2, 3];
const [one, two, three] = arr;
console.log(one, two, three); // 1 2 3

// ES5
var arr = [1, 2, 3];

var one = arr[0];
var two = arr[1];
var three = arr[2];

console.log(one, two, three); // 1 2 3
```

- ES6의 배열 디스트럭처링 할당은 배열의 각 요소를 배열로부터 추출하여 1개 이상의 변수에 할당
- 배열 디스트럭처링 할당의 대상(할당문의 우변)은 이터러블이어야 하며, 할당 기준은 배열의 인덱스이다.
- 배열 디스트럭처링 할당을 위해서는 할당 연산자 왼쪽에 값을 할당받을 변수를 선언해야 하며, 변수를 배열 리터럴 형태로 선언

```javascript
const [x, y] = [1, 2]

const [x, y]; // SyntaxError: Missing initializer in destructuring declaration
const [a, b]; // TypeError: {} is not iterable
```

- 이터러블을 할당하지 않으면 에러

```javascript
const [a, b] = [1, 2];
console.log(a, b); // 1 2

const [c, d] = [1];
console.log(c, d); // 1 undefined

const [e, f] = [1, 2, 3];
console.log(e, f); // 1 2

const [g, ,h] = [1, 2, 3];
console.log(g, h); // 1 3
```

* 배열 디스트럭처링 할당의 기준은 배열의 인덱스
  * 순서대로 할당됨
  * 변수의 갯수와, 이터러블의 요소 갯수가 반드시 일치할 필요는 없음

```javascript
// 기본값
const [a, b, c = 3] = [1, 2];
console.log(a, b, c); // 1 2 3

const [e, f = 10, g = 3] = [1, 2];
console.log(e, f, g); // 1 2 3 
```

- 배열 디스트럭처링 할당은 배열과 같은 이터러블에서 필요한 요소만 추출해서 변수에 할당하고 싶을 때 유용



```javascript
function parseURL(url = '') {
  const parseURL = url.match(/^(\w+):WV/([^/]+)\/(.*)$/)));
  console.log(parsedURL)
}
```





## 36.2 객체 디스트럭처링 할당

```javascript
// ES6
const user = { firstName: 'Gyumin', lastName: 'Lee' };

const { lastName, firstName } = user;
console.log(firstName, lastName); // Gyumin Lee

// ES5
var user = { firstName: 'Gyumin', lastName: 'Lee' };

var firstName = user.firstName;
var lastName = user.lastName;

console.log(firstName, lastName); // Gyumin Lee
```

