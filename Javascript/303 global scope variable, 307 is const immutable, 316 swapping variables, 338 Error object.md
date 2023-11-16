## 303. What is the output of below for loops
 
```js
for (var i = 0; i < 4; i++) {
  // global scope
  setTimeout(() => console.log(i));
}

for (let i = 0; i < 4; i++) {
  // block scope
  setTimeout(() => console.log(i));
}
```

### var(global scope)의 경우

<img width="643" alt="image" src="https://github.com/Fastcampus-Final-Team3/jober-frontend/assets/87072568/ce762593-5e85-41f5-89f4-cc90a74b2ef9">

- setTimeout 함수는 비동기적으로 실행되기 때문에 Task Queue에 익명함수 `() => console.lot(i)` 가 4개 쌓인다.
- 익명함수는 모든 반복문이 실행된 후 Call Stack에 올라간다.

<img width="642" alt="image" src="https://github.com/Fastcampus-Final-Team3/jober-frontend/assets/87072568/8d288339-9aaf-4e92-a9d4-8321ea885200">

- Call Stack이 비었으므로 첫번째 익명함수가 실행된다.
- 이 때 참조하는 i값은 var로 선언이 되었기 때문에 반복문 실행이 완료된 시점의 i인 4가 출력된다.

i는 전역 스코프(global scope)에 선언되었으므로 반복문이 싨행될 때 마다 값이 변경된다. 모든 반복문이 끝난 후에 , setTimeout 함수가 실행될 때마다 i의 최신 값(4)를 출력한다.

- 그래서 4가 4번 출력됩니다.

### let(block scope)의 경우

<img width="642" alt="image" src="https://github.com/Fastcampus-Final-Team3/jober-frontend/assets/87072568/75cb6a02-d800-421a-bef3-0aa1e991287d">

- let은 블록 스코프(block scope)를 가지므로 i의 값은 해당 반복 내에서만 유효하다.

- 익명함수가 호출될 때 각 익명함수의 스코프에 i값을 참조하게 되므로 0부터 3까지 순차적으로 출력된다.

## 307. Is const variable makes the value immutable

### const로 선언한 변수는 불변성을 가지는가?

```js
const name = 'howoo';

name = 'mango';

// Uncaught TypeError: Assignment to constant variable.

const name = 'howoo';

const name = 'mango';

// Uncaught SyntaxError: Identifier 'name' has already been declare
```

- 불변성을 가지는 것 처럼 보이지만

```js
const howoo = {
  age: 6,
  color: 'gold',
};

howoo.age = 7;

console.log(howoo);
// {age: 7, color: 'gold'}

const howoo = {
  age: 7,
  color: 'gold',
};
// Uncaught SyntaxError: Identifier 'howoo' has already been declared
```

- 참조형 데이터는 불변성을 지키지 못함.
- 그러나 재할당은 막을 수있다.

## 316. How do you swap variables in destructuring assignment

### 구조분해 할당을 사용하지 않는 경우

```js
let x = 1;
let y = 2;
let temp;

temp = y;
y = x;
x = temp;

console.log({ x, y, temp });
// {x: 2, y: 1, temp: 2}
```

- temp 변수를 만들어야 되고 temp변수가 계속 남아있음

```js
let x = 1;
let y = 2;

[x, y] = [y, x];
console.log({ x, y });
//{x: 2, y: 1}
```

## 338. What is the purpose of Error object

- JavaScript의 Error 객체는 런타임 오류가 발생했을 때 생성되며, 오류에 대한 정보를 담고 있다. 개발자는 Error 객체를 직접 생성함으로써 프로그램에서 오류를 인위적으로 발생시킬 수 있고 try catch 문으로 에러처리를 할 수 있다.

```js
new Error([message[, fileName[, lineNumber]]])
// 대괄호의 의미 알고 계신가요?
```

- 매개변수 없이: new Error()
- 메시지만 전달: new Error('오류발생')
- 메시지와 파일 이름 전달: new Error('오류발생', 'index.js')
- 메시지, 파일 이름, 줄 번호 모두 전달: new Error('오류발생', 'index.js', 2)

```js
let age = 29;

try {
  if (age < 30) throw new Error('나이 더먹고 와라');
} catch (e) {
  console.log(e); // Error: 나이 더먹고 와라
}

try {
  if (age < 30) throw new TypeError('나이 더먹고 와라');
} catch (e) {
  console.log(e); // TypeError: 나이 더먹고 와라
}

try {
  if (age < 30) throw new ReferenceError('나이 더먹고 와라');
} catch (e) {
  console.log(e); // ReferenceError: 나이 더먹고 와라
}

try {
  if (age < 30) throw new TypeError('나이 더먹고 와라', 'index.js', 20);
} catch (e) {
  console.log(e.name, e.message, e.fileName, e.lineNumber);

  // TypeError 나이 더먹고 와라 undefined undefined

  // 일반적으로, fileName과 lineNumber는 JavaScript 엔진이 오류를 생성할 때 자동으로 설정되며 브라우저가 Error 생성자에 fileName과 lineNumber 매개변수를 지원하지 않기 때문이다.
}
```
