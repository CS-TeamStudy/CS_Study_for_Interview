## 전역 변수 (global variable)

전역변수란 어디에서든 접근할 수 있는 변수를 의미합니다.<br />
자바스크립트는 기본적으로 함수 레벨 스코프(Function-level Scope)를 가지는 언어이기 때문에<br />
함수 바깥에서 선언된 변수는 모두 전역 변수로 취급됩니다.

<br />

### 전역 변수 선언 방법

1. **선언하지 않은 채로 사용**

```jsx
apple = '사과';

console.log(apple);          // 사과
console.log(window.apple);   // 사과
```

```jsx
apple = '사과';          // 함수 바깥에서 선언없이 사용 = 전역변수

function test() {
  console.log(apple);
  banana = '바나나';     // 함수 내부지만 선언없이 사용 = 전역변수
}

console.log(apple);    // 사과
test();                // 사과
console.log(banana);   // 바나나 (함수 호출로 인해 전역변수가 만들어져 정상적으로 출력!)
```

전역 변수는 전역 객체(global object)의 프로퍼티(property)이기 때문에 `window` 객체에서 찾을 수 있습니다.

![image](https://github.com/dev-junehee/front-interview-questions-archive/assets/116873887/89fd199f-13bc-44f7-8e1c-8d20867c8dbd)


<br />

2. **`var` 키워드로 선언할 시 함수 바깥에서 선언**

```jsx
var apple = '사과';

function test() {
  var banana = '바나나';
}

console.log(apple);    // 사과
console.log(banana);   // ReferenceError: banana is not defined
```

함수 안에서 선언할 경우 변수의 스코프가 함수 내부로 한정되기 때문에 전역 변수로 사용할 수 없습니다.

<br />

3. **`let`, `const` 키워드로 선언할 시 블럭 바깥에서 선언**

```jsx
let apple = '사과';

if (true) {
  let apple = '애플';
}

function test() {
  let banana = '바나나';
  
  if (banana === '바나나') {
      console.log(apple);
      console.log(banana);
  }
}

test();
console.log(apple);
console.log(banana);
```

```jsx
let apple = '사과';

if (true) {
  let apple = '애플';
}

function test() {
  let banana = '바나나';

  if (banana === '바나나') {
      console.log(apple, banana);
  }
}

test();                 // 사과 바나나
console.log(apple);     // 사과
console.log(banana);    // ReferenceError: banana is not defined
```
    
전역변수 `apple`은 어디에서든지 접근 가능하기 때문에 `test()` 내 `if문`에서 출력해도 오류가 나지 않으며, `test()` 안에서 선언된 `banana`의 경우도 `test()` 블록문 내부에서 얼마든지 접근할 수 있습니다. 그러나 `banana`를 블럭문 바깥에서 접근할 경우 접근 가능한 스코프에서 해당 변수를 찾을 수 없기 때문에 참조 에러가 발생하게 됩니다.
    
<br />

### 전역 변수의 문제점

1. **긴 생명 주기 (Life Cycle)**

모든 변수는 생성되고 소멸되는 생명 주기를 가집니다. 만약 생명 주기가 없다면 프로그램을 종료하기 전까지 변수가 메모리 공간을 계속해서 차지하기 때문에 메모리 사용량이 늘어나게 됩니다. 함수 내부에서 선언된 지역 변수의 경우 함수가 호출됨과 동시에 내부 변수가 호이스팅되어 식별자로 등록되고, 이후 값이 할당되어 메모리에 올라가고, 함수가 종료되면 변수도 소멸되어 생명 주기가 종료됩니다. 반면 전역 변수의 경우 명시적인 코드 호출이 없이 스크립트가 로드됨과 동시에 실행되기 때문에 변수가 전역 객체의 프로퍼티가 되어 **전역 객체와 생명 주기가 동일하게 되어 긴 생명 주기**를 찾습니다. 생명 주기가 길면 **메모리 리소스도 오래 소비**하게 됩니다.

2. **암묵적 결합 (Implicit Coupling)**

전역 변수는 코드 어디에서든지 접근 가능하고 변경할 수 있는 암묵적 결합을 허용합니다. 그렇기 때문에 코드 가독성이 나빠지거나 개발자의 의도와는 다르게 변수의 상태가 변경되는 등의 위험성을 가지고 있습니다.

3. **스코프 체인 상에서 마지막 위치에 존재**

스코프 체인에 따라 변수를 검색할 때 전역 객체에 속해있는 전역 변수는 가장 마지막 단계에서 검색되기 때문에 검색 속도가 느립니다.

4. **네임스페이스 오염**

자바스크립트는 스크립트 파일이 분리되어 있어도 하나의 전역 스코프를 공유합니다. 때문에 동일한 이름의 변수나 함수를 여러 스크립트 파일에서 사용할 경우 개발자가 의도한대로 프로그램이 실행되지 않을 가능성이 높아집니다.

<br />

### 전역 변수 사용을 줄이는 방법

1. 전역 변수를 꼭 사용해야 하는 이유가 없다면 지역 변수를 활용하는 것이 좋습니다.
2. 함수 정의와 동시에 한 번만 호출되는 즉시 실행 함수를 활용합니다. 즉시 실행 함수로 감싼 모든 변수는 지역 변수가 됩니다. 이 방법은 플러그인이나 라이브러리를 만들 때 많이 사용됩니다.

```jsx
(function test() {
  var local = '지역변수가 됩니다';
}());

console.log(local);   // ReferenceError: local is not defined
```

3. 네임스페이스 역할을 담당할 객체를 생성하여 활용합니다.

```jsx
var APP_NAME = {
  // ...
}

APP_NAME.apple = '사과';

console.log(APP_NAME.apple);   // 사과
```

4. 클래스를 모방한 모듈 패턴을 만들어 관련 변수와 함수를 모아 관리합니다.

```jsx
var App = (function() {
  // private 변수가 됩니다!
  var x = 10;
  
  // 외부로 공개할 변수나 메서드를 반환합니다
  return {
    toString() {
      return String(x);
    }
  }
}
```

5. ES6 모듈은 파일 자체의 독자적인 모듈 스코프를 제공하기 때문에 전역 변수를 사용할 수 없습니다.

```jsx
<script type="module" src="/main.js"></script>
```
