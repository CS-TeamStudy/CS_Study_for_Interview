# Javascript

## Proxy object

#### Proxy?

- Proxy의 사전적 뜻은 '**대리인**', '**대리**'라는 뜻
- _JS_ 에서도 말그대로 **대리인의 역할** 을 해주는데, _**쉽게 말하면 객체의 여러 기본 동작을 가로채(트랩) 특별한 다른 동작을 할 수 있게 한다는 것**_

#### Proxy object?

- 특정 객체를 감싸 프로퍼티 읽기, 쓰기와 같은 객체에 가해지는 작업을 중간에서 가로채는 객체로, 가로채진 작업은 Proxy 자체에서 처리되기도 하고, 원래 객체가 처리하도록 그대로 전달되기도 한다

#### How to use Proxy?

- 문법

  ```javascript
  let proxy = new Proxy(target, handler);
  ```

  - `target`
    - proxy로 감싸게 될 객체, 함수를 포함한 모든 객체 가능
  - `handler`
    - 동작을 가로채는 메서드인 `트랩(trap)`이 담긴 객체로, 여기서 proxy 설정 (get,set 등 이용)
    - **get trap**은 target의 프로퍼티를 읽을 때, **set trap**은 target의 프로퍼티를 쓸 때 활성화

- proxy에 작업이 가해지고, `1.handler에 작업과 상응하는 trap이 있으면 실행되어 proxy가 이 작업을 처리함`, `2.trap이 없으면 target에 작업 직접 수행`

  ***

  `2.trap이 없으면 target에 작업 직접 수행`

  ```javascript
  let target = {};
  let proxy = new Proxy(target, {});
  // handler에 빈 객체
  proxy.test = 5;
  alert(target.test);
  // 5

  // hadler에 상응하는 trap이 없는 빈 객체이기 때문에 proxy를 사용해 값을 읽을 수 있음
  alert(proxy.test);
  // 5
  for (let key in proxy) alert(key);
  // test
  ```

- proxy handler가 trap이 존재하지 않는 빈 객체이기 때문에 proxy에 가해지는 모든 작업은 target에 직접 전달됨

  1. proxy.test=를 이용해 값을 쓰면 target에 새로운 값이 설정됨
  2. proxy.test를 이용해 값을 읽으면 target에서 값을 읽어옴
  3. proxy를 대상으로 반복 작업을 하면 target에 저장된 값이 반환됨

- **_트랩이 없으면 proxy는 target을 둘러싸는 투명한 rapper가 됨_**

  <img width="302" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/25fd0612-0d52-4550-b9ff-94dd2ac58498">

  Proxy는 일반 객체와는 다른 행동 양상을 보이는 `특수 객체(exotic object)` 로,프로퍼티가 없으므로 handler가 비어있으면 Proxy에 가해지는 작업은 target에 곧바로 전달됨

## Proxy의 기능 활성화

객체에 어떤 작업을 할 땐 자바스크립트 명세서에 정의된 '_내부 메서드(internal method)_'가 깊숙한 곳에서 관여하는데 프로퍼티를 읽을 땐 [[Get]]이라는 내부 메서드가, 프로퍼티에 쓸 땐 [[Set]]이라는 내부 메서드가 관여하게 된다.
하지만 내부 메서드들은 명세서에만 정의된 메서드이기 때문에 개발자가 코드를 사용해 호출할 순 없다.

이때, Proxy의 handler trap이 내부 메서드의 호출을 가로채 사용하게 된다

여기서 handler method를 사용해 trap을 만들수 있다

<img width="819" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/7a3fd4d5-46d7-4adf-b914-57fefb67ffec">

### get, set

---

`1.handler에 작업과 상응하는 trap이 있으면 실행되어 proxy가 이 작업을 처리함`

### get trap

- 프로퍼티를 읽거나 쓸 때 사용되는 trap으로 가장 흔히 볼 수 있는 trap
- get method는 **프로퍼티를 읽으려고 할 때** 작동

#### How to?

- handler에 get(`target`, `property`, `receiver`) method 사용

  - `target` : 동작을 전달할 객체로 new Proxy의 첫 번째 인자
  - `property` : 프로퍼티 이름
  - `receiver` : 타깃 프로퍼티가 getter라면 receiver는 getter가 호출될 때( 그 시점의) this로, _대개는 proxy 객체 자신이 this이다_

  ```javascript
  let cats = ["냐옹", "먀옹", "애옹"];

  cats = new Proxy(cats, {
    get(target, prop) {
      // target => new Proxy의 첫번째 인자인 cats
      if (prop in target) {
        return target[prop];
      } else {
        return "먀옹";
        // 기본값
      }
    },
  });

  console.log(cats[냐옹]);
  // 냐옹
  console.log(cats[멍]);
  // 먀옹 (해당하는 요소가 배열에 없으므로 설정한 기본값 '멍'이 반환됨)
  ```

  ```javascript
  const handler = {
    get(target, prop) {
      return prop in target ? target[prop] : "왈";
    },
  };

  const cats = new Proxy({}, handler);
  cats.mimi = "먕";
  cats.nabi = undefined;

  console.log(cats.mimi, cats.nabi);
  // "먕", undefined

  console.log("baduck" in cats, cats.baduck);
  // false, '왈'

  console.log("mimi" in cats, cats.mimi);
  // true '먕'
  ```

  <img width="300" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/750539f9-4109-4cf3-8b95-e681eacf9aa5">

  <img width="300" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/16e917ae-011d-4680-8976-26e789466a07">

  <img width="300" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/2d131b4c-4d36-4c57-891f-7cfdab370681">

### set trap

- handler에 set(target, property, value, receiver) method 사용

  - `target` : 동작을 전달할 객체로 new Proxy의 첫 번째 인자입니다.
  - `property` : 프로퍼티 이름
  - `value` : 프로퍼티 값
  - `receiver` : get 트랩과 유사하게 동작하는 객체로, setter 프로퍼티에만 관여함

- **set trap**은 값을 성공적으로 처리했으면 반드시 true를 반환해야함 그렇지 않은 경우엔( true를 반환하지 않았거나 falsy한 값을 반환하게 되면 TypeError가 발생) false를 반환하도록 해야함

  ```javascript
  let cats = [];

  cats = new Proxy(cats, {
    set(target, prop, val) {
      if (typeof val == string) {
        target[prop] = val;
        return true;
      } else {
        return false;
      }
    },
  });

  cats.push("먕"); // true
  cats.push("골골"); // ture
  console.log("Length is: " + cats.length); //Length is 2

  cats.push(1); // Error: 'set' on proxy

  console.log(
    "윗줄에서 에러가 발생했기 때문에 이 줄은 절대 실행되지 않습니다.",
  );
  ```
