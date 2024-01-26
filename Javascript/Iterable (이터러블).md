## Iterable (이터러블)

### Iteration Protocol 

ES6에서 도입된 **이터레이션 프로토콜(iteration protocol)** 은 반복 가능한(iterable) 자료구조를 만들기 위해 ECMAScript 사양에 정의하여 미리 약속한 규칙입니다.

ES6 이전에는 반복 가능한 데이터에 대해 통일된 규약 없이 `for, for…in, forEach` 메서드 등을 사용하여 순회할 수 있었지만, **ES6 이후 반복 가능한 데이터 컬렉션을 이터레이션 프로토콜을 준수하는 이터러블(iterable)로 통일하여** `for…of, Spread 연산자, 배열 구조 분해 할당`**을 사용할 수 있도록** 했습니다.

- **이터러블 프로토콜** (Iterable Protocol)
- **이터레이터 프로토콜** (Iterator Protocol)

![image](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/116873887/da82ca72-d701-4a69-8a45-764058110665)

<br />

### Iterable

**이터러블 프로토콜을 준수한 객체를 이터러블**이라고 합니다.<br />
이터러블은 `Symbol.iterator`를 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체를 말합니다.<br />
`Symbol.iterator` 메서드는 이터레이터를 반환하며, 이터러블은 `for…of` 순회와 `Spread` 문법을 사용할 수 있습니다.

```jsx
const handsome = ['조승우', '공유', '정우성'];

console.log(Symbol.iterator in handsome);   // true
```

배열은 `Symbol.iterator` 메서드를 가지고 있으므로 **이터러블 프로토콜을 준수한 이터러블 객체**입니다.<br />
그러므로 배열은 `for…of` 를 통해 순회가 가능하며 `Spread` 문법을 사용할 수 있습니다.

```jsx
for (const guy of handsome) {
  console.log(guy);
}

// 조승우
// 공유
// 정우성

console.log(...handsome);
// 조승우 공유 정우성

const [my, handsome, guy] = handsome;
console.log(my, handsome, guy);
// 조승우 공유 정우성
```

그러나 일반 객체는 `Symbol.iterator` 메서드를 **가지고 있지 않으므로** 이터러블이라고 할 수 없습니다.

```jsx
const gorgeous = {
  no1: '김혜수',
  no2: '전도연',
  no3: '문소리'
}

console.log(Symbol.iterator in gorgeous);   // false
```

따라서 이터러블 프로토콜을 준수하지 않는 객체는 `for…of` 를 통해 순회가 불가능하며 `Spread` 연산자 사용도 불가능합니다.

```jsx
for (const lady of gorgeous) {
  console.log(lady);
}
// TypeError: gorgeous is not iterable

console.log(...gorgeous); 
// TypeError: Spread syntax requires ...iterable[Symbol.iterator] to be a function
```

그러나 일반 객체 안에서 `Spread` 연산자 사용은 허용됩니다.

```jsx
console.log({ ...gorgeous });

// { no1: '김혜수', no2: '전도연', no3: '문소리' }
```

<br />

### Iterator

이터러블의 `Symbol.iterator` 메서드를 호출하면 이터레이터 프로토콜을 준수한 이터레이터를 반환합니다.<br />
이터러블의 `Symbol.iterator` 메서드가 반환한 이터레이터는 `next` 메서드를 가지고 있습니다.

```jsx
const handsome = ['조승우', '공유', '정우성'];

const iterator = handsome[Symbol.iterator]();

console.log(iterator);             // Array Iterator {}
console.log('next' in iterator);   // true
```

![image](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/116873887/aab5bc10-2a07-4b31-8a38-5d5f313085af)

이터레이터의 `next` 메서드를 호출하게 되면 `value, done` 프로퍼티를 갖는 **이터레이터 리절트(iterator result) 객체를 반환**합니다.

```jsx
let iteratorResult = iterator.next();

console.log(iteratorResult);   // { value: '조승우', done: false }
```

이터레이터의 `next` 메소드는 이터러블의 **각 요소를 순회하기 위한 포인터의 역할**을 하기 때문에 `next` 메소드를 연속적으로 호출하면 이터러블을 순차적으로 한 단계씩 순회하며 이터레이터 리절트 객체를 반환합니다.

```jsx
const handsome = ['조승우', '공유', '정우성'];

const iterator = handsome[Symbol.iterator]();

console.log(iterator.next());   // { value: '조승우', done: false }
console.log(iterator.next());   // { value: '공유', done: false }
console.log(iterator.next());   // { value: '정우성', done: false }
console.log(iterator.next());   // { value: undefined, done: true }
```

이터레이터의 `next` 메소드가 반환하는 이터레이터 리절트 객체의 `value` 프로퍼티는 **현재 순회 중인 이터러블의 값**을 반환하고, `done` 프로퍼티는 **이터러블의 순회 완료 여부**를 반환한다.

<br />

### Built-in Iterable (반복 가능한 객체)

ES6 이후 자바스크립트에서 제공하는 빌트인 이터러블(Built-in Iterable)의 종류는 아래와 같습니다.

