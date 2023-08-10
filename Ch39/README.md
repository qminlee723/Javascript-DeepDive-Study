# 39장 DOM

`**DOM`은 `HTML` 문서의 계층적 구조와 정보를 표현하며 이를 제어할 수 있는 `API`, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조이다**

# 39-1. 노드

HTML 요소는 HTML 문서를 구성하는 개별적인 요소를 의미한다.

![https://k.kakaocdn.net/dn/bAerqp/btrvkGjtEy8/NlEaA4Ad3KTdWN2tuPKHXk/img.png](https://k.kakaocdn.net/dn/bAerqp/btrvkGjtEy8/NlEaA4Ad3KTdWN2tuPKHXk/img.png)

HTML 요소는 렌더링 엔진에 의해 파싱되어 DOM을 구성하는 요소 노드 객체로 변환된다.



트리 자료구조는 하나의 최상위 노드에서 시작한다. 최상위 노드는 부모 노드가 없으며, 루트 노드라 한다. 자식 노드가 없는 노드를 리프 노드라 한다.

**노드 객체들로 구성된 트리 자료구조를 DOM이라 한다. 노드 객체의 트리로 구조화되어 있기 때문에 DOM을 DOM 트리라고 부르기도 한다.**

## **노드 객체의 타입**

- 문서 노드 (document node)
DOM 트리의 최상단에 존재하는 루트노드, document 객체를 가르킨다 document 객체는 브라우저가 렌더링한 HTML 문서 전체를 가르킴, 전역 객체의 document 프로퍼티에 바인딩되어 있다
- 요소 노드 (element node)
HTML 요소를 가르키는 객체HTML 요소들의 중첩관계를 통해 문서의 구조를 표현한다
- 어트리뷰트 노드 (attribute node)
HTML 요소의 어트리뷰트를 가르키는 객체 어트리뷰트 노드는 해당 요소 노드에만 연결되어 있다
- 텍스트 노드 (text node)
HTML 요소의 텍스트를 가르키는 객체 해당 요소 노드의 자식 노드이며 말단 노드임

## 노드 객체의 상속 구조

DOM은 DOM을 제어할 수 있는 API, 즉 프로퍼티와 메서드를 제공하는 트리 자료구조이다.

DOM을 구성하는 노드 객체는 브라우저 환경에서 제공하는 호스트 객체다.

노드 객체도 자바스크립트 객체이므로 프로토타입에 의한 상속 구조를 갖는다. 노드 객체의 상속 구조는 다음과 같다.

![https://k.kakaocdn.net/dn/bICiGX/btrvl8UlGyc/J1Y52ENcKGLDfAJEiCPGkK/img.png](https://k.kakaocdn.net/dn/bICiGX/btrvl8UlGyc/J1Y52ENcKGLDfAJEiCPGkK/img.png)

노드 객체는 Object, EventTarget, Node 인터페이스를 상속받는다.

예를들어 input 요소 노드 객체를 보자. input 요소 노드 객체는 HTMLInputElement, HTMLEIement, Element, Node, EventTarget, Object의 prototype에 바인딩되어 있는 프로토타입 객체를 상속받는다.

즉 input 요소 노드 객체는 프로토타입 체인에 있는 모든 프로토타입의 프로퍼티나 메서드를 상속 받아 사용할 수 있다.



```jsx
<!DOCTYPE html>
<html>
<body>
  <input type="text">
  <script>
    // input 요소 노드 객체를 선택
    const $input = document.querySelector('input');

    // input 요소 노드 객체의 프로토타입 체인
    console.log(
      Object.getPrototypeOf($input) === HTMLInputElement.prototype,
      Object.getPrototypeOf(HTMLInputElement.prototype) === HTMLElement.prototype,
      Object.getPrototypeOf(HTMLElement.prototype) === Element.prototype,
      Object.getPrototypeOf(Element.prototype) === Node.prototype,
      Object.getPrototypeOf(Node.prototype) === EventTarget.prototype,
      Object.getPrototypeOf(EventTarget.prototype) === Object.prototype
    ); // 모두 true
  </script>
</body>
</html>
```

**정리하자면 DOM은 HTML 문서의 계층적 구조와 정보를 표현하는 것은 물론 노드 객체의 종류, 즉 노드 타입에 따라 필요한 기능을 프로퍼티와 메서드의 집합인 DOM API로 제공한다. 이 DOM API를 통해 HTML의 구조나 내용 또는 스타일 등을 동적으로 조작할 수 있다.**

# 39-2. 요소 노드 취득

HTML의 구조나 내용 또는 스타일 등을 동적으로 조작하려면 먼저 요소 노드를 취득해야 한다.

DOM은 요소 노드를 취득할 수 있는 다양한 메서드를 제공한다.

## **id를 이용한 요소 노드 취득**

Document.prototype.getElementById 메서드는 인수로 전달한 id 어트리뷰트 값을 갖는 하나의 요소 노드를 탐색하여 반환한다. id값은 HTML 문서 내에서 유일한 값이어야 하지만, 여러 개 존재하더라도 에러가 발생하지 않기 때문에 관리를 잘 해야한다.

인수로 전달된 id값을 갖는 첫 번째 요소 노드가 반환한다. (id 값이 중복되더라도 첫 번째 id값만 반환) 요소가 존재하지 않는 경우 null을 반환한다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <ul>
      <li id="apple">Apple</li>
      <li id="banana">Banana</li>
      <li id="orange">Orange</li>
    </ul>
    <script>
      // id 값이 'banana'인 요소 노드를 탐색하여 반환한다.
      // 두 번째 li 요소가 파싱되어 생성된 요소 노드가 반환된다.
      const $elem = document.getElementById('banana');

      // 취득한 요소 노드의 style.color 프로퍼티 값을 변경한다.
      $elem.style.color = 'red';
    </script>
  </body>
</html>
```

## ****HTMLCollection과 NodeList****

DOM 컬렉션 객체인 HTMLCollection과 NodeList는 DOM API가 여러 개의 결과값을 반환하기 위한

DOM 컬렉션 객체이다. HTMLCollection과 NodeList는 모두 유사 배열 객체이면서 이터러블이다.

따라서 for...of 문으로 순회할 수 있으며 스프레드 문법을 사용하여 간단히 배열로 변환할 수 있다.

**HTMLCollection과 NodeList의 중요한 특징은 노드 객체의 상태 변화를 실시간으로 반영하여**

**살아 있는 객체**라는 것이다.

HTMLCollection은 언제나 live 객체로 동작한다. NodeList는 특정 경우에만 live 객체로 동작한다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // childNodes 프로퍼티는 NodeList 객체(live)를 반환한다.
    const { childNodes } = $fruits;
    console.log(childNodes instanceof NodeList); // true

    // $fruits 요소의 자식 노드는 공백 텍스트 노드(39.3.1절 "공백 텍스트 노드" 참고)를 포함해 모두 5개다.
    console.log(childNodes); // NodeList(5) [text, li, text, li, text]

    for (let i = 0; i < childNodes.length; i++) {
      // removeChild 메서드는 $fruits 요소의 자식 노드를 DOM에서 삭제한다.
      // (39.6.9절 "노드 삭제" 참고)
      // removeChild 메서드가 호출될 때마다 NodeList 객체인 childNodes가 실시간으로 변경된다.
      // 따라서 첫 번째, 세 번째 다섯 번째 요소만 삭제된다.
      $fruits.removeChild(childNodes[i]);
    }

    // 예상과 다르게 $fruits 요소의 모든 자식 노드가 삭제되지 않는다.
    console.log(childNodes); // NodeList(2) [li, li]
  </script>
</html>
```

따라서 노드 객체의 상태 변경과 상관없이 안전하게 DOM 컬렉션을 사용하려면 HTMLCollection이나 NodeList 객체를 배열로 변환하여 사용하는 것을 권장한다. 또한 고차 함수를 사용하여 순회한다.

# 39-3. 노드 탐색

## 공백 텍스트 노드

`HTML` 요소 사이의 스페이스, 탭, 줄바꿈(개행) 등의 공백 문자는 텍스트 노드를 생성한다. 이를 공백 텍스트 노드라 한다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <ul>
      <li id="apple">Apple</li>
      <li id="banana">Banana</li>
      <li id="orange">Orange</li>
    </ul>
  </body>
</html>
```

위 HTML 문서에도 공백 문서가 포함되어 있다. 따라서 노드를 탐색할 때는 공백 문자가 생성한 공백 텍스트 노드에 주의해야 한다. 인위적으로 `HTML` 문서의 공백 문자를 제거하면 공백 텍스트 노드를 생성하지 않지만, 가독성이 좋지 않으므로 권장하지 않는다.

# 39-4. 노드 정보 취득

노드 객체에 대한 정보를 취득하려면 다음과 같은 노드 정보 프로퍼티를 사용한다.

| 프로퍼티 | 설명 |
| --- | --- |
| Node.prototype.nodeType | 노드 객체의 종류, 즉 노드 타입을 나타내는 상수를 반환한다. 노드 타입 상수는 Node에 정의되어 있다.
- Node.ELEMENT_NODE: 요소 노드 타입을 나타내는 상수 1을 반환
- Node.TEXT_NODE: 텍스트 노드 타입을 나타내는 상수 3을 반환
- Node.DOCUMENT_NODE: 문서 노드 타입을 나타내는 상수 9를 반환 |
| Node.prototype.nodeName | 노드의 이름을 문자열로 반환한다.
- 요소 노드: 대문자 문자열로 태그 이름("UL", "LI"등)을 반환
- 텍스트 노드: 문자열 "#text"를 반환
- 문서 노드: 문자열 "#document"를 반환 |

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 문서 노드의 노드 정보를 취득한다.
    console.log(document.nodeType); // 9
    console.log(document.nodeName); // #document

    // 요소 노드의 노드 정보를 취득한다.
    const $foo = document.getElementById("foo");
    console.log($foo.nodeType); // 1
    console.log($foo.nodeName); // DIV

    // 텍스트 노드의 노드 정보를 취득한다.
    const $textNode = $foo.firstChild;
    console.log($textNode.nodeType); // 3
    console.log($textNode.nodeName); // #text
  </script>
</html>
```

# 39-5. 요소 노드의 텍스트 조작

## nodeValue

지금까지 살펴본 노드 탐색, 노드 정보 프로퍼티는 모두 읽기 전용 접근자 프로퍼티다. 지금부터 살펴볼 `Node.prototype.nodeValue` 프로퍼티는 `setter`와 `getter` 모두 존재하는 접근자 프로퍼티다. 따라서 `nodeValue` 프로퍼티는 참조와 할당 모두 가능하다.

노드 객체의 `nodeValue` 프로퍼티를 참조하면 노드 객체의 값을 반환한다. 노드 객체의 값이란 텍스트 노드의 텍스트다. 따라서 텍스트 노드가 아닌 노드, 즉 문서 노드나 요소 노드의 `nodeValue` 프로퍼티를 참조하면 `null`을 반환한다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 문서 노드의 nodeValue 프로퍼티를 참조한다.
    console.log(document.nodeValue); // null

    // 요소 노드의 nodeValue 프로퍼티를 참조한다.
    const $foo = document.getElementById("foo");
    console.log($foo.nodeValue); // null

    // 텍스트 노드의 nodeValue 프로퍼티를 참조한다.
    const $textNode = $foo.firstChild;
    console.log($textNode.nodeValue); // Hello
  </script>
</html>
```

## textContent

`Node.prototype.textContent` 프로퍼티는 `setter`와 `getter` 모두 존재하는 접근자 프로퍼티로서 요소 노드의 텍스트와 모든 자손 노드의 텍스트를 모두 취득하거나 변경한다.

요소 노드의 `textContent` 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역 내의 텍스트를 모두 반환한다. 다시 말해, 요소 노드의 `childNodes` 프로퍼티가 반환한 모든 노드들의 텍스트 노드의 값, 즉 텍스트를 모두 반환한다. 이때 `HTML` 마크업은 무시된다.

자식 요소 노드가 없고 텍스트만 존재한다면 `nodeValue`와 `textContext` 프로퍼티는 같은 결과를 반환한다.

```jsx
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
		console.log(document.getElementById("foo").nodeValue); // null
		console.log(document.getElementById("foo").firstChild.nodeValue); // Hello

		// #foo 요소 노드의 텍스트를 모두 취득한다. 이때 HTML 마크업은 무시된다.
    console.log(document.getElementById("foo").textContent); // Hello world!
  </script>
</html>
```

# 39-7. 어트리뷰트

## 어트리뷰트 노드와 attributes 프로퍼티

`HTML` 문서의 구성 요소인 `HTML` 요소는 여러 개의 어트리뷰트(속성)를 가질 수 있다. `HTML` 요소의 동작을 제어하기 위한 추가적인 정보를 제공하는 `HTML` 어트리뷰트는 `HTML` 요소의 시작 태그에 `어트리뷰트 이름="어트리뷰트 값"`형식으로 정의한다.

```jsx
<input id="user" type="text" value="kyeom">
```

`HTML` 문서가 파싱될 때 `HTML` 요소의 어트리뷰트(이하 `HTML` 어트리뷰트)는 어트리뷰트 노드로 변환되어 요소 노드와 연결된다. 이때 `HTML` 어트리뷰트당 하나의 어트리뷰트 노드가 생성된다. 즉, 위 `input` 요소는 3개의 어트리뷰트가 있으므로 3개의 어트리뷰트 노드가 생성된다.

## **HTML 어트리뷰트 vs DOM 프로퍼티**

**HTML 어트리뷰트의 역할은 HTML 요소의 초기 상태를 지정하는 것이다. 즉, HTML 어트리뷰트 값은 HTML 요소의 초기 상태를 의미하며 이는 변하지 않는다.**

```jsx
<!DOCTYPE html>
<html>
  <body>
    <input id="user" type="text" value="kyeom" />
    <script>
      const $input = document.getElementById("user");

      // attributes 프로퍼티에 저장된 value 어트리뷰트 값
      console.log($input.getAttribute("value")); // kyeom

      // 요소 노드의 value 프로퍼티에 저장된 value 어트리뷰트 값
      console.log($input.value); // kyeom
    </script>
  </body>
</html>
```

**요소 노드는 상태(State)를 가지고 있다.** 

사용자가 `input` 요소의 입력 필드에 "foo"라는 값을 입력한 경우를 생각해보자. 이때 `input` 요소 노드는 사용자의 입력에 의해 변경된 **최신 상태("foo")**를 관리해야 하는 것은 물론, `HTML` 어트리뷰트로 지정한 **초기 상태("kyeom")**도 관리해야 한다. 초기 상태 값을 관리하지 않으면 웹 페이지를 처음 표시하거나 새로고침할 때 초기 상태를 표시할 수 없다.

이처럼 **요소 노드는 2개의 상태, 즉 초기 상태와 최신 상태를 관리해야 한다. 요소 노드의 초기 상태는 어트리뷰트 노드가 관리하며, 요소 노드의 최신 상태는 DOM 프로퍼티가 관리한다.**

## **어트리뷰트 노드**

**HTML 어트리뷰트로 지정한 HTML 요소의 초기 상태는 어트리뷰트 노드에서 관리한다.**
어트리뷰트 노드에서 관리하는 어트리뷰트 값은 사용자의 입력에 의해 상태가 변경되어도 변하지 않고 `HTML` 어트리뷰트로 지정한 `HTML` 요소의 초기 상태를 그대로 유지한다.

## **DOM 프로퍼티**

**사용자가 입력한 최신 상태는 HTML 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티가 관리한다. DOM 프로퍼티는 사용자의 입력에 의한 상태 변화에 반응하여 언제나 최신 상태를 유지한다.**
