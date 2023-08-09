# 37. Set과 Map

## 37.1 Set

>  `Set` 객체는 중복되지 않는 유일한 값들의 집합이다

배열과 유사하지만 

- 동일한 값을 중복해서 포함할 수 없다
- 요소 순서에 의미가 없다
- 인덱스로 요소에 접근할 수 없다



### 37.1.1 Set 객체의 생성

- Set객체는 Set 생성자 함수로 생성되며, 인수를 전달하지 않으면 빈 객체가 생성된다

```javascript
const set = new Set();
console.log(set); // set(0) {}
```

- Set 생성자 함수는 이터러블을 인수로 전달받아 Set 객체를 생성한다. 이 때 이터러블의 중복된 값은 Set 객체에 요소로 저장되지 않는다. Set 객체의 특성을 이용하여 배열에서 중복된 요소를 제거할 수 있다.

```javascript
const set1 = new Set([1, 2, 3, 3]);
console.log(set1); Set(3) {1, 2, 3}

const set2 = new Set('hello');
console.log(set2); // Set(4) {'h', 'e', 'l', 'o'}
```



### 37.1.2 요소 개수 확인: `Set.prototype.size`

```javascript
const { size } = new Set([1, 2, 3, 3]);
console.log(size);
```

- size 프로퍼티는 setter 함수 없이 getter 함수만 존재하는 접근자 프로퍼티

```javascript
const set = new Set([1, 2, 3]);

console.log(Object.getOwnPropertyDescriptor(Set.prototype, 'size'));
// {set: undefined, enumerable: false, configurable: true, get: f}

set.size = 10; // 무시된다
console.log(set.size); // 3
```



### 37.1.3 요소 추가: `Set.prototype.add`

- 새로운 요소가 추가된 Set 객체를 반환한다. 따라서 `add` 메서드를 호출한 후에  `add` 메서드를 연속적으로 호출 할 수 있다
- 중복된 요소의 추가는 허용되지 않음 - 에러가 발생하지는 않고, 무시됨
- `NaN`과 `NaN`을 같다고 평가하여 중복추가 허용하지 않음
- `-0 `과  `+0`을 같다고 평가
- 객체나 배열과 같이 자바스크립트의 모든 값을 요소로 저장이 가능

```javascript
const set = new Set();
set.add(1);
console.log(set); // Set(1) {1}

set.add(2).add(3);
console.log(set); // Set(3) {1, 2, 3}

cont set2 = new Set();
set2.add(true).add(undefined).add(null).add({}).add([]).add(() => {}) // 가능
```





### 37.1.4 요소 존재 여부 확인: `Set.prototype.has`

- 특성 요소의 존재 여부를 나타내는 불리언 값 반환

```javascript
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(4)); // false
```



### 37.1.5 요소 삭제: `Set.prototype.delete`

- 삭제 성공 여부를 나타내는 불리언 값을 반환한다. 따라서 `add`메서드와 달리 연속적으로 호출이 불가능
- 삭제하려는 요소값을 인수로 전달해야 함
- 만약 존재하지 않는 Set 객체의 요소를 삭제하려면 에러 없이 무시됨

```javascript
const set = new Set([1, 2, 3]);

set.delete(2);
console.log(set); // set(2) {1, 3}

set.delete(5);
console.log(set); // set(2) {1, 3}
```



### 37.1.6 요소 일괄 삭제: `Set.prototype.clear`

- clear메서드는 언제나 `undefined`를 반환

```javascript
const set = new Set([1, 2, 3]);

set.clear();
console.log(set); // set(0) {}
```



### 37.1.7 요소 순회: `Set.prototype.forEach`

- `Array.prototype.forEach`와 유사하게 콜백함수와 forEach메서드의 콜백 함수 내부에서 this로 사용될 객체를 인수로 전달한다. 이때 콜백 함수는 다음과 같이 3개의 인수를 전달받는다
  - **첫번째 인수**: 현재 순회 중인 요소값
  - **두번째 인수**: 현재 순회 중인 요소값
    - 첫번째 인수와 동일한 값
    - 굳이 있는 이유는 `Array.prototype.forEach`와 인터페이스를 통일하기 위함
    - `Array.prototype.forEach`의 경우, 두 번째 인수로 현재 순회중인 요소값의 인덱스를 받음
  - **세번째 인수**: 현재 순회 중인 Set 객체 자체

- Set 객체는 **이터러블**
  -  `for..of`문으로 순회가 가능
  - 스프레드 문법과 배열 디스트럭처링의 대상이 됨