| 빌트인 이터러블 | Symbol.iterator 메서드 |
| --- | --- |
| Array | Array.prototype[Symbol.iterator] |
| String | String.prototype[Symbol.iterator] |
| Map | Map.prototype[Symbol.iterator] |
| Set | Set.prototype[Symbol.iterator] |
| TypedArray | TypedArray.protype[Symbol.iterator] |
| arguments | arguments[Symbol.iterator] |
| DOM 컬렉션 | NodeList.prototype[Symbol.iterator]<br />HTMLCollection.protoype[Symbol.iterator] |

<br />

### Iterable과 유사 배열

- 이터러블(Iterable) : `Symbol.iterator` 메서드가 구현된 객체
- 유사배열(array-like) : `index`와 `length` 프로퍼티가 있어 배열처럼 보이는 객체

```jsx
const array_like = {
  0: "CS",
  1: "STUDY",
  length: 2
}

for (let item of array_like) {
  console.log(item);
}

// TypeError: array_like is not iterable
```

유의할 점은 `이터러블 객체 === 유사배열` 은 아닙니다.<br />
위 예제 코드는 인덱스와 length가 있지만 이터러블이 아니기 때문에 `for…of` 로 순회할 수 없습니다.

<br />

### for…of

`for…of` 문은 이터러블 내부적으로 `next` 메서드를 호출하여 이터러블을 순회하며,<br />
**`next` 메서드가 반환한 이터레이터 리절트 객체의 `value` 값을  변수에 할당**해 사용합니다.

이터레이터 리절트 객체의 `done` 프로퍼티 값이 `false`이면 순회를 계속하고, `true`이면 중단합니다.

```jsx
for (변수 선언문 of 이터러블) { ... }
```

```jsx
for (const guy of ['조승우', '공유', '정우성']) {
  console.log(guy);   // 조승우 공유 정우성 (변수에 차례대로 값이 할당!)
}
```

for…of 의 내부 동작 매커니즘을 for 문으로 표현하면 아래와 같습니다.

```jsx
// 이터러블 객체
const iterable = [1, 2, 3];

// 이터러블의 Symbol.iterator 메서드를 호출하여 이터레이터 생성
const iterator = iterable[Symbol.iterator]();

for ( ... ) {
  // 이터레이터의 next 메서드를 호출하여 이터러블 객체를 순회하여 이터레이터 리절트 객체를 반환
  const result = iterator.next();
  
  // 이터레이터 리절트 객체의 done 프로퍼티 값이 true일 때 순회 중단
  if (result.done) break;
  
  // 이터레이터 리절트 객체의 value 프로퍼티 값을 item 변수에 할당하여 출력
  const item = result.value;
  console.log(item);   // 1 2 3
}
```

<br />

### for…in

`for…in` 문은 객체의 프로토타입 체인 상 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `true`인 프로퍼티를 순회하며 열거합니다.
이때 프로퍼티 키가 `Symbol`인 프로퍼티는 열거하지 않습니다. 

```jsx
for (변수 선언문 of 객체) { ... }
```

```jsx
const hyesoo = {
  name: '김혜수',
  job: 'Actress',
  __proto__: { birth: 1970 }
};

for (const info in hyesoo) {
  console.log(info + ': ' + hyesoo[info]);
}

// name: 김혜수
// job: Actress
// birth: 1970
```

프로퍼티 키가 `Symbol`인 프로퍼티를 열거하지 않는 예는 아래와 같습니다.

```jsx
const symbol = Symbol();

const hyesoo = {
  name: '김혜수',
  job: 'Actress',
  [symbol]: 1970
};

for (const info in hyesoo) {
  console.log(info + ': ' + hyesoo[info]);
}

// name: 김혜수
// job: Actress
```

`[[Enumerable]]`의 경우 JS 엔진이 관리하는 내부 상태값이기 때문에 직접 접근 불가하지만 간접적으로 확인할 수 있습니다.<br />
`Object.getOwnPropertyDescriptor` 메서드는 주어진 객체의 속성에 대한 설명을 제공합니다.

```jsx
Object.getOwnPropertyDescriptor(obj, prop);
```

```jsx
console.log(Object.getOwnPropertyDescriptor(hyesoo, 'name'));
// { value: '김혜수', writable: true, enumerable: true, configurable: true }
```

<br />

### for…of vs for…in

> **`for… in`** 문은 객체 자신의 고유 프로퍼티뿐 아니라 상속받은 프로퍼티도 함께 열거합니다.<br />
즉, 객체에서 문자열로 key가 지정된 모든 열거 가능한 속성에 대해 반복합니다. (non-Symbol)
> 
> 
> 따라서 상속하지 않은 프로퍼티의 열거만 필요할 경우에는 `Object.prototype.hasOwnProperty` 메서드를 사용하여 객체 자신의 프로퍼티인지 확인하는 추가 처리가 필요하며, 객체 자신의 고유 프로퍼티만 열거하기 위해서는 for…in 보다 `Object.keys, Object.values, Object.entries` 메서드를 사용하는 것을 권장합니다.
> 

> **`for…of`** 문은 반복 가능한 객체, 즉 **이터러블(Iterable)에 대해 반복**합니다.<br />
이터러블에는 Array, String, Map, Set, TypedArray, Arguments, DOM 컬렉션이 있습니다.<br />
> 이터러블 객체는 for...of 문과 함께 Spread 연산자, 구조 분해 할당 연산을 사용할 수 있습니다.
>
