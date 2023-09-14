## 순서

46.1 제너레이터란?

46.2 제너레이터 함수의 정의

46.3 제너레이터 객체

46.4 제너레이터의 일시 중지와 재개

46.5 제너레이터의 활용

46.6 async/await


   
## 46.1 제너레이터란?

- 제너레이터
    - ES6에 도입
    - 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수
- 일반 함수와 차이
    
    
    |  | 일반 함수 | 제너레이터 함수 |
    | --- | --- | --- |
    | 1) 함수 실행의 제어권 | 함수 | 함수 호출자 |
    | 2) 함수 상태 공유 | 함수 상태 변경 불가능 | 함수 호출자에게 상태를 전달할 수 있고 전달 받을 수도 있음 |
    | 3) 함수 반환 값 | 함수 값을 반환 | 제너레이터 객체 반환
    (함수 코드 실행하는 것이 아님) |
    
    1) 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있음
    
    → 함수의 제어권을 함수가 독점하는 것이 아니라 함수 호출자에게 양도 할 수 있다는 것을 의미
    
    2) 제너레이터 함수는 함수 호출자와 함수의 상태를 주고 받을 수 있음
    
    → 일반 함수는 함수가 실행되고 있는 동안 함수 외부에서 함수 내부로 값을 전달하여 함수의 상태를 변경할 수 없음. 하지만 제너레이터 함수는 함수 호출자와 양방향으로 함수의 상태를 주고 받을 수 있음
    
    3) 제너레이터 함수를 호출하면 제너레이터 객체를 반환함
    
    → 일반 함수를 호출하면 함수 코드를 실행하는 것이 아니라 이터러블이면서 동시에 이터레이터인 제너레이터 객체를 반환함
    

## 46.2 제너레이터 함수의 정의

- 정의방법
    - funtion* 키워드로 선언
    - 하나 이상의 yield 표현식 포함
- 코드
    
    ```jsx
    // 제너레이터 함수선언문 
    function* genDecFunc() {
    	yield 1; 
    }
    
    // 제너레이터 함수 표현식
    const genExpFunc = function* () { 
    	yield 1;
    };
    
    // 제너레이터 메서드
    const obi = {
    	* genobjMethod() {
    		yie1d 1;
    	};
    }
    
    // 제너레이터 클래스메서드 
    class MyClass {
    	* genClsMethod() { 
    		yield 1;
    	}
    }
    ```
    
    - * 의 위치는 function 키워드와 함수 이름 사이라면 어디든지 상관없음
    - 하지만 일관성을 유지하기 위해 function 키워드 바로 뒤에 붙이는 걸 권장
    
    ```jsx
    function* genFunc() { yield 1; } (추천)
    function * genFunc() { yield 1; } (o)
    function *genFunc() { yield 1; } (o)
    function genFunc() { yield ;1 ) (o)
    ```
    
    - 제너레이터 함수는 화살표 함수로 정의할 수 없음
    
    ```jsx
    const genArrowFunc= * () => { 
    	yield ;1
    }; // SynaxtEror Unexpectedtoken '*'
    ```
    
    - 제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출할 수 없음
    
    ```jsx
    function* getFunc() { 
    	yield ;1
    }
    new genFunc(); // TypeError: genFunc is not a constructor
    ```
    

## 46.3 제너레이터 객체

- 제너레이터 함수 반환값
    - 제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환함
    - 제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터
        - 다시말해 (자세히)
            - 제너레이터 객체는 Symbol.iterator 메서드를 상속받는 이터러블이면서 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 이터레이터
            - Symbol.iterator 메서드를 호출해서 별도로 이터레이터를 생성할 필요가 없음
        
        ```jsx
        // 제너레이터 함수
        function* getFunc() {
        	yield ;1
        	yield ;2
        	yield ;3
        }
        
        // 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다. 
        const generator = genFunc();
        
        // 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.
        // 이터러블은 Symbol.iterator 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속 받은 객체다.
        console.log(Symbol.iterator in generaor); // T
        
        // 이터레이터는 next 메서드를 갖는다.
        console.log('next' in generator); // T
        ```
        
- return, throw
    - 제너레이터 객체는 next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 return, throw 메서드를 갖음
    
    - next 메서드를 호출하면 제너레이터 함수의 yield 표현식까지 코드블록을 실행하고 yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환
    - return 메서드를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, t rue를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환
    
    - throw 메서드를 호출하면 인수로 전달받은 에러를 발생시키고 undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환

## 46.4 제너레이터의 일시 중지와 재개

- 제너레이터 성질에 의한 일시 중지와 재개
    1. 함수 실행의 제어권
        - 제너레이터는 yield 키워드와 next 메서드를 통해 실행을 일시 중지 했다가 필요한 시점에 다시 재개할 수 있음
        - 일반함수는 호출 이후 제어권을 함수가 독점하지만 제너레이터는 함수 호출자에게 제어권을 양도하여 필요한 시점에 함수 실행을 재개
    2. 함수 반환 값
        - 제너레이터 함수를 호출하면 제너레이터 함수의 코드 블록이 실행되는 것이 아니라 제너레이터 객체를 반환
        - 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖음
        - 제너레이터 객체의 next 메서드를 호출하면 제너레이터 함수의 코드 블록 실행
    3. 일반함수와 차이점
        - 일반 함수처럼 한번에 코드 블록의 모든 코드를 일괄 실행하는 것이 아니라 yield 표현식까지만 실행함
        - yield 키워는 제너레이터 함수의 실행을 일시 중지시키거나 yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환함

