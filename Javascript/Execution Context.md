## 실행 컨텍스트 (Execution Context)
실행 컨텍스트는 자바스크립트의 동작 원리를 담고 있는 핵심 개념입니다. 자바스크립트 엔진은 소스코드를 실행하기 전에 ‘평가 과정’을 거치게 되는데, 이 평가 과정에서 자바스크립트 엔진은 실행 컨텍스트를 생성하고 변수와 함수 등의 선언문을 먼저 실행하여 해당하는 스코프에 저장하게 됩니다.

평과 과정이 끝나면 소스코드가 실행되는 ‘런타임 환경(runtime)’이 시작되는데, 이 때 소스코드 실행에 필요한 변수나 함수의 참조 정보 등을 실행 컨텍스트가 관리하는 스코프(scope)에서 찾습니다. 그리고 변수에 할당된 값이 변경되는 등의 소스코드의 실행 결과를 다시 실행 컨텍스트가 관리하는 스코프에 저장합니다.

이처럼 자바스크립트 엔진은 **1. 전역 코드 평가 2. 전역 코드 실행 3. 함수 코드 평가 4. 함수 코드 실행**의 단계를 거쳐 소스코드를 실행하고, 함수 호출이 종료되면 함수 호출 이전으로 돌아가기 위해 현재 실행 중인 코드와 이전에 실행했던 코드를 별도로 관리해야 합니다. 즉, 하나의 소스코드를 실행하기 위해서는 **스코프, 식별자, 코드 실행 순서 등의 관리**가 필요합니다. 그리고 이 모든 것을 관리하는 것이 바로 ‘실행 컨텍스트’ 입니다.

<br />

### 실행 컨텍스트가 무엇인가요?

실행 컨텍스트는 소스코드를 실행하는데 필요한 환경 정보들을 저장해두는 영역(객체)입니다. 식별자(변수, 함수, 클래스 등의 이름)를 등록하고 관리하는 스코프(scope)와 코드의 실행 순서 관리를 구현하는 내부 매커니즘으로, 모든 코드가 실행 컨텍스트를 통해 실행되고 관리됩니다.

실행되는 소스코드의 타입에 따라 각각 다른 실행 컨텍스트가 생성되며, 생성된 실생 컨텍스트 내부에는 Variable Environment, Lexical Environment, This Binding 정보가 담기게 됩니다.

<br />

### 실행 컨텍스트 내부에는?

- **Variable Environment (변수 환경)**
    - 현재 실행되는 컨텍스트 내의 식별자 정보 (변수명, 함수명, 클래스명 등)
    - 외부 환경 정보
    - 선언 시점의 렉시컬 환경 스냅샷 (변경 사항이 저장되지 않음)
- **Lexical Environment (렉시컬 환경)**
    - 처음에는 VariableEnvironment와 동일
    - 변경 사항이 실시간으로 저장 됨
- **This Binding (This 바인딩)**
    - 식별자가 바라보아야 할 대상 객체 저장

<br />

### 실행 컨텍스트 종류

- **전역 코드 - 전역 실행 컨텍스트**
    - 자바스크립트 엔진이 소스코드를 처음 마주할 때 생성
- **함수 코드 - 함수 실행 컨텍스트**
    - 자바스크립트 엔진이 함수를 실행할 때 생성
- **eval 코드 - eval 실행 컨텍스트**
    - eval 코드는 strict mode에서 자신만의 독자적인 스코프를 생성한다. eval 코드가 평가되면 eval 실행 컨텍스트가 생성된다
    - 현재는 잘 사용되지 않음, MDN에서 사용하지 말 것을 권고
- **모듈 코드 - 모듈 실행 컨텍스트**
    - 모듈 코드는 모듈별로 독립적인 모듈 스코프를 생성한다. 이를 위해 모듈 코드가 평가되면 모듈 실행 컨텍스트가 생성된다.

<br />

```jsx
var x = 'global context';

function A() {
  function B() {
    console.log(x);
    var x = 'B local context';
    console.log(x);
  }

  B();
  console.log(x);
}

A();

console.log(x);
```

![image](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/116873887/42a054f3-0607-4f2c-9514-718902b9c3da)

자바스크립트 엔진은 동일한 코드 타입의 환경 정보를 같은 컨텍스트에 담아 콜 스택에 저장하는데
위 코드를 토대로 살펴보면,

