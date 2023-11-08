# JavaScript

## error

### What is the error object?

1. error object

- 런타임 시에 발생하는 오류에 대한 객체

2. error object가 필요한 이유

- 런타임시에 발생하는 오류에 대해 적절하게 **예외 처리를 하고 핸들링** 할 수 있어야 우리가 작성한 애플리케이션이 **정상적으로 동작**할 수 있음

3. error object 구성요소

- Message
  - 오류의 원인 또는 설명에 대한 정보를 포함한 오류에대한 메세지
- Name

  - 오류의 유형을 식별하는 데 사용되는 에러명
  - JS 내장 error type

    - EvalError
      - 전역 함수 eval()에서 발생하는 오류의 인스턴스를 생성함
      - 문자열을 코드로 인식하게 해주는 Eval()
      - 현재는 잘 발생하지 않지만 하위호환성을 위해 남아있음
    - InternalError

      - JavaScript 엔진의 내부에서 오류가 발생했음을 나타내는 오류 인스턴스를 생성함

      ```javascript
      "too many switch cases", (swich case의 수가 너무 많음)
      "too many parentheses in regular expression", (정규표현식에 너무 많은 괄호가 있음)
      "array initializer too large", (배열 초기화 값이 너무 큼)
      "too much recursion". (너무 많은 재귀 호출)

      function cookies(x) {
        if (x >= 1000000000000) return;
        // 어떤 코드
        cookies(x + 1);
      }

       console.log('내가만든', cookies(0));
      // InternalError: too much recursion(너무 많은 재귀 호출)
      ```

    - RangeError

      - 숫자 변수나 매개변수가 유효한 범위를 벗어났음을 나타내는 오류 인스턴스를 생성합니다.

      ```javascript
      function check(postSeason) {
        if (!(postSeason >= 5 && postSeason <= 1)) {
          throw new RangeError("The argument must be between 5 and 1");
        }
      }
      try {
        check(6);
      } catch (error) {
        if (error instanceof RangeError) {
          console.error("postSeason 진출 실패!");
        }
      }
      ```

      <img width="544" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/7a697086-b630-4c2c-bea1-21421c6a845b">

    - ReferenceError

      - 잘못된 참조(**현재 범위에 존재하지 않는(또는 아직 초기화되지 않은) 변수가 참조될 때**)를 했음을 나타내는 오류 인스턴스를 생성합니다.

    - SyntaxError
      - eval() 이 코드를 분석하는 중 잘못된 구문(**문법적으로 잘못된 코드를 해석하려고 시도할 때**)을 만났음을 나타내는 오류 인스턴스를 생성합니다.
      - 가장 많이 만나는 오류 `console.log("안녕하세요" ` 라고 했을경우 우리는 SyntaxError를 만날수 있다.
    - TypeError
      - 변수나 매개변수가 유효한 자료형이 아님을 나타내는 오류 인스턴스를 생성합니다.

4. error Handling

- try...cathch 구문을 통해 우리는 에러를 핸들링 할 수 있다

  <img width="545" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/279b3685-cdca-43e3-b75b-a8b76d1ad207">

- try…catch 동작 알고리즘

  1. 먼저, try {...} 안의 코드가 실행
  2. 에러가 없다면, try 안의 마지막 줄까지 실행되고, catch 블록은 건너뜀
  3. 에러가 있다면, try 안 코드의 실행이 중단되고, catch(err) 블록으로 제어 흐름이 넘어감

  - 변수 err(아무 이름이나 사용 가능)는 무슨 일이 일어났는지에 대한 설명이 담긴 에러 객체를 포함합니다.

  ```javascript
  function footballPlayer(name) {
    let player = ["손흥민", "이강인", "김민재"];
    if (name === "손흥민") {
      return months[0];
    } else {
      throw "누구냐 넌"; // 여기서 throw 키워드 사용
    }
  }

  try {
    // 시도할 명령문
    newName = footballPlayer("김하성"); // 예외가 발생할 수 있는 함수
  } catch (e) {
    newName = "unknown";
    console.error(e); // 오류 처리기에 예외 객체 전달
  }
  ```

  <img width="560" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/4e9874a7-7a2d-4566-bde0-1681b6f3fb00">

  - try: 이 명령문은 코드 블록의 오류를 테스트하는 데 사용
  - catch: 이 문은 오류를 처리하는 데 사용
  - throw: 이 문은 사용자 정의 오류를 만드는 데 사용
  - finally: 이 문은 결과에 관계없이 try 및 catch 후에 코드를 실행하는 데 사용

    - 에러가 없는 경우: try 실행이 끝난 후
    - 에러가 있는 경우: catch 실행이 끝난 후

    ```javascript
    try {
       ... 코드를 실행 ...
    } catch(e) {
       ... 에러 핸들링 ...
    } finally {
       ... 항상 실행 ...
    }
    ```