## 46.5 제너레이터의 활용

- 활용
    1. 이터러블의 구현
    2. 비동기 처리

- 이터러블 구현
    - 에터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블을 구현할 수 있음
    - 무한 피보나치 수열 생성하는 함수 구현 예제
        
        ```jsx
        // 이터레이션 프로토콜 준수
        const infiniteFibonacci = (function () {
        	let [pre, cur] = [0, 1];
        	return {
        		[Symbol.iterator]() { return this; },
        		next() {
        			[pre, cur] = [cur, pre + cur];
        			// 무한 이터러블이므로 done 프로퍼티를 생략
        			return { value: cur };
        		}
        	}
        }());
        
        for(const num of infiniteFibonacci) {
        	if (num > 10000) break;
        }
        ```
        
        ```jsx
        // 제너레이터 함수
        const infiniteFibonacci = (function* () {
        	let [pre, cur] = [0, 1];
        
        	while (true) {
        		[pre, cur] = [cur, pre + cur];
        		yield cur;
        	}
        }());
        
        for(const num of infiniteFibonacci) {
        	if (num > 10000) break;
        }
        ```
        
    
- 비동기 처리
    - 제너레이터 함수의 next 메서드와 yield 표현식으로 통해 함수 호출자와 함수의 상태 주고 받는 성질을 이용해 프로미스를 사용한 비동기 처리를 동기처럼 구현
    - 프로미스 후속 처리 메서드 then/catch/finally 없이 비동기 처리 결과를 반환하도록 구현
    

## 46.6 async/await

- 도입 배경
    - ES8
    - 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기처럼 동작하도록 구현

- async/await
    - 프로미스 기반으로 동작
    - 프로미스의 then, catch, finally 후속 처리 메서드에 콜백함수를 전달해서 비동기 처리 결과를 후속 처리할 필요 없이 마치 동기처럼 프로미스를 사용 가능
    - 다시 말해, 프로미스의 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있음
    - 위 예제 async/await 버전
        
- async
    - await 키워드는 반드시 async 함수 내부에서 사용해야 함
    - async 함수는 반드시 async 키워드를 사용해 정의하며 언제나 프로미스 반환
    - async 함수가 명시적으로 프로미스를 반환하지 않더라고 async 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환
    
    ```jsx
    // async 함수 선언문
    async function foo(n) { return n; )
    foo(1).then(v => console.log(v)); // 1
    
    // async 함수 표현식
    const bar= async function (n) { return n; );
    bar(2).then(v => console.log(v)); // 2
    
    // async 화살표 함수
    const bax = async n => n;
    bax(3).then(v => console.log(v)); // 3
    
    // async 메서드
    const obj = {
    	async foo(n) { return n; }
    }
    obj.foo(4).then(v => console.log(v));
    ```
    
- await
    - 프로미스 setteled 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환
    - await 키워드는 반드시 프로미스 앞에서 사용해야 함
    
    ```jsx
    const fetch = require('node-fetch');
    
    const getGithubUserName = async id => {
    	const res = await fetch(`~/${id}`); // 1
    	const { name } = await res.json(); // 2
    	console.log(name);
    }
    
    getGithubUserName('ungmo2');
    ```
    
    - 설명
        - await 키워드는 프로미스가 settled 상태가 될 때까지 대기
        - 따라서 [1]의 fetch 함수가 수행한 HTTP 요청에 대한 서버의 응답이 도착해서 fetch 함수가 반환한 프로미스가 settled 상태가 될 때까지 [1]은 대기
        - 이후 프로미스가 settled 상태가 되면 프로미스가 resolve한 처리 결과가 res 변수에 할당
        
        → await 키워드는 다음 실행을 일시 중지시켰다가 프로미스가 settled 상태가 되면 다시 재개
        
    - 주의할 점
        - await를 써도 되는 상황인지 고려하고 사용하기 (순차적으로 처리할 필요가 있는지 확인하고 사용하기)

- 에러 처리
    - 비동기 처리를 위한 콜백 패턴의 단점 중 가장 심각한 것은 에러 처리가 곤란하다는 것
    - (45.1.2절 에러처리의 한계) 에러는 호출자 방향으로 전파
    - 즉, 콜 스택의 아래방향(실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파
    - 하지만 비동기 함수의 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에 try…catch문을 사용해 에러를 캐치할 수 없음

- async/await에서 에러 처리
    - but, async/await에서 에러 처리는 try…catch문을 사용할 수 있음
    - 콜백함수를 인수로 전달 받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기에  호출자가 명확하기 때문
    
    → async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환
    
    → 따라서 async 함수를 호출하고 Promise.prototype.catch 후속 처리 메서드를 사용해 에러 처리를 캐치할 수도 있음
    
    ```jsx
    const fetch = require('node-fetch');
    const foo = async() => { 
    	const wrongUrl = '~';
    	const response = await fetch(wrongUrl);
    	const data = await response. json();
    	return data; 
    };
    
    foo()
     .then(console.log)
    	.catch(console.error); // TypeError: Failed to fetch
    ```
