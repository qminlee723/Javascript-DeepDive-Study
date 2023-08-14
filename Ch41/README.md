# 41. 타이머

## 41.1 호출 스케쥴링

> 함수를 명시적으로 호출하지 않고, 일정 시간이 경과된 이후에 호출되도록 함수 호출을 예약하려면 타이머 함수를 사용한다. 이를 호출 스케쥴링(scheduling a call)이라고 한다.



#### 자바스크립트가 제공하는 타이머 함수

- 타이머 생성 타이머 함수
  - `setTimeout`
  - `setInterval`

- 타이머 제거 타이머 함수
  - `clearTimeout`
  - `clearInterval`



#### 타이머 함수의 특징

- `setTimeout`, `setInterval`은 모두 일정 시간이 경과된 후 콜백 함수가 호출되도록 타이머를 생성
- 즉, 타이머함수가 생성한 타이머가 만료시 콜백함수 호출

- `setTimeout`
  - 단 한번 동작
  - 콜백함수 타이머가 만료되면 단 한번 호출
- `setInterval`
  - 반복해서 동작
  - 콜백함수 타이머가 만료될 때마다 반복 호출



#### 자바스크립트 엔진의 특징

- 두가지 이상의 태스크를 동시 실행할 수 없음
  - 자바스크립트 엔진은 단 하나의 실행 컨텍스트 스택을 가지기 때문
- 싱글 스레드로 동작
- `setTimeout`, `setInterval`은 비동기 처리 방식으로 동작





## 41.2 타이머 함수

### 41.2.1 `setTimeout` / `clearTimeout`

#### `setTimeout` 특징

- 두 번째 인수로 전달받은 시간으로 단 한번 동작하는 타이머 생성

- 타이머 만료시 첫 번째 인수로 전달받은 콜백 함수가 호출

```javascript
setTimeout(func|code[, delay, param1, param2, ...])
```

- Func
  - 타이머가 만료된 뒤 호출된 콜백함수
- delay
  - 타이머 만료시간(ms)
  - 인수 전달 생략시 기본값 0
  - delay가 4ms 이하인 경우, 최소 지연 시간 4ms가 지정된다
- param1, param2, ...
  - 호출 스케줄링된 콜백 함수에 전달해야 할 인수가 존재하는 경우 세 번째 이후의 인수로 전달 가능

```javascript
// 1초(1000ms) 후 타이머가 만료되면 콜백 함수가 호출된다.
setTimeout(() => console.log('Hi!'), 1000);

// 1초(1000ms) 후 타이머가 만료되면 콜백 함수가 호출됨
// 이 때, 콜백 함수에 'Lee'가 인수로 전될됨
setTimeout(name => console.log(`Hi! ${name}.`), 1000, 'Lee');

// 두 번째 인수(delay)를 생략하면 기본값 0이 지정된다
setTimeout(() => console.log('Hello!'));
```

- `setTimeout` 함수는 생성된 타이머를 식별할 수 있는 고유한 타이머 id를 반환
- 타이머 id
  - 브라우저 환경인 경우 숫자, node.js 환경인 경우 객체
  - `clearTimeout` 함수의 인수로 전달하면 타이머를 취소할 수 있다.



#### `clearTimeout`

```javascript
// 1초 후 타이머가 만료되면 콜백 함수가 호출
// setTimeout 함수는 생성된 타이머를 식별할 수 있는 고유한 타이머 id를 반환
const timerId = setTimeout(() => console.log('Hi!'), 1000);

// setTimeout 함수가 반환한 타이머id를 clearTimeout 함수의 인수로 전달하여 타이머 취소
// 타이머가 취소되면 setTimeout 함수의 콜백 함수가 실행되지 않음
clearTimeout(timerId)
```



### 41.2.2 `setInterval` / `clearInterval`

#### `setInterval` 특징

- 두 번째 인수로 전달받은 시간(ms)으로 반복 동작하는 타이머 생성
- 타이머 만료시 첫 번째 인수로 전달받은 콜백 함수가 반복 호출
- 타이머가 취소될 때까지 계속

```javascript
const timerId = setInterval(func|code[, delay, param1, param2, ...]);
```

- `setInterval` 함수는 생성된 타이머를 식별할 수 있는 고유한 타이머 id를 반환
- 타이머 id
  - 브라우저 환경인 경우 숫자, node.js 환경인 경우 객체
  - `clearInterval` 함수의 인수로 전달하면 타이머를 취소할 수 있다.



#### `clearInterval`

```javascript
let count = 1;

// 1초 후 타이머가 만료될 때마다 콜백 함수가 호출됨
// setInterval 함수는 생성된 타이머를 식별할 수 있는 고유한 타이머 id를 반환
const timeoutId = setInterval(() => {
  console.log(count); // 1 2 3 4 5
  
  // count가 5면 setInterval 함수가 반환한 타이머 id를 clearInterval 함수의 인수로 전달하여 타이머 취소
  // 타이머가 취소되면 setInterval 함수의 콜백함수가 실행되지 않는다
  if ( count++ === 5 ) clearInterval(timeoutId);
}, 1000);
```





## 41.3 디바운스와 스로틀

> 디바운스와 스로틀은 짧은 시간 간격으로 연속으로 발생하는 이벤트를 **그룹화**해서 **과도한 이벤트 핸들러의 호출을 방지**하는 프로그래밍 기법

- `scroll`, `resize`, `input`, `mousemove`와 같은 이벤트는 짧은 시간 간격으로 연속해서 발생. 이러한 이벤트에 바인딩한 이벤트 핸들러는 과도하게 호출되어 성능문제 일으킬 가능성이 있음
  - 이럴 때 디바운스와 스로틀 사용해서 과도한 호출 방지

