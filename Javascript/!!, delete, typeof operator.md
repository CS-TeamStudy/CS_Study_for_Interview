## 이중 논리 연산자 (!!, Double Exclamation Mark)

자바스크립트의 이중 논리 연산자는 변수값의 존재 유무를 `boolean`으로 반환합니다.<br />
값이 정의되지 않은 변수를 연산할 때에도 `true/false` 값을 확실하게 가지도록 하는 것이 목적입니다.<br />
즉, 명시적으로 형변환이 필요할 경우 사용할 수 있습니다.

```jsx
var x;

console.log(x);           // undefined
console.log(typeof x);    // undefined
console.log(!!x);         // false
```

| falsy data | !! operator result | truthy data | !! operator result |
| --- | --- | --- | --- |
| null | false | { } | true |
| undefined | false | [ ] | true |
| false | false | true | true |
| NaN | false | “hello” | true |
| 0 | false | “0” | true |
| -0 | false | -42 | true |
| 0n | false | 123456789n | true |
| “” | false | function () { } | true |
| document.all | false | Infinity | true |
|  |  | -Infinity | true |

<br />

`!!` 연산자는 사실상 `Boolean()`과 똑같은 역할을 수행합니다. 그래서 **어떤 것을 사용할지는 개발자가 선택해야 할 몫**입니다.
Airbnb 스타일 가이드에 따르면 `!!` 을 더 좋은 컨벤션이라고 지정하고 있으며, Kyle Simpson의 You don't Know 책에 따르면 두 방법 모두 좋다고 나와있습니다. 성능 측면에서는 사파리에서 `!!` 가 우세, 크롬에서는 `Boolean()`이 우세합니다. 그러므로 **빠르게 입력하느냐, 혹은 더 명확하게 표기하느냐에 따라 개발자 본인이 적절히 선택해서 사용**하면 됩니다.

<br />

## 삭제 연산자 (Delete Operator)

`delete` 삭제 연산자는 **객체에서 속성을 삭제**하는 연산자입니다.<br />
삭제를 성공했을 때 `true`, 실패했을 때 `false`를 반환합니다. (메모리 해제와는 연관X)

```jsx
const fruits = {
    apple: '사과',
    banana: '바나나', 
    cherry: '체리'
}

delete fruits.banana;
// true

console.log(fruits);
// { apple: '사과', cherry: '체리' }
```

<br />

오직 객체의 속성만을 삭제할 수 있기 때문에
변수나 함수 등 다른 식별자를 삭제하려고 할 때 `false` 혹은 엄격 모드에서 `Syntax Error`가 발생합니다.

```jsx
const tvN = 'No.1 K-콘텐츠 채널';
delete tvN;   // false

const sayHello = () => {
    console.log('hello!');
}
delete sayHello;   // false
```

```jsx
'use strict';

const tvN = 'No.1 K-콘텐츠 채널';
delete tvN;   // Syntax Error
```

<br />

그렇다면 아래 콘솔에 찍히는 결과는 어떻게 될까요?

```jsx
const person = {
    name: '이름',
    age: '나이',
    nationality: '국적',
    job: '직업'
}

const family = ['아빠', '엄마', '형제', '자매']

console.log('1', Object.keys(person).length);
console.log('2', family.length);

delete person.nationality;
delete family[2];

console.log('3', Object.keys(person).length);
console.log('4', family.length);
console.log('5', family[2]);
```

<details>
<summary>정답은?</summary>
<div markdown="1">
  
```jsx
const person = {
    name: '이름',
    age: '나이',
    nationality: '국적',
    job: '직업'
}

const family = ['아빠', '엄마', '형제', '자매']

console.log('1', Object.keys(person).length);    // 4
console.log('2', family.length);                 // 4

delete person.nationality;
delete family[2];

console.log('3', Object.keys(person).length);   // 3
console.log('4', family.length);                // 4
console.log('5', family[2]);                    // undefined
```

