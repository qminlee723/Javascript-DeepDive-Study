- 자바스크입트의 비동기 처리를 위한 해결 패턴으로 콜백함수를 사용.
- 하지만 전통적인 콜백 패턴은 콜백헬로 인해 가독성이 안좋고, 비동기 처리 중 발생 한 에러 처리가 곤란함.
- 이를 해결하기 위해 ES6에서 비동기 처리를 위한 또다른 패턴으로 프로미스를 도입.

## 45.1 비동기 처리를 위한 콜백 패턴의 단점

### 콜백 헬

- 처리 순서를 보장하기 위해 여러 개의 콜백 함수가 네스팅(nesting, 중첩)되어 복잡도가 높아지는 것
- 비동기 처리 모델은 실행 완료를 기다리지 않고 즉시 다음 태스크를 실행하므로, 비동기 함수내에서 처리 결과를 반환(또는 전역 변수에의 할당)하면 원하는대로 작동하지 않을 것임.

  ```jsx
  <!DOCTYPE html>
  <html>
  <body>
    <script>
      // 비동기 함수
      function get(url) {
        // XMLHttpRequest 객체 생성
        const xhr = new XMLHttpRequest();

        // 서버 응답 시 호출될 이벤트 핸들러
        xhr.onreadystatechange = function () {
          // 서버 응답 완료가 아니면 무시
          if (xhr.readyState !== XMLHttpRequest.DONE) return;

          if (xhr.status === 200) { // 정상 응답
            console.log(xhr.response);
            // 비동기 함수의 결과에 대한 처리는 반환할 수 없다.
            return xhr.response; // ①
          } else { // 비정상 응답
            console.log('Error: ' + xhr.status);
          }
        };

        // 비동기 방식으로 Request 오픈
        xhr.open('GET', url);
        // Request 전송
        xhr.send();
      }

      // 비동기 함수 내의 readystatechange 이벤트 핸들러에서 처리 결과를 반환(①)하면 순서가 보장되지 않는다.
      const res = get('http://jsonplaceholder.typicode.com/posts/1');
      console.log(res); // ② undefined
    </script>
  </body>
  </html>

  ```

- 즉, 비동기 함수의 처리 결과를 반환하는 경우, 순서가 보장되지 않기 때문에 그 반환 결과를 가지고 후속 처리를 할 수 없다. 즉, 비동기 함수의 처리 결과에 대한 처리는 비동기 함수의 콜백 함수 내에서 처리해야 함.

### 에러 처리의 한계

- 콜백패턴에서 가장 심각한 문제는 에러처리가 곤란하다는 것이다.
  ```jsx
  try {
    setTimeout(() => {
      throw new Error("Error!");
    }, 1000);
  } catch (e) {
    console.log("에러를 캐치하지 못한다..");
    console.log(e);
  }
  ```
  - setTimeout은 비동기 함수이고, 비동기함수의 콜백함수는 이벤트가 발생되는 시점에 태스크큐로 바로 이동 후 호출스택이 비워졌을 때, 호출스택이 비워지면이동되어 실행된다.
  - setTimeout은 비동기 함수이기때문에 콜백함수가 실행될 때까지 기다리지 않고 호출스택에서 제거된다.
  - error는 호출자 방향으로 전파되므로(스택에서 하단 방향으로) setTimeout의 콜백함수에서 발생시킨 에러는 캐치되지 않는다.

## 45.2 프로미스의 생성

- 프로미스는 Promise 생성자 함수를 통해 인스턴스화한다. Promise 생성자 함수는 비동기 작업을 수행할 콜백 함수를 인자로 전달받는데 이 콜백 함수는 resolve와 reject 함수를 인자로 전달받는다.

  ```jsx
  // Promise 객체의 생성
  const promise = new Promise((resolve, reject) => {
    // 비동기 작업을 수행한다.

    if (/* 비동기 작업 수행 성공 */) {
      resolve('result');
    }
    else { /* 비동기 작업 수행 실패 */
      reject('failure reason');
    }
  });
  ```

- 프로미스는 다음과 같은 비동기처리가 어떻게 진행되고 있는지를 나타내는 상태값을 가진다.

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/48cdedce-03df-4cf1-81ca-1e6c61b28794/Untitled.png)

- 프로미스는 위 진행상태를 나타내는 상태값과, 결과값 상태또한 가지고 있다.
  - fulfilled되면 비동기 처리결과를 값으로 갖게됨
  - rejected되면 Error 객체를 값으로 가짐
    ⇒ 즉, 프로미스는 비동기 처리상태와 처리 결과를 관리하는 객체이다.

## 45.3 프로미스의 후속 처리 메서드

### Promise.prototype.then

- then 메소드는 두 개의 콜백 함수를 인자로 전달 받는다. 첫 번째 콜백 함수는 성공(fulfilled, resolve 함수가 호출된 상태) 시 호출되고 두 번째 함수는 실패(rejected, reject 함수가 호출된 상태) 시 호출된다. **then 메소드는 Promise를 반환한다.**
  ```jsx
  /*
        비동기 함수 promiseAjax은 Promise 객체를 반환한다.
        Promise 객체의 후속 메소드를 사용하여 비동기 처리 결과에 대한 후속 처리를 수행한다.
      */
  promiseAjax("GET", "http://jsonplaceholder.typicode.com/posts/1")
    .then(JSON.parse)
    .then(
      // 첫 번째 콜백 함수는 성공(fulfilled, resolve 함수가 호출된 상태) 시 호출된다.
      render,
      // 두 번째 함수는 실패(rejected, reject 함수가 호출된 상태) 시 호출된다.
      console.error
    );
  ```