- 디바운스와 스로틀 구현에는 타이머 함수가 사용됨



### 41.3.1 디바운스(debounce)

> 짧은 시간 간격으로 이벤트가 연속해서 발생하면 이벤트 핸들러를 호출하지 않다가 **일정 시간이 경과한 후** 이벤트 핸들러가 한 번만 호출되도록 함
>
> 즉, 짧은 시간 간격으로 발생하는 이벤트를 그룹화해서 **마지막에 한 번만** 이벤트 핸들러가 호출되도록 함

### 예시

**텍스트 입력 필드에서 `input` 이벤트가 짧은 시간 간격으로 연속해서 발생하는 경우**

```javascript
<!DOCTYPE html> 
  <html>
  <body>
  	<input type="text"> 
  	<div class="msg"></div>
		<script>
      const $input = document.querySelector('input');
			const $msg = document.querySelector('.msg');
			const debounce = (callback, delay) => { 
        let timerId;
        // debounce함수는 timerId를 기억하는 클로저를 반환한다.
        return event => {
          // delay보다 빠르게 이벤트가 발생하면 이전 타이머를 취소하고 새로운 타이머를 재설정한다. 
          // 따라서 delay보다 짧은 간격으로 이벤트가 발생하면 callback은 호출되지 않는다.
          if (timerId) clearTimeout(timerId);
          timerId = setTimeout(callback, delay, event);
    };
	};

	// debounce함수가 반환하는 클로저가 이벤트 핸들러로 등록된다.
	// 30ms보다 빠르게 input 이벤트가 발생하면 debounce함수의 콜백함수는 호출되지 않다가
	// 300ms동안 input 이벤트가 더 이상 발생하지않으면 한 번만 호출된다.
	$input.oninput = debounce(e => {
    $msg.textConetnt = e.target.value;
  }, 300);
	</script>
</body>
</html>
```

- `input` 이벤트는 텍스트 입력 필드에 값을 입력할 때마다 연속해서 발생
  - 서버에 부담을 주는 불필요한 처리
  - 사용자가 입력을 완료했을 때 한 번만 Ajax 요청을 전송하는 것이 바람직
- 일정 시간 동안 텍스트 입력 필드에 값을 입력하지 않으면 입력이 완료된 것으로 간주
  - debounce 함수가 반환한 함수는 debounce 함수에 두번째 인수로 전달한 시간(`delay`)보다 짧은 간격으로 이벤트가 발생하면, 이전 타이머를 취소하고 새로운 타이머를 재설정
    - delay보다 빠르게 이벤트가 발생하면, 계속
    - `delay` 보다 느리게 이벤트가 발생하면 한 번만 호출
- 사용례
  - `resize` 이벤트 처리
  - input 요소에 입력된 값으로 `ajax` 요청하는 입력 필드 자동완성 UI 구현
  - 버튼 중복 클릭 방지 처리
  - https://webclub.tistory.com/607
- 실무에서는 Underscore의  throttle 함수나 Lodash 사용 권장



### 41.3.2 스로틀(throttle)

> 짧은 시간 간격으로 이벤트가 연속해서 발생해도, **일정 시간 간격으로 이벤트 핸들러가 최대 한 번만 호출**되도록 한다.
>
> 즉, 짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화해서 **일정 시간 단위로** 이벤트 핸들러가 호출되도록 **호출 주기**를 만든다. 실행 횟수에 제한을 두는 것.

### 예시

**`scroll` 이벤트가 짧은 시간 간격으로 연속해서 발생하는 경우**

```javascript
<!DOCTYPE html>
<html>
<head>
  <style>
  	.container {
      width: 300px;
      height: 300px;
      background-color: rebeccapurple;
      overflow: scroll;
    }

		.content {
      width: 300px;
      height: 1000vh;
    }
	</style>
</head>
<body>
	<div class="container">
    <div class="content"></div>
	</div>
	<div>
      일반 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
			<span class="normal-count">0</span>
	</div>
	<div>
      스로틀 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
			<span class="throttle-count">0</span>
	</div>

  <script>
    const $container = document.querySelector('.container');
    const $normalCount = document.querySelector('.normal-count');
    const $throttleCount = document.querySelector('.throttle-count');

    const throttle = (callback, delay) => {
      let timerId;
      // throttle 함수는 timerId를 기억하는 클로저를 반환
      return event => {
        if (timerId) return;
        timerId = setTimeout(() => {
          callback(event);
          timerId = null;
        }, delay, event);
      };
    };

    let normalCount = 0;
    $container.addEventListener('scroll', () => {
      $normalCount.textContent = ++normalCount;
    });

    let throttleCount = 0;
    // throttle 함수가 반환하는 클로저가 이벤트 핸들러로 등록하다
    $container.addEventListener('scroll', throttle(() => {
      $throttleCount.textContent = ++throttleCount;
    }, 100));
  </script>
</body>
</html>
```

- `scroll` 이벤트는 사용자가 스크롤 할 때 짧은 시간 간격으로 연속해서 발생
  - `throttle` 함수는 이벤트를 그룹화해서 **일정 시간 단위로 호출 주기** 만듦
- delay 시간 간격으로 호출 주기 만듦
  - delay 보다 빠르게 이벤트가 발생하면 아무것도 하지 않음
  - delay 보다 느리게 이벤트가 발생하면 콜백 함수를 호출하고, 새로운 타이머 재설정
- 사용례
  - `scroll` 이벤트 처리
  - 무한스크롤 UI 구현
- 실무에서는 Underscore의  throttle 함수나 Lodash 사용 권장
