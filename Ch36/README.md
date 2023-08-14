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

- 변수에 기본값을 설정할 수 있음

```javascript
const [x, ...y] = [1, 2, 3];
console.log(x, y); // 1 [ 2,3 ]
```

- Rest 요소 사용가능 





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

- 우변에 객체 또는 객체로 평가될 수 있는 표현식(문자열, 숫자, 배열 등)을 할당하지 않으면 에러가 발생



```javascript
const { lastName, firstName } = user;
// 위와 아래는 동치
const { lastName: lastName, firstName: firstName } = user;

const user = { firstName: 'Gyumin', lastName: 'Lee' };
const { lastName: ln, firstName: fn } = user;

console.log(fn, ln); // Gyumin Lee
```

- 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다
- ln = 프로퍼티 키가 lastName인 프로퍼티 값
- fn = 프로퍼티 키가 firstName인 프로퍼티 값

```javascript
const { firstName = "Gyumin", lastName } = { lastName: 'Lee'}
console.log(firstName, lastName); // Gyumin Lee

const { firstName: fn = "Gyumin", lastName: ln } = { lastName: 'Lee' };
console.log(fn, ln); // Gyumin Lee
```

- 기본값도 설정이 가능



### 사용례

#### 객체에서 프로퍼티 키로 필요한 프로퍼티 값만 추출하여 변수에 할당하고 싶을 때

```javascript
const str = "Hello";
// String 래퍼 객체로부터 length 프로퍼티만 추출
const { length } = str;
console.log(length); // 5

const todo = { id: 1, content: "HTML", completed: true };
// todo 객체로부터 id 프로퍼티만 추출
const { id } = todo;
console.log(id); // 1
```



#### 객체를 인수로 전달받는 함수의 매개변수에 사용

```javascript
function printTodo(todo) {
  console.log(`할일 ${todo.content}은 ${todo.completed ? '완료' : '비완료'} 상태입니다.`);
}

printTodo({ id: 1, content: 'HTML', completed: true }); // 할일 HTML은 완료 상태
```

상기 예제를 아래처럼 간단하고 가독성 좋게 표현이 가능

```javascript
function printTodo({content, completed}) {
  console.log(`할일 ${content}은 ${completed ? '완료' : '비완료'} 상태입니다.`);
}

printTodo({ id: 1, content: 'HTML', completed: true }); // 할일 HTML은 완료 상태
```



#### 배열의 요소가 객체인 경우, 배열 디스트럭처링 할당과 객체 디스트럭처링 할당 혼용 가능

```javascript
const todos = [
  { id: 1, content: 'HTML', completed: true },
  { id: 2, content: 'CSS', completed: false },
  { id: 3, content: 'JS', completed: false }
];

// todos 배열의 두 번째 요소인 객체로부터 id 프로퍼티만 추출
const [, { id }] = todos;
console.log(id); // 2
```



#### 중첩 객체의 경우

```javascript
const user = {
  name: 'Lee', 
  address: {
    zipCode: '03068',
    city: 'Seoul'
  }
};

// address 프로퍼티 키로 객체를 추출하고, 이 객체의 city 프로퍼티 키로 값을 추출
const { address: {city} } = user;
console.log(city); // Seoul
```



#### Rest 프로퍼티 `...`

```javascript
const { x, ...rest } = { x: 1, y:2, z: 3};
console.log(x, rest); // 1 {y:2, z:3}
```