### Promise.prototype.catch

- 예외(비동기 처리에서 발생한 에러와 then 메소드에서 발생한 에러)가 발생하면 호출된다. **catch 메소드는 Promise를 반환한다.**

### Promise.prototype.finally

- 프로미스의 성공 실패 상관없이 무조건 한번호출. 공통적으로 처리해야할 내용있을 때 유용. **finally 메소드는 Promise를 반환한다.**

## 45.4 프로미스의 에러 처리

- then이나 catch 이용하여 에러처리
- then은 then 안쪽 두번 째 콜백을 이용해야해서 가독성 안좋고, 첫번째 콜백의 오류 잡아내지 못하는 문제 있음
- chtch 사용할 것

## 45.5 프로미스 체이닝

- 위에서 언급한 세가지 후속처리 메서드는 전부 프로미스를 반환한다.
- 따라서 콜백함수 안에 콜백함수를 쓰지않고 후속처리 메서드를 이어서 사용할 수 있음
  ```jsx
  promiseAjax("GET", `${url}/1`)
    // 포스트 id가 1인 포스트를 작성한 사용자의 아이디로 작성된 모든 포스트를 검색하고 프로미스를 반환한다.
    .then((res) =>
      promiseAjax("GET", `${url}?userId=${JSON.parse(res).userId}`)
    )
    .then(JSON.parse)
    .then(render)
    .catch(console.error);
  ```
- 어쨌든 프로미스도 콜백함수를 사용하고 있음. ES8에서 나온 await async를 이용하면 마치 동기함수처럼 후속처리 메서드없이 프로미스가 처리값을 반환하도록 할 수 있다.

## 45.6 프로미스의 정적 메서드

### Promise.resolve / Promise.reject

- Promise.resolve와 Promise.reject 메소드는 존재하는 값을 Promise로 래핑하기 위해 사용한다.
- 정적 메소드 Promise.resolve 메소드는 인자로 전달된 값을 resolve하는 Promise를 생성한다.

  ```jsx
  const resolvedPromise = Promise.resolve([1, 2, 3]);
  resolvedPromise.then(console.log); // [ 1, 2, 3 ]

  const resolvedPromise = new Promise((resolve) => resolve([1, 2, 3]));
  resolvedPromise.then(console.log); // [ 1, 2, 3 ]
  ```

### Promise.all

- Promise.all 메소드는 프로미스가 담겨 있는 배열 등의 이터러블을 인자로 전달 받는다.
- 전달받은 모든 프로미스를 병렬로 처리하고 그 처리 결과를 resolve하는 새로운 프로미스를 반환한다.
  ```jsx
  Promise.all([
    new Promise((resolve) => setTimeout(() => resolve(1), 3000)), // 1
    new Promise((resolve) => setTimeout(() => resolve(2), 2000)), // 2
    new Promise((resolve) => setTimeout(() => resolve(3), 1000)), // 3
  ])
    .then(console.log) // [ 1, 2, 3 ]
    .catch(console.log);
  ```

### Promise.race

- Promise.race 메소드는 Promise.all 메소드와 동일하게 프로미스가 담겨 있는 배열 등의 이터러블을 인자로 전달 받는다.
- Promise.all 메소드처럼 모든 프로미스를 병렬 처리하는 것이 아니라 가장 먼저 처리된 프로미스가 resolve한 처리 결과를 resolve하는 새로운 프로미스를 반환한다.
  ```jsx
  Promise.race([
    new Promise((resolve) => setTimeout(() => resolve(1), 3000)), // 1
    new Promise((resolve) => setTimeout(() => resolve(2), 2000)), // 2
    new Promise((resolve) => setTimeout(() => resolve(3), 1000)), // 3
  ])
    .then(console.log) // 3
    .catch(console.log);
  ```

### Promise.allSettled

- Promise.allSettled 메소드는 프로미스가 담겨 있는 배열 등의 이터러블을 인자로 전달 받는다.
- 전달받은 프로미스가 모두 settled상태가 되면 fullfilled인지 reject인지 처리결과를 배열로 반환

  ```jsx
  Promise.allSettled([
    new Promise((resolve) => setTimeout(() => resolve(1), 3000)), // 1
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Error!")), 2000)
    ), // 2
  ]).then(console.log);

  /*
  [
  	{status: "fulfilled", value: 1},
  	{status: "rejected", reason: Error: Error!}
  ]
  */
  ```

## 46.7 마이크로태스크 큐

```jsx
setTimeoust(()=>console.log(1),0);

Promise.resolve()
  .then(() => console.log(2)
  .then(() => console.log(3);
```

- 프로미스의 후속처리 메서드도 비동기로 작동하므로 1-2-3일 것같지만 2-3-1임
- 프로미스 후속처리 메서드는 태스크큐가 아니라 마이크로태스크 큐에 저장되기 때문
- 마이크로 태스크큐가 우선순위가 더 높음
- 즉, 콜스택이 비면 마이크로 태스크 큐에 있는 애들 먼저 실행되고, 태스크큐에 있는 것들 실행됨

## 46.8 fetch

- fetch가 반환하는 프로미스는 404 Not Found 나 500 Internal Server Error와 같은 HTTP에러가 발생해도 에러를 reject하지 않고 불리언타입의 ok 상태를 false로 설정한 response를 반환한다.
- 오프라인 등의 네트워크 장애나 cors 에서에 의해 요청이 완료되지 못한 경우만 프로미스를 reject 한다.
- 따라서 에러처리 시, fetch 가 반환한 프로미스가 resolve한 불리언타입의 ok상태를 확인하여 에러 처리를 해주어야한다.
