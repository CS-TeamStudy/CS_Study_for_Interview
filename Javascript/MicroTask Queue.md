## 이벤트 루프와 태스크 큐

이벤트 루프(Event Loop)는 자바스크립트 런타임 환경에서 콜 스택(Call Stack)과 태스크 큐(Task Queue, Callback Queue)를 관찰하여 콜 스택이 비었을 때 태스크 큐에 쌓여있는 태스크를 쌓인 순서대로(FIFO) 콜 스택으로 옮겨 실행하는 역할을 합니다.

태스크 큐는 두 가지 종류로, **마이크로태스크 큐(Microtask Queue)와 매크로태스크 큐(Macrotask queue)** 가 있으며 둘 중 **마이크로태스크 큐의 우선순위가 더 높습니다.** 마이크로태스크는 다른 이벤트 핸들러나 렌더링 작업, 그리고 매크로태스크가 실행되기 전에 처리되기 때문입니다.

즉, 이벤트 루프는 콜 스택이 비었을 때 먼저 마이크로태스크 큐에 대기하고 있는 함수를 가져와 실행하고 이후 마이크로태스크 큐가 비었을 때 매크로태스크 큐에 대기 중인 함수를 실행합니다.

<img src="https://uploads.disquscdn.com/images/9466d8aa53fc5b3e63a92858a94bb429df02bbd20012b738f0461343beaa6f90.gif?w=600&h=272" />

<br />

## 마이크로태스크 큐 vs 매크로태스크 큐

마이크로태스크 큐와 매크로태스크 큐는 각각 어떤 API를 사용하는지 등에 따라 쌓이는 위치가 달라지게 됩니다.

#### 마이크로태스크 큐 (MicroTask Queue, Event Queue)

- 현재 수행 중인 작업이 끝난 뒤에 이어서 실행될 작업(콜백) 입니다.
- 프로미스의 후속 처리 메서드인 `.then`, `.catch`, `.finally`
- DOM Tree의 변경을 감지하는 기능을 제공하는 `MutationObserver()`
- Node.js의 `process.nextTick()` 메서드
  
#### 매크로태스크 큐 (MacroTask Queue, Job Queue)

- 콜 스택의 작업이 끝난 후에 실행되는 작업입니다. 즉 콜 스택의 작업이 끝나지 않으면 실행되지 않는 작업으로 보통 시간이 오래 걸리는 UI 업데이트 및 Blocking Operations 같은 작업을 수행합니다.
- 자바스크립트의 `setTimeout`, `setInterval`, `setImmediate`
- Node.js의 `requestAnimationFrame`
- 그 외 I/O 연산이나 UI 렌더링 등…

<br />

```jsx
setTimeout(() => console.log(1));

Promise.resolve()
	.then(() => console.log(2))
	.then(() => console.log(3));

console.log(4);
```

 ```jsx
4
2
3
1
```

- 일반적인 동기 코드 순서에 따라 `console.log(4)`가 매크로태스크 큐에 들어가 가장 먼저 출력됩니다.
- 프로미스는 마이크로태스크 큐에서 대기하므로 우선순위를 가져 `setTimeout()`보다 먼저 출력됩니다.
- `setTimeout()`는 매크로태스크 큐에 들어가기 때문에 가장 마지막에 출력됩니다.

<br />

## 마이크로태스크 큐

### 사용 목적

대부분의 개발자가 마이크로 태스크를 직접적으로 사용할 일은 거의 없습니다. 마이크로 태스크는 현대 브라우저 기반의 자바스크립트에서 고도로 특화된 기능으로, **사용자의 컴퓨터에서 발생하는 수많은 태스크 중 다른 것보다 우선해서 실행할 코드를 예약할 수 있는 기능**입니다. 때문에 남용하게 되면 **성능 문제가 발생**할 수 있습니다.

마이크로 태스크는 자바스크립트의 실행 맥락의 주 본문이 종료되었으나 **다른 이벤트 처리기나, setTimeout, setInterval, 기타 콜백이 호출되기 전에 결과를 내는 작업을 수행해야할 때 사용**할 수 있습니다. 

### 사용 방법

- `queueMicrotask(func)`

```jsx
const callback = () => {
  console.log("일반 콜백 함수를 실행했습니다.");
}

const urgentCallback = () => {
  console.log("***긴급 콜백 함수를 실행했습니다!!!!!");
}

const doWork = () => {
  let result = 0;
  
  queueMicrotask(urgentCallback);
  
  for (let i = 1; i <= 5; i++) {
    result += i;
  }
  
  return result;
};

console.log("시작합니다.");
setTimeout(callback, 0);
console.log(`1부터 5까지 더한 결과는 ${doWork()}입니다.`);
console.log("종료합니다.");
```
 
```
시작합니다.
1부터 5까지 더한 결과는 15입니다.
종료합니다.
***긴급 콜백 함수를 실행했습니다!!!!!
일반 콜백 함수를 실행했습니다.
```

- `console.log(”시작합니다.”)` : 가장 먼저 실행되어 콘솔에 문자열이 바로 출력됩니다.
- `setTimeout(callback, 0)` : setTimeout이 호출되어 매크로태스크 큐로 이동합니다.
- `console.log(`1부터 5까지 더한 결과는 ${doWork()}입니다.`)` : doWork()가 호출되어 내부에 있던 queueMicrotask로 감싸진 urgentCallback 함수가 마이크로태스크 큐로 이동합니다. 이후 for문이 실행되어 결과를 출력합니다.
- `console.log("종료합니다.")` : 마지막으로 실행되어 콘솔에 문자열이 출력되고 콜 스택이 비어지게 됩니다.
- 콜 스택이 비었으므로 이벤트 루프가 태스크 큐에 쌓여있는 태스크를 콜 스택으로 이동시킵니다.
- 우선순위가 높은 마이크로 태스크 큐에 쌓인 queueMicrotask의 콜백 함수 `urgentCallback`이 실행되어 콘솔에 출력되고, 마이크로태스크 큐는 빈 상태가 됩니다.
- 마이크로태스크 큐가 비어있으므로 매크로태스크 큐에 쌓인 setTimeout의 콜백 함수 `callback`이 실행됩니다.

<br />

## 정리
- 태스크 큐에는 마이크로태스크 큐와 매크로태스크 큐가 있습니다.
- 마이크로태스크 큐의 우선순위가 매크로태스크 큐의 우선순위보다 높아 먼저 실행됩니다.
- 마이크로태스크 큐에는 `Promise` 메서드와, `MutationObserver`, `process.nextTick()` 가 있습니다.
- 매크로태스크 큐에는 `setTimeout`, `setInterval`, `setImmediate`, `requestAnimationFrame`, I/O 연산이나 UI 렌더링이 있습니다.
- (드물지만) 마이크로태스크를 사용하고 싶을 때에는 `queueMicrotask()`을 사용할 수 있습니다.