- 변수 `x`는 `var` 키워드를 통해 선언되었기 때문에 `전역 컨텍스트`에 저장됩니다.
- `A()` 함수가 호출 되면서 `A 함수 컨텍스트`가 생성되고, A 함수에 필요한 정보가 저장됩니다.
- `A()` 함수 내부에서 `B()` 함수가 실행되면서 `B 함수 컨텍스트`가 생성되고, B 함수에 필요한 정보가 저장됩니다.
- `B()` 함수가 종료되어, `B 함수 컨텍스트`가 스택에서 사라집니다.
- `A()` 함수가 종료되어, `A 함수 컨텍스트`가 스택에서 사라집니다.
- 이후 `console.log(a)`가 종료되면 스택에서 `전역 컨텍스트` 또한 사라지며, 프로그램이 종료됩니다.

<br />

```jsx
// 전역 컨텍스트
var x = 'global context';

// A 함수 컨텍스트
function A() {
	// B 함수 컨텍스트
  function B() {
    console.log(x);   // undefined
    var x = 'B local context';
    console.log(x);   // B local context
  }

  B();

  console.log(x);   // global context
}

A();

console.log(x);  // global context
```

`함수 B`에서 호출된 `console.log(x)`는 `undefined`를 반환합니다.
하지만 `함수 A`에서 호출된 `console.log(x)`는 `‘global context’`라는 문자열을 반환합니다.

같은 변수 `x`를 호출하고 있지만 서로 다른 값을 반환하는 이유는 무엇일까요?

실행 컨텍스트가 생성되면서 각각의 실행 컨텍스트 내부에는 LexicalEnvironment가 생성되는데,
LexicalEnvironment의 내부에는 **environmentRecord**와 **outerEnvironmentReference**로 구성됩니다.
이 중 **environmentRecord**에 의해서 자바스크립트의 **호이스팅(Hoisting)** 현상이 발생하게 됩니다.

`함수 B` 내부에서는 var 키워드를 통해 변수 `x`가 선언되어 있습니다. 때문에 `B 함수 실행 컨텍스트`에서는 호이스팅 현상을 통해 변수 `x`의 선언문이 스코프의 가장 위로 올라가게 됩니다. 그래서 `함수 B`의 `console.log(x)`는 호이스팅을 통해 초기화된 변수 `x`의 값 `undefined`를 먼저 반환하고, 이후 변수 `x`에 `‘B local context’` 문자열이 할당되어 `‘B local context’`가 반환됩니다.

```jsx
function B() {
	var x;            // hoisting!
	console.log(x);   // undefined;
	x = 'B local context';
	console.log(x);   // B local context
}
```

반면에 `함수 A` 내부에서는 변수 `x`가 선언되어있지 않기 때문에 자바스크립트 엔진이 코드를 실행하면서 `x`에 대한 값을 반환하기 위해 실행 컨텍스트가 관리하는 스코프에서 `x`의 값을 검색합니다. `A 함수 컨텍스트`에는 `x`에 대한 값이 없기 때문에 **스코프 체이닝(scope chaining)**을 통해 상위 스코프로 거슬러 올라가면서 `x`에 대한 값을 찾고, 값을 찾았다면 스코프 체이닝이 종료되어 `x` 값이 반환됩니다.

```jsx
function A() {
  function B() {
    console.log(x);
    var x = 'B local context';
    console.log(x);
  }

  B();

  console.log(x);   // global context
}
```

| 전역 컨텍스트 |  |
| --- | --- |
| x | ‘global context’ |

⬆️  *(x값이 없네? 상위 스코프에서 찾자!)*

| 함수 A 컨텍스트 |  |
| --- | --- |
| B | function () { } |

이렇듯 **스코프 체이닝(scope chaining)** 은 특정 컨텍스트 내부에 또 다른 컨텍스트가 존재하고, 외부 컨텍스트의 스코프와 내부 컨텍스트의 스코프가 체인처럼 연결되어 있다는 의미입니다.

스코프 체이닝은 실행 컨텍스트 내부에 생성된 LexicalEnvironment의 **outerEnvironmentReference**를 통해 가능하게 되는데, **outerEnvironmentReference**는 현재 호출된 함수(A)가 선언될 당시의 LexicalEnvironment를 참조하기 때문에 변수 `x`에 대한 정보가 저장되어 있어 스코프 체이닝이 가능하게 됩니다. 

<br />

## 정리

