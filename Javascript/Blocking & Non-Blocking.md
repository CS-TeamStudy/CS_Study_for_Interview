### Blocking & Non-Blocking

**블로킹(Blocking)** 이란 Node.js 프로세스에서 자바스크립트가 아닌 작업(non-JavaScript operation)의 실행이 완료될 때까지 추가적인 자바스크립트가 실행되지 않고 기다리는 것을 의미합니다. 자바스크립트가 아닌 작업이란 파일에서 데이터 읽기/쓰기, 네트워크 요청 만들기, DB에서 데이터 읽기 등과 같은 작업 등이 있습니다.

**논블로킹(Non-Blocking)** 이란 이전 작업이 완료될 때까지 기다리지 않고, 다음 작업을 수행할 수 있도록 동작하는 것입니다.

쉽게 말하면, 블로킹은 자신의 작업을 진행하다가 다른 주체자의 작업이 시작되면 해당 작업이 끝날 때까지 기다린 후 다시 자신의 작업을 시작하는 것입니다. 예를 들어, 카페에서 손님들이 줄을 서있을 때 앞 손님이 커피를 주문하고 주문한 커피를 받을 때까지 뒷 손님이 아무것도 하지 못하는 채로 기다려야 한다면 해당 현상을 블로킹이라고 표현할 수 있습니다. 즉 동기적인 작업에 따라 이후 작업들이 **차단된 상태**입니다. (논블로킹은 그 반대!)

<br /> 

### Sync & Async VS Blocking & Non-Blocking

동기와 비동기, 블로킹과 논블로킹은 이론상 비슷하게 느껴질 수 있습니다. 그러나 동기와 비동기는 코드의 실행 순서와 관련이 있고, 블로킹과 논블로킹은 코드의 실행이 다른 코드의 실행에 영향을 끼치는지와 관련 있습니다.

- **동기 (Synchronous)** : 코드가 순차적으로 실행
- **비동기 (Asynchronous)** : 코드가 순차적으로 실행되지 않음
- **블로킹 (Blocking)** : 코드의 실행이 다른 코드의 실행을 차단(block)함
- **논블로킹 (Non-Blockign)** : 코드의 실행이 다른 코드의 실행을 차단하지 않음(non-block)

<br />

동기와 비동기, 블로킹과 논블로킹은 유사하지만 다른 개념입니다.<br />
그래서 프로그래밍을할 때 이두 개념이 조화되어 사용될 수 있습니다.

- **Sync Blocking** (동기 + 블로킹)
- **Async Blocking** (비동기 + 블로킹)
- **Sync Non-Blocking** (동기 + 논블로킹)
- **Async Non-Blocking** (비동기 + 논블로킹)

  ![image](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/116873887/b9f97fce-e90a-43a5-b984-3069c4db6957)