```javascript
// 인수확인
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));

// 1 Set(3) {1, 2, 3}
// 2 Set(3) {1, 2, 3}
// 3 Set(3) {1, 2, 3}
```

```javascript
// iterable 확인
const set = new Set([1, 2, 3]);
console.log(Symbol.iterator in set);

for (const value of set) {
  console.log(value); // 1 2 3 
}

console.log([...set]); // [1, 2, 3]
```



### 37.1.8 집합 연산

> Set 객체는 수학적 집합을 구현하기 위한 자료구조이므로, 교집합, 합집합, 차집합 등을 구현 할 수 있다

#### 교집합 `intersection`

집합 A와 집합 B의 공통 요소로 구성

```javascript
Set.prototype.intersection = function (set) {
  const result = new Set();
  
  for (const value of set) {
    // 2개의 set요소가 공통되는 요소이면 교집합의 대상이다
    if (this.has(value)) result.add(value);
  }
  
  return result;
}

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 교집합
console.log(setA.intersection(setB)); // set(2) {2, 4}
// setB와 setA의 교집합
console.log(setB.intersection(setA)); // set(2) {2, 4}
```

```javascript
Set.prototype.intersection = function (set) {
  return new Set([...this].filter(v => set.has(v)));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 교집합
console.log(setA.intersection(setB)); // set(2) {2, 4}
// setB와 setA의 교집합
console.log(setB.intersection(setA)); // set(2) {2, 4}
```



#### 합집합 `union`

집합 A와 집합 B의 중복 없는 모든 요소로 구성

```javascript
Set.prototype.union = function (set) { // this(Set객체)를 복사
  const result = new Set(this);

  for (const value of set) {
    // 합집합은 2개의 Set객체의 모든 요소로 구성된 집합이다. 중복된 요소는 포함되지 않는다. 
    result.add(value);
  }
	return result; 
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 합집합
console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4} 
// setB와 setA의 합집합
console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
```

```javascript
Set.prototype.union = function (set) { // this(Set객체)를 복사
  return new Set([...this, ...set]);
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 합집합
console.log(setA.union(setB)); // Set(4) {1, 2, 3, 4}  
// setB와 setA의 합집합
console.log(setB.union(setA)); // Set(4) {2, 4, 1, 3}
```



#### 차집합 `difference`

집합 A에는 존재하지만, 집합 B에는 존재하지 않는 요소로 구성

```javascript
Set.prototype.difference = function (set) {
  // this(Set 객체)를 복사
  const result = new Set(this);
  
  for (const value of set) {
    // 차집합은 어느 한쪽 집합에는 존재하지만, 다른 한 쪽 집합에는 존재하지 않는 요소로 구성된 집합
    result delete(value);
  }
  
  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 차집합
console.log(setA.difference(setB)); // Set(2) {1, 3} 
// setB와 setA의 차집합
console.log(setB.difference(setA)); // Set(0) {}
```

```javascript
Set.prototype.union = function (set) { // this(Set객체)를 복사
  return new Set([...this].filter(v => !set.has(v)));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 차집합
console.log(setA.difference(setB)); // Set(2) {1, 3} 
// setB와 setA의 차집합
console.log(setB.difference(setA)); // Set(0) {}
```



#### 부분집합과 상위집합

```javascript
// this가 subset의 상위 집합인지 확인
Set.prototype.isSuperset = function (subset) {
  for (const value of subset) {
    // superset의 모든 요소가 subset의 모든 요소를 포함하는지 확인
    if (!this.has(value)) return false;
  }
  
  return true;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 상위 집합인지 확인
console.log(setA.isSuperset(setB)); // true
// setB와 setA의 상위 집합인지 확인
console.log(setB.isSuperset(setA)); // false
```

```javascript
// this가 subset의 상위 집합인지 확인
Set.prototype.isSuperset = function (subset) {
  const supersetArr = [...this];
  return [...subset].every(v => supersetArr.includes(v));
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

// setA와 setB의 상위 집합인지 확인
console.log(setA.isSuperset(setB)); // true
// setB와 setA의 상위 집합인지 확인
console.log(setB.isSuperset(setA)); // false
```





## 37.2 Map

### 37.2.1 Map 객체의 생성

### 37.2.2 요소 개수 확인

### 37.2.3 요소 추가

### 37.2.4 요소 취득

### 37.2.5 요소 존재 여부 확인

### 37.2.6 요소 삭제

### 37.2.7 요소 일괄 삭제

### 37.2.8 요소 순회