<img width="400" src="https://github.com/dev-junehee/front-interview-questions-archive/assets/116873887/62edcdb0-4fdb-405f-b85c-577629963574" />

</div>
</details>

<br />

그렇다면 **배열에서 `delete` 사용을 지양**하는 이유가 무엇일까요? 배열만 가지고 다시 테스트 해봅시다.

```jsx
const family = ['아빠', '엄마', '형제', '자매']

console.table(family);
```

<img width="550" src="https://github.com/dev-junehee/front-interview-questions-archive/assets/116873887/68582189-fa35-4db1-b31e-9faf5094e512" />

<br /><br />

```jsx
delete family[2];

console.table(family);
console.log(family.length);
```

<img width="550" src="https://github.com/dev-junehee/front-interview-questions-archive/assets/116873887/0a122586-5556-4f7f-b132-0912c4d87713" />

<br />

인덱스 2의 자리가 사라졌지만 배열의 길이는 그대로입니다.<br />
즉, 배열의 길이보다 원소의 개수가 적은 **희소배열(Sparse Table)** 로 바뀌게 되었습니다.<br />
그리고 이런 형태의 데이터를 사용하게 되면 개발자가 예상하지 못한 사이드 이펙트가 발생할 수 있습니다.<br />

<br />

그래서 배열에서 특정 요소를 삭제하기 위해서는 `splice()`와 같이<br />
배열에서 사용할 수 있는 함수를 사용하는 것이 훨씬 효과적이고 안정적으로 요소를 삭제할 수 있습니다.

```jsx
const family = ['아빠', '엄마', '형제', '자매']

family.splice(2, 1);

console.table(family);
console.log(family.length);
```

<img width="550" src="https://github.com/dev-junehee/front-interview-questions-archive/assets/116873887/7f596cfc-404c-4db6-8415-91b6459899c1" />

<br />
<br />

## typeof

`typeof` 연산자는 피연산자의 데이터 타입을 문자열로 반환합니다.<br />
반환할 수 있는 문자열은 총 8가지이며, null은 반환할 수 없고, 함수의 경우 function을 반환합니다.

- “string” (원시)
- “number” (원시)
- “bigInt” (원시)
- “boolean” (원시)
- “undefined” (원시)
- “symbol” (원시)
- “object”
- “function”

```jsx
typeof 'hello';          // "string"
typeof 123;              // "number"
typeof 123456789n;       // "bigInt"
typeof true;             // "boolean"
typeof undefined;        // "undefined"
typeof new Symbol();     // "symbol"
typeof {};               // "object"
typeof [];               // "object"
typeof new Date();       // "object"
typeof /[123]/gi;        // "object"

// 중요한 부분!
typeof null;             // "object";
typeof function () {};   // "funtion";
```

<br />

null의 경우 null을 반환하지 않고 object를 반환합니다.<br />
때문에 값이 null인지 확인하기 위해서는 `typeof` 연산자 대신 일치 연산자(`===`)를 사용하는 것이 좋습니다. 

```jsx
const nothing = null;

typeof nothing;       // object
nothing === null;     // true
nothing === object;   // false
```

`typeof null`의 연산값이 object인 이유는 자바스크립트 언어 자체의 오류이나 하위 호환성을 유지하기 위해 오류를 수정하지 않고 현재까지 남겨두고 있습니다. **`null`의 데이터 타입은 객체가 아닌 특수한 값을 가진 별도의 고유 데이터 타입이라는 것을 명시해야 합니다!**

<br />

위와 같은 이유로 `typeof` 연산자를 통해 `object`, `array`, `null`을 판별하는 것이 불가합니다.

```jsx
const obj = { a: 'apple', b: 'banana', c: 'cheery' };
const arr = ['apple', 'banana', 'cherry'];
const nothing = null;

typeof obj;       // "object"
typeof arr;       // "object"
typeof nothing;   // "object"
```

<br />

함수의 경우에는 object를 반환하지 않고 `function`을 반환합니다.

```jsx
const sayHello = () => {
	console.log('hello!');
};

typeof sayHello;   // function
```