- `실행 컨텍스트(Execution Context)`는 자바스크립트의 동작 원리를 담고 있는 핵심 개념입니다.
- 자바스크립트 엔진이 처음으로 스크립트 소스코드를 실행할 때 `전역 컨텍스트(Global Execution Context)`가 생성됩니다.
- 함수가 호출될 때 마다 호출된 시점에서 해당 함수의 실행 컨텍스트인 `함수 컨텍스트(Function Execution Context)`가 생성됩니다.
- 생성된 컨텍스트가 활성화되는 시점에서는 `호이스팅`, `외부 환경 정보 구성`, `this 값 설정`이 일어납니다.
- 특정 컨텍스트 내부에 또 다른 컨텍스트가 존재하여 컨텍스트가 중첩되었을 경우에는 `스코프 체이닝(Scope Chaining)`이 일어날 수 있으며, 하위 스코프에서 상위 스코프 방향의 단방향 탐색만 수행할 수 있습니다.

<br />

## Quiz!

각각의 console.log에 출력되는 값은 무엇일까요?
호이스팅과 스코프 체이닝을 생각하며 정답을 맞춰보세요.

```jsx
var x = '글로벌 스코프';

function A() {
    console.log(x);   // ?

    var x = 'x는 A 로컬 스코프';
    var y = 'y도 A 로컬 스코프';

    function B() {
        console.log(x);   // ?
        console.log(y);   // ?

        var y = 'y는 B 로컬 스코프';  
        var z = 'z는 B 로컬 스코프';

        function C() {
            console.log(x);   // ?
            console.log(y);   // ?
            console.log(z);   // ?
        }
        
        C();
    }
    
    B()
}

A();
```


<details>
<summary>정답은?</summary>
<div markdown="1">

```jsx
    var x = '글로벌 스코프';
    
    function A() {
    		var x;
        console.log(x);   // undefined
    
        x = 'x는 A 로컬 스코프';
        var y = 'y도 A 로컬 스코프';
    
        function B() {
            console.log(x);   // x는 A 로컬 스코프
    				var y;
            console.log(y);   // undefined
    
            y = 'y는 B 로컬 스코프';  
            var z = 'z는 B 로컬 스코프';
    
            function C() {
                console.log(x);   // x는 A 로컬 스코프
                console.log(y);   // y는 B 로컬 스코프
                console.log(z);   // z는 B 로컬 스코프
            }
            
            C();
        }
        
        B()
    }
    
    A();
```

<br />

- `함수 A`의 내부에는 `x`가 선언되어있기 때문에 호이스팅을 통해 변수 `x`의 선언문이 `함수 A의 스코프` 내 가장 상단으로 끌어올려져 `undefined`가 출력됩니다.
- `함수 B`에서 `x`가 선언되지 않았기 때문에 호이스팅이 일어나지 않고, 스코프 체이닝을 통해 함수 B의 실행 컨텍스트에서 함수 A의 실행 컨텍스트로 거슬러 올라가 변수 `x`에 할당된 값(’x는 A의 로컬 스코프’)을 찾아 출력합니다.
- `함수 B`에서는 `y`가 선언되어있기 때문에 호이스팅을 통해 변수 `y`의 선언문이 `함수 B의 스코프` 내 가장 상단으로 끌어올려져 `undefined`가 출력됩니다.
- `함수 C`에서는 아무 변수도 선언되지 않았기 때문에 스코프 체이닝을 통해 상위 스코프로 거슬러 올라가며 `x, y, z`에 할당된 값을 각각 찾아 출력합니다.

</div>
</details>

<br />

```jsx
var x = '글로벌 스코프';

function A() {
    console.log(x);    // ?

    var x = 'x는 A 로컬 스코프'
    var y = 'y도 A 로컬 스코프'
    var z = 'z도 A 로컬 스코프'

    function B() {
        console.log(x);    // ?
        console.log(y);    // ?
        console.log(z);    // ?

        function C() {
            console.log(x);    // ?
            console.log(y);    // ?
            console.log(z);    // ?
        }
        
        C();
    }
    
    B();
}

A();
```

<details>
<summary>정답은?</summary>
<div markdown="2">

```jsx
var x = '글로벌 스코프';

function A() {
    console.log(x);    // undefined

    var x = 'x는 A 로컬 스코프';
    var y = 'y도 A 로컬 스코프';
    var z = 'z도 A 로컬 스코프';

    function B() {
        console.log(x);    // x는 A 로컬 스코프
        console.log(y);    // y도 A 로컬 스코프
        console.log(z);    // z도 A 로컬 스코프

        function C() {
            console.log(x);    // x는 A 로컬 스코프
            console.log(y);    // y도 A 로컬 스코프
            console.log(z);    // z도 A 로컬 스코프
        }
        
        C();
    }
    
    B();
}

A();
```

</div>
</details>
