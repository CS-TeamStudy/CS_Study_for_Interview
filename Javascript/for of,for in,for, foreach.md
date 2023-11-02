# JavaScript

### for ...of 와 for ...in의 차이점

```javascript
//  Prototype 속성 추가
Object.prototype.objCustom = function () {};
Array.prototype.arrCustom = function () {};

// arr 배열 속성 추가
var arr = [300, 400];
arr[2] = 500;
arr.son = "sony";

// for ...of
console.log("for-of");
for (let item of arr) {
  console.log(item);
}

// for ...in
console.log("for-in");
for (let idx in arr) {
  console.log(idx, arr[idx]);
}
```

- **for ...of**  
  <img width="499" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/b8119906-767f-47e5-9ca4-b6afdbf57002">

  > **눈에 보이는 배열의 원소를 index 순서대로 출력함**

- **for ...in**  
  <img width="488" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/9985d94e-722c-41af-a4f8-02ba8590f6e8">
  > 눈에 보이는 배열의 원소 뿐만 아니라, **눈에 보이지 않는 프로토타입 속성의 인덱스까지 모두 돌며 반복**하는 것을 알 수 있고, objCustom을 먼저 정의해주었지만, arrCustom이 먼저 출력되는것으로 알 수 있듯 인덱싱 순서를 보장해 주지 못함

#### for ...of

- **반복가능한 객체(Array, Map, Set, String, TypedArray, arguments 객체 등을 포함)에 대해서 반복(interable한 자료형)**하고, 각 개별 속성값에 대해 실행되는 문이 있는 사용자 정의 반복 후크를 호출하는 루프를 생성함

#### for ...in

- **상속된 열거 가능한 속성들을 포하하여 객체에서 문자열로 키가 지정된 모든 열거 가능한 속성에 대해 반복함**
- for.. in 반복문은 **_순서 없이_** 반복이 되는 특징을 지니기 때문에 index가 중요한 **배열**에 사용하게되면 **배열의 index 순서대로 반복한다는 결과를 보장할 수 없기 때문**에 권장하지 않음
- **enumerable 속성**으로 순환할 것인지 아닌지를 결정 할 수 있음
- 배열에 반복문을 사용하는 경우 for...in문은 for...of문보다 처리 속도가 느림

### 그렇다면 for ...in은 언제 사용하는것이 좋을까?

- 객체(Object)에서의 반복에서 for ...in을 사용하자

  ```javascript
  var obj = { name: "kangin", age: 23, skill: "미드필더" };

  // for ...of
  console.log("for.. of 반복 결과");
  for (const key of obj) {
    console.log(key);
  }

  // for ...in
  console.log("for.. in 반복 결과");
  for (const key in obj) {
    console.log(key, obj[key]);
  }
  ```

- **for ...of**  
  <img width="539" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/c007aef5-7ffe-4050-9c58-5f8104bf42b5">

- **for ...in**  
  <img width="508" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/c5aed036-14d4-452d-83bb-8c4469b77fab">
  > **객체의 key,value 모두 정상적으로 출력**해 줄 수 있음

### for(), forEach()

- for()

  - 초깃값부터 시작해서 증가 또는 감소하면서 조건에 부합하면 계속 순회하지만, **동기 방식이기 때문에 오류가 나면 오류가 난 위치 이후의 작업이 동작하지 않고 멈춤**
  - **break** 문을 사용할 수 있음
  - 장점
    - index를 순차적으로 증가시키거나 감소 시켜 loop 순서를 제어 가능함

  ```javascript
  // for
  console.log("for");
  for (let i = 0; i < arr.length; i++) {
    console.log(i, arr[i]);
  }
  ```

- forEach()

  - 배열의 각 요소에 대해 callback을 실행
  - **비동기 방식이기 때문에 오류가 난 이후에도 멈추지 않고 동작**
  - **break** 문을 사용할 수 없음
  - 장점
    - forEach 문은 "향상된 for문" 이라고 칭함
    - 가변적인 배열이나 리스트 크기를 구할 필요가 없어 **복잡한 반복문에 적합**하며, 인덱스를 생성하여 접근하는 **for문보다 수행 속도가 빠른 특징**이 있음
  - 단점
    - 반복문 내에서 배열이나 리스트 값을 변경하거나 추가할 수 없음 > readonly
    - 배열을 역순으로 탐색할 수 없음 > loop 순서를 제어 할 수 없기 때문

  ```javascript
  console.log("forEach()");
  arr.forEach(function (item, idx) {
    console.log(idx, item);
  });
  ```
