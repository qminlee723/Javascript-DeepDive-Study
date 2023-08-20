# 35장 스프레드 문법

## 순서

35.1 함수 호출문의 인수 목록에서 사용하는 경우

35.2 배열 리터럴 내부에서 사용하는 경우

35.3 객체 리터럴 내부에서 사용하는 경우


## 시작하기 전

- 스프레드 문법
    
    하나로 뭉쳐있는 여러 값들의 집합을 펼쳐서 개별적인 값의 목록으로 만드는 문법
    

- 스프레드 문법 사용할 수 있는 대상
    
    for …of 문으로 순회할 수 있는 이터러블
    
    (Array, String, Map, Set, Dom 컬렉션, arguments)
    
- 예제
    
    ```tsx
    console.log(...[1, 2, 3]); // 1 2 3
    
    console.log(...'Hello'); // H e l l o
    
    console.log(...{ a: 1, b: 2 }); // TypeError
    ```
    
    1 2 3은 값이 아니라 값들의 목록
    
    → 즉, 스프레드 문법의 결과는 값이 아니다
    
    → 이는 스프레드 문법 (…)이 피연산자를 연산하여 값을 생성하는 연산자가 아님을 의미한다
    
    → 따라서 스프레드 문법의 결과는 변수에 할당할 수 없다
    
    ```tsx
    const list = ...[1, 2, 3]
    ```
    
    이처럼 스프레드 문법의 결과물은 값으로 사용할 수 없고 다음과 같이 쉼표로 구분한 값의 목록을 사용하는 문맥에서만 사용할 수 있음
    
    - 쉼표로 구분한 값의 목록을 사용하는 문맥
        1. 함수 호출문의 인수 목록
        2. 배열 리터럴의 요소 목록
        3. 객체 리터럴의 프로퍼티 목록

## 35.1 함수 호출문의 인수 목록에서 사용하는 경우

- Math 예제
    
    ```tsx
    const arr = [1, 2, 3];
    
    const max = Math.max(arr); // NaN
    ```
    
    ```tsx
    var arr = [1, 2, 3];
    
    var max = Math.max.apply(null, arr);
    ```
    
    ```tsx
    const arr = [1, 2, 3];
    const max = Math.max(...arr);
    ```
    
- Rest 파라미터와 혼동 주의
    
    ```tsx
    function foo(...rest) {
    	console.log(rest); // 1, 2, 3 -> [1, 2, 3]
    }
    
    foo(...[1, 2, 3]) // [1, 2, 3] -> 1, 2, 3
    ```
    

## 35.2 배열 리터럴 내부에서 사용하는 경우

1. concat
    
    ```tsx
    // ES5
    const arr = [1, 2].concat([3, 4]);
    console.log(arr); // [1, 2, 3, 4]
    
    // ES6
    const arr = [...[1, 2], ...[3, 4]];
    console.log(arr); // [1, 2, 3, 4]
    ```
    
2. splice
    
    ```tsx
    var arr1 = [1, 4];
    var arr2 = [2, 3];
    
    // ES5
    arr1.splice(1, 0, arr2);
    console.log(arr1); // [1, [2, 3], 4]
    
    // ES6
    arr.splice(1, 0, ...arr2);
    console.log(arr1); // [1, 2, 3, 4]
    ```
    
3. slice (배열 복사)
    
    ```tsx
    // ES5
    var origin = [1, 2];
    var copy = origin.slice();
    
    console.log(copy); // [1, 2]
    console.log(copy === origin) // f
    
    // ES6
    var origin = [1, 2];
    const copy = [...origin];
    
    console.log(copy); // [1, 2]
    console.log(copy === origin) // f
    ```
    
4. 이터러블을 배열로 변환
    
    ```tsx
    // 1
    function sum() {
    	return [...arguments].reduce((pre, cur) => pre + cur, 0);
    }
    
    console.log(sum(1, 2, 3)); // 6
    
    // 2
    const sum = (...args) => args.reduce((pre, cur) => pre + cur, 0);
    
    console.log(sum(1, 2, 3)); // 6
    ```
    
    단, 이터러블이 아닌 유사 배열 객체는 스프레드 문법의 대상이 될 수 없음
    

## 35.3 객체 리터럴 내부에서 사용하는 경우

```jsx
const obj = { x: 1, y: 2};
const copy = { ...obj };
copy // { x: 1, y: 2 }
obj === copy // false 

const merged = { x: 1, y: 2, ...{ a: 3, b: 4 } };
merged // { x: 1, y: 2, a: 3, b: 4 }
```

스프레드 프로퍼티는 객체 얕은 복사를 함

```jsx
const merged = { ...{ x: 1, y: 2 }, ...{ y: 10, z: 3 } };
merged // { x: 1, y: 10, z: 3}
```

이렇게 병합, 변경, 추가 모두 가능
