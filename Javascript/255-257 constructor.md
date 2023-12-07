# 255. What is a constructor method

![image](https://github.com/FastSubTeam/front/assets/83483378/6a75e8d7-ddbf-4ea8-b197-2530d64908b3)

constructor 메서드는 클래스 내에서 생성 및 초기화하는 특수한 메서드이다.

constructor는 인스턴스를 생성하고 초기화의 목적으로 사용되고, 이름은 변경할 수 없다.

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}
```

![image](https://github.com/FastSubTeam/front/assets/83483378/6813195a-9f52-481f-a3da-258ee9b450f0)

위 사진 처럼 클래스는 함수 객체로 평가되고, 고유의 프로퍼티를 모두 가지고 있다.

`constructor` 프로퍼티는 클래스 자신을 가리키고 있다는 것을 알 수 있다.

이는 클래스가 인스턴스를 생성하는 생성자 함수임을 알 수 있으며, `new` 연산자와 함께 클래스를 호출하면, 클래스는 인스턴스를 생성한다.

```js
const personInstance = new Person('cu');

console.log(personInstance);
```

![image](https://github.com/FastSubTeam/front/assets/83483378/b77e6167-4ea1-4814-958d-440afbfdb140)

> `Person` 클래스의 `constructor` 내부에서 `this`에 추가한 `name` 프로퍼티가 클래스가 생성한 인스턴스의 프로퍼티로 추가된 것을 알 수 있다

<br />

### 클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티

클래스의 `constructor` 메서드와, 프로토타입의 `contructor` 프로퍼티는 이름이 같지만 직접적인 관련이 없다.

프로토타입의 `constructor` 프로퍼티는 모든 프로토타입이 가지고 있는 프로퍼티이며, 생성자 함수를 가리킨다.

<br />

# What happens if you write constructor more than once in a class

`constructor` 메서드는 클래스 내에 최대 **한 개**만 존재할 수 있다.

만약 클래스가 2개 이상의 `constructor`를 포함하면, 문법 에러<sup>SyntaxError</sup>가 발생한다.

```js
class Employee {
  constructor() {
    this.name = "John";
  }
  constructor() {   //  Uncaught SyntaxError: A class may only have one constructor
    this.age = 30;
  }
}
```

![image](https://github.com/FastSubTeam/front/assets/83483378/d37d6c1b-4c22-4689-ba42-baaaa736a630)

### 클래스의 `constructor` 메서드는 생략할 수 있다.

```js
class Convenience {}
```

위 코드는 아래와 동일하다.

```js
class Convenience {
  // 아래와 같이 빈 constructor가 암묵적으로 정의됨
  constructor() {}
}
```

`constructor`를 생략한 클래스는 빈 `constructor`에 의해 빈 객체를 생성한다.

```js
class Convenience {}

const cu = new Convenience();

console.log(cu); // Convenience {}
```

### 인스턴스 초기화

인스턴스를 생성할 때 클래스 외부에서 인스턴스 프로퍼티의 초기값을 전달하려면 다음과 같이 `constructor`에 매개변수를 선언하고
인스턴스를 생성할 때 초기값을 전달한다.

이 때 초기값은 `constructor`의 매개변수에게 전달된다.

```js
class Conveience() {
  constructor(name, stack) {
    // 인수로 인스턴스 초기화
    this.name = name;
    this.stack = stack;
  }
}

// 인수로 초기값을 전달한다.
const cu = new Convenience('cu', 'fe');

console.log(cu.name, cu.stack); // 'cu', 'fe'
```

이처럼 `constructor` 내에서는 인스턴스의 생성과 동시에 인스턴스 프로퍼티 추가를 통해 인스턴스의 초기화를 실행한다.

따라서 인스턴스를 초기화하려면 `constructor`를 생력해서는 안된다.

# How do you call the constructor of a parent class

### extends

JS 클래스는 기존(부모) 클래스를 상속받아 새로운 클래스를 확장<sup>extends</sup>하여 정의할 수 있다.

![image](https://github.com/FastSubTeam/front/assets/83483378/6a273ed1-a02a-4872-a8cb-4ce339fc0c38)

동물을 추상화한 `Animal` 클래스, 새와 사자를 추상화한 `Bird`, `Lion` 클래스를 각각 정의해보자.

`Animal` 클래스는 동물의 속성을 표현하고 `Bird`, `Lion` 클래스는 상속을 통해 `Animal` 클래스의 속성을 그대로 사용하면서 자신만의 고유한 속성만 추가하여 확장할 수 있다.

---

### 동적 상속

extends 키워드 다음에는 클래스뿐만이 아니라 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다.

이를 통해 동적으로 상속받을 대상을 결정할 수 있다.

```js
class CU {}

class GS {}

let condition = true;

// 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
class Convenience extends (condition ? CU : GS) {}

const convenience = new Convenience();
console.log(convenience); // Convenience {}

console.log(convenience instanceof CU); // true
console.log(convenience instanceof GS); // false
```

---

### super

`super` 키워드는 함수처럼 호출할 수도 있고 this와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.

- `super`를 호출하면 수퍼클래스의 constructor(super-constructor)를 호출한다.
- `super`를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

다음 예제는 `new` 연산자와 함께 서브 클래스를 호출하면서 인수를 전달하고, `super` 호출을 통해 수퍼 클래스의 `constructor`로 전달하는 예제이다.

```js
class SuperClass {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

class SubClass extends SuperClass {
  // constructor를 선언하지 않으면, 암묵적으로 생성
  // constructor(...args) { super(...args) }
}

const subClass = new SubClass(1, 2);
console.log(subClass); // SubClass {a: 1, b: 2}
```

위와 같이 수퍼 클래스의 `constructor`에 전달할 필요가 있는 인수를 서브 클래스 `constructor`에서 호출하는 `super`를 통해 전달한다.

---

### super 주의할 점

1. 서브 클래스에서 constructor을 생략하지 않는 경우 서브 클래스의 `constructor`에서는 반드시 `super`를 호출해야 한다.

```js
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    console.log('constructor call');
  }
}

const derived = new Derived();
```

2. 서브 클래스의 `constructor`에서 `super`를 호출하기 전에는 this를 참조할 수 없다.

```js
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    this.a = 1;
    super();
  }
}

const derived = new Derived(1);
```

3. `super`는 반드시 서브 클래스의 `constructor`에서만 호출한다.

```js
class Base {
  constructor() {
    super(); // SyntaxError: 'super' keyword unexpected here
  }
}

function Foo() {
  super(); // SyntaxError: 'super' keyword unexpected here
}
```

---

### super 참조

> 메서드 내에서 super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

```js
// 수퍼클래스
class CU {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `하이! ${this.name}`;
  }
}

// 서브클래스
class Junhee extends CU {
  sayHi() {
    // super.sayHi는 수퍼클래스의 프로토타입 메서드를 가리킨다.
    return `${super.sayHi()}. 잘 지내니?`;
  }
}

const junheeInstance = new Junhee('시우');

console.log(junheeInstance.sayHi()); // 하이! 시우 잘 지내니?
```

---

> 서브 클래스의 정적 메서드 내 super를 통해 수퍼 클래스의 정적 메서드에 접근할 수 있다.

```js
// 수퍼클래스
class CU {
  static sayHi() {
    return `하이! 시우`;
  }
}

// 서브클래스
class Junhee extends CU {
  static sayHi() {
    // super.sayHi는 수퍼클래스의 정적 메서드를 가리킨다.
    return `${super.sayHi()}. 잘 지내니?`;
  }
}

console.log(Junhee.sayHi()); // ??
```
