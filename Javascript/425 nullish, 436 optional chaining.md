# Nullish coalescing operator

**널 병합 연산자(`??`)** 는 왼쪽 피연산자가 `null` 또는 `undefined`일 때 오른쪽 피연산자를 반환하고, <br />
그렇지 않으면 왼쪽 피연산자를 반환하는 논리 연산자이다.
 
> 이는 왼쪽 피연산자가 `null` 또는 `undefined` 뿐만 아니라 `falsy` 값에 해당할 경우 오른쪽 피연산자를 반환하는 논리 연산자 `OR(||)`와 대조된다.

```js
const first = null ?? 'default';
console.log(first); // 'default'

const second = 0 ?? 42;
console.log(second) // '0'
```

`||` boolean 논리 연산자는 만약 `0`, `''` 또는 `NaN`을 유효한 값으로 생각한 경우 예기치 않는 결과를 초래할 수 있다.

```js
let count;
let text;

count = 0;
text = '';

let qty = count || 42;
let message = text || 'hi';

console.log(qty); // 42
console.log(message) // "hi"
```


---

널 병합 연산자는 `OR`와 `AND` 같은 논리 연산자들과 마찬가지로, 만약 왼쪽이 `null` 또는 `undefined`가 아님이 판명되면, 오른쪽 표현식을 평가되지 않는다.

```js
function A() {
  console.log("A was called");
  return undefined;
}

function C() {
  console.log("C was called");
  return "foo";
}

console.log(A() ?? C());
```

<details>
<summary>정답은?</summary>

<img width="324" alt="image" src="https://github.com/Kernel360/f1-Orury-Client/assets/83483378/1e7be934-3151-4ff1-a139-f7d9cb170a2f">

</details>

<br />
<br />
<br />
<br />

```js
function B() {
  console.log("B was called");
  return false;
}

function C() {
  console.log("C was called");
  return "foo";
}

console.log(B() ?? C());
```

<details>
<summary>정답은?</summary>

<img width="332" alt="image" src="https://github.com/Kernel360/f1-Orury-Client/assets/83483378/1b9dec30-9d95-4a4b-a71e-52b2f72a25a4">

</details>


<br />
<br />
<br />

# Optional chaining


**optional chaining** 연산자 (`?.`) 는 체인의 각 참조가 유효한지 명시적으로 검증하지 않고, 연결된 객체 체인 내에 깊숙이 위치한 속성 값을 읽을 수 있다.

`?.` 연산자는 `.` 체이닝 연산자와 유사하게 작동하지만, 만약 참조가 `nullish` (`null` 또는 `undefined`)이라면, 에러가 발생하는 것 대신에 표현식의 리턴 값은 `undefined로` 단락된다. 함수 호출에서 사용될 때, 만약 주어진 함수가 존재하지 않는다면, `undefined를` 리턴한다.

따라서 참조가 누락될 가능성이 있는 경우 연결된 속성으로 접근할 때 더 짧고 간단한 표현식이 생성된다. 어떤 속성이 필요한지에 대한 보증이 확실하지 않는 경우 객체의 내용을 탐색하는 동안 도움이 될 수 있다.


```js
const Animal = {
  name: 'hi',
  cat: {
    name: 'bye',
  }
}

const dogName = Animal.dog?.name;
console.log(dogName); // undefined
```


---

### 암묵적 확인

 중첩된 구조를 가진 객체에서 obj가 있다. optional chaining이 없이 깊이 중첩된 하위 속성을 찾으려면, 다음과 같이 참조를 확인해야 한다

```js
let nestedProp = obj.first && obj.first.second;
```

`obj.first.second의` 값에 접근하기 전에 `null` 그리고 `undefined`가 아니라는 점을 검증한다.

그러나 optional chaining 연산자(?.)를 사용하여, obj.first.second 에 접근하기 전에 obj.first의 상태에 따라 명시적으로 테스트하거나 단락시키지 않아도 된다.

```js
let nestedProp = obj.first?.second;
```


### 할당자의 left-hand side

할당자의 왼쪽에서 Optional Chaining을 시도하는 경우, `SyntaxError`를 반환한다.

```js
let object = {};
object?.property = 1; // Uncaught SyntaxError: Invalid left-hand side in assignment
```

### `Nullish coalescing operator` 널 병합 연산자와의 연계

널 병합 연산자는 Optional chaining을 사용한 후에 아무 값도 찾을 수 없는 경우 기본 값을 할당하기 위해 사용될 수 있다.

```js
let customer = {
  name: "Carl",
  details: { age: 28 },
};

const customerCity = customer?.city ?? "Unknown city";
console.log(customerCity); // Unknown city
```
