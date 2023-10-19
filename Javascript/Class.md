# ES6의 Class

JS는 프로토타입 기반 객체지향 언어다. 즉 객체를 중심으로 코드를 구성하고, 객체 간에 상속과 연결을 통해 유연하게 코드를 작성하고 확장할 수 있다.

```javascript
// 부모 객체
const shape = {
  type: 'shape',
  getType: function () {
    return this.type;
  },
};

// 부모 객체를 상속받은 Circle 객체
const circle = Object.create(shape);
circle.type = 'Circle';

// Circle만의 고유한 메소드 추가
circle.getRadius = function () {
  return this.radius;
};

// Circle 객체를 상속 받은 TheCircle 객체
const theCircle = Object.create(circle);
theCircle.radius = 5;

console.log(theCircle.getType()); //
console.log(theCircle.getRadius()); //
```

ES6에서 도입된 Class는 기존 프로토타입 기반 객체지향 프로그래밍보다 자바와 같은 클래스 기반 객체지향 프로그래밍에 익숙한 프로그래머가 더욱 빠르게 학습할 수 있도록 클래스 기반 객체지향 프로그래밍 언어와 흡사한 새로운 매커니즘을 제시한다.

그렇다고 ES6의 클래스가 기존이 프로토타입 기반 객체지향 모델을 폐지하고 새롭게 클래스 기반 객체지향 모델을 제공하는 것은 아니고, 매우 유사하게 동작하는 `문법적 설탕`이라고 볼 수도 있다.

다만 클래스를 `새로운 객체 생성 매커니즘`으로 보는 것이 더 합당하다.

---

## 클래스 정의

> 클래스는 `class` 키워드를 사용하여 정의하고, 일반적으로 Pascal Case로 정의한다. <br />
> 일반적이지 않지만 함수와 마찬가지로 표현식으로 정의할 수 있다.

```javascript
class Person {} // 일반적인 정의

const Person = class {}; // 익명 클래스 표현식

const Person = class MyClass {}; // 기명 클래스 표현식
```

---

## 클래스 메소드

> 클래스는 생성자 함수이며, new 연산자와 함께 호출되어 인스턴스를 생성한다. <br />
> 클래스는 인스턴스를 생성하는 것이 유일한 존재 이유이므로 반드시 new 연산자와 함께 호출해야 한다. <br />
> 클래스 몸체에서 정의할 수 있는 메소드는 `생성자`, `프로토타입 메소드`, `정적 메소드`의 세 가지가 있다.

```javascript
// 클래스 선언문
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // name 프로퍼티는 기본적으로 public
  }

  // 프로토타입 메소드
  sayHi() {
    console.log(`Hi, ${this.name}`);
  }

  // 정적 메소드
  static sayHello() {
    console.log('Hello!');
  }
}

// 인스턴스 생성
const me = new Person('cu');

// 인스턴스의 프로퍼티 참조하기
console.log(me.name); //

// 프로토타입 메소드 호출하기
me.sayHi(); //

// 정적 메소드 호출하기
Person.sayHello(); // Hello!
```

---

### 정적 메소드

> 정적 메소드는 인스턴스를 생성하지 않아도 호출할 수 있는 메소드를 칭한다.

```javascript
class Square {
  static area(width, height) {
    return width * height;
  }
}

console.log(Square.area(10, 10)); // 100
```

정적 메소드 `area`는 2개의 인수를 전달받아 면적을 계산한다. 이 과정에서 인스턴스 프로퍼티를 참조하지 않는다.

만약 인스턴스 프로퍼티를 참조하는 프로토타입 메소드를 사용하려면 아래와 같이 구성한다

```javascript
class Square {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  // 프로토타입 메소드
  area() {
    return this.width * this.height;
  }
}

// 인스턴스 생성
const square = new Sqaure(10, 10);

console.log(square.area()); // 100
```

> 메소드 내부에서 인스턴스 프로퍼티를 참조할 필요가 있다면 `this`를 사용해야한다. 그렇지만 그럴 필요가 없다면 `this`를 사용하지 않는 메소드 즉, 정적 메소드로 정의하는 것이 좋다.

> 인스턴스의 변수에 영향을 받지 않기 때문에, 유틸리티 함수를 생성하는데 주로 사용되기도 한다. <br />
> 표준 빌트인 객체인 `Math`를 예시로 들자면 인스턴스와 상관없이 전역에서 `Math.max()`, `Math.floor()` 등을 사용할 수 있다.

---

### private 필드

대부분의 객체지향 프로그래밍 언어는 클래스를 정의하고 그 클래스를 구성하는 프로퍼티와 메소드에 대하여 `public`, `private`, `protected` 와 같은 접근 제한자를 선언하여 공개 범위를 한정할 수 있다. 그러나 JS는 접근 제한자를 제공하지 않는다. 따라서 모든 객체의 프로퍼티와 메소드는 기본적으로 외부에 공개되어 있다.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
}

const me = new Person('Lee');
console.log(me.name); // Lee
```

> 그러나 완벽하진 않지만 class의 `private` 필드를 이용하면 `클래스 외부에서 참조가 불가능`하게 만들 수 있다.

```javascript
class Person {
  // private 필드 정의
  #name = '';

  constructor(name) {
    // private 필드에 대한 내부 참조
    this.#name = name;
  }
}

const me = new Person('Lee');

// private 필드 #name은 클래스 외부에서 참조할 수 없다.
console.log(me.#name);
```

<img width="844" alt="image" src="https://github.com/1017yu/this-is-money/assets/83483378/bcb9d48c-acd4-4121-9314-47327510c2fe">

<br />

> private 필드를 통해 클래스 외부에서 직접 접근할 수 있는 방법은 없다.

---

### 여담

현재는 JavaScript 슈퍼셋인 TypeScript가 좀 더 객체지향 언어에 더욱 가깝다고 생각한다. <br />
(클래스 기반 객체지향 언어가 지원하는 접근제한자인 public, private, protected를 모두 지원)

그러나 항상 FE 개발자에게 JS의 기본 개념은 필수 요건이므로 Vanila JS를 다루는 것을 미루지 말자!
