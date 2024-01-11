# LSP (Liskov Substitution Principle)


## What is LSP

리스코프 치환 원칙은 1988년 바바라 리스코프(Barbara Liskov)가 올바른 상속 관계의 특징을 정의하기 위해 발표한 것으로, 서브 타입은 언제나 기반 타입으로 교체할 수 있어야 한다는 것을 뜻한다.

`교체`할 수 있다는 말은, 자식 클래스는 최소한 자신의 부모 클래스에서 가능한 행위는 수행이 보장되어야 한다는 의미이다.

즉, 부모 클래스의 인스턴스를 사용하는 위치에 자식 클래스의 인스턴스를 대신 사용했을 때 코드가 원래 의도대로 작동해야 한다는 의미이다.

>이것을 부모 클래스와 자식 클래스 사이의 행위가 일관성이 있다고 말한다.

리스코프 치환 원칙이 제대로 지켜지지 않으면 다형성에 기반한 개방 폐쇄 원칙 역시 지켜지지 않기 때문에, 리스코프 치환 원칙을 지키는 것은 매우 중요하다.

<br />


## LSP를 위반한 예제 - 자식 클래스가 부모 클래스의 의도와 다르게 메소드를 오버라이딩

직사각형을 표현하기 위한 `Rectangle 클래스`는 가로와 세로 두 개의 값을 지정하거나, 얻을 수 있다.

```js
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getWidth() {
    return this.width;
  }

  getHeight() {
    return this.height;
  }
}
```

정사각형을 직사각형의 특수한 경우로 보고, 정사각형을 표현하기 위한 Square 클래스가 Rectangle 클래스를 상속받도록 구현을 했다고 하자. 

정사각형은 가로와 세로가 모두 동일한 값을 가져야 하므로, Square 클래스는 Rectangle 클래스의 setWidth() 메서드와 setHeight() 메서드를 재정의해서 가로와 세로 값이 일치하도록 구현하였다.

```js
class Square extends Rectangle {
  setWidth(width) {
    super.setWidth(width);
    super.setHeight(width);
  }

  setHeight(height) {
    super.setWidth(height);
    super.setHeight(height);
  }
}
```

이 때, 이제 `height`와 `width`를 비교해서 `height`를 조건부로 증가시키는 메서드가 존재한다고 가정해보자.

```js
function increaseHeight(params) {
    if (params.getHeight() <= params.getWidth()) {
      params.setHeight(params.getWidth() + 10);
    }
}
```
`increaseHeight()` 메서드를 사용하는 코드는 `increaseHeight()` 메서드 실행 후에 `width`보다 `height` 값이 더 크다고 가정할 것이다. 

그런데, `increaseHeight()` 메서드의 파라미터로 `Square` 객체가 전달되면 이 가정은 깨진다.

 `Square`의 `setHeight()` 메서드는 높이와 폭을 모두 같은 값으로 만들기 때문에 `increaseHeight()` 메서드를 실행하더라도 높이가 폭보다 길어지지 않게 된다.


이러한 문제를 해소하기 위해 `increaseHeight()` 메서드에서 rec 파라미터의 실제 타입이 Square인 경우에는 이 기능을 실행하지 않도록 `instanceof` 연산자를 사용할 수 있을 것이다.

 하지만, 앞서 봤듯이 `instanceof` 연산자를 사용한다는 것 자체가 리스코프 치환 원칙 위반이 되고, 이는 `increaseHeight()` 메서드가 Rectangle 확장에 열려 있지 않다는 것을 뜻한다.



```
개념상 정사각형은 높이와 폭이 같은 직사각형이므로, Rectangle 클래스를 상속받아 Square 클래스를 구현하는 것이 합리적으로 보일 수 있으나, 실제 프로그램에서는 이 둘을 상속 관계로 묶을 수 없다. 

increaseHeight() 와 같은 기능이 필요하다면, 실제 구현에서는 Square 클래스는 Rectangle 클래스를 상속받아 구현하기 보다는 별개의 타입으로 구현해 주어야 한다.
```


### LSP를 위반한 예제 - 잘못된 상속 관계 구성으로 인한 메서드 정의

`Animal` 이라는 클래스를 정의하고 동물은 대부분 목소리를 낼 수 있기 때문에 메소드 speak()을 통하여 메서드 구현을 강제하도록 규칙을 지정하였다.


```js
class Animal {
  speak() {
  }
}

class Cat extends Animal {
  speak() {
    console.log("냐옹");
  }
}

class Dog extends Animal {
  speak() {
    console.log("멍멍");
  }
}
```

그런데 개발을 진행하다 `Fish`  클래스를 추가해야 할 상황이 와서, 급하게 Animal 클래스를 상속했더니, 
물고기는 행할수 없는 `speak()` 메서드를 구현해야 하는 상황이 생겨 버렸다.

따라서 개발자는 호환성을 위해 `Fish` 클래스의 `speak()` 메서드는 동작을 하지 못하게 하고 예외(Exceptoin)을 던지도록 설정하였다.


```js

class Fish extends Animal {
  speak() {
    try {
      throw new Error("물고기는 말할 수 없음");
    } catch (error) {
      console.error(error.message);
    }
  }
}
```
이 부분을 자신이 개발하고 사용한다 그러면 문제가 되지 않는다. 문제는 다른 개발자와 협업할 때이다.

만일 다른 개발자가 제대로된 스펙 문서를 전달받지 못하고 남이 만들어 놓은 클래스를 사용하려고 할때 각 동물 타입에 맞게 울부짖는 결과를 내보내야 되는데, 갑자기 뜬금없이 예외를 던져버리니 개발자 간 상호 신뢰를 잃게 될수도 있다

