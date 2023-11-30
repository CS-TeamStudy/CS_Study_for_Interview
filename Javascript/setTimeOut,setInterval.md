# Javascript

## setTimeout & setInterval

### setTimeout

1. setTimeout?

- 함수를 비동기적으로 실행하는 JS내장 함수
- 지정된 밀리초만큼 기다렸다가 실행되도록 설계되어 있음

2. setTimeout 함수의 구조

```javascript
let timerId = setTimeout(hello, [delay], [arg1], [arg2], ...)
```

- 첫번째 매개변수
  - _**hello**_
  - 실행할 콜백함수로 함수 또는 문자열 형태로 전달
  - \*문자열을 넣을수 있지만 함수를 넣는것 지향
- 두번째 매개변수
  - _**delay**_
  - 실행 전 대기 시간으로 단위는 밀리초(millisecond, 1000 millisecond = 1 second)이며 기본값은 0
- 그 이후 매개변수
  - _**arg1,arg2,...**_
  - 함수에 전달할 인수들
  - \*IE9 이하에서는 지원하지 않음

3. 사용 예시

```javascript
// 함수 전달 인자를 넘겨주지 않는 경우
function sayPotato() {
  alert("안녕하세요.");
}
setTimeout(sayPotato, 1000);

//  함수 전달 인자를 넘져주는 경우
function sayHi(who, phrase) {
  alert(who + " 님은, " + phrase);
}

setTimeout(sayPotato, 1000, "유감자", "감자입니다."); // 유감자 님은, 감자입니다.

// 문자열로 전달하는 경우
// 전달된 문자열로 함수를 만든다
setTimeout("alert('안녕하세요.')", 1000);
```

4. clearTimeout

- setTimeout을 호출 > timer identifier 반환
- clearTimeout함수에 timer identifier로 스케줄링 종료

```javascript
let timerId = setTimeout(...);
clearTimeout(timerId);
```

- 아래 이미지를 보면 브라우저 환경에선 타이머 식별자가 숫자라는 걸 알 수 있다!!
- **스케줄링에 관한 명세는 따로 존재하지 않기 때문에** \*다른 호스트 환경에선 타이머 식별자가 숫자형 이외의 자료형일 수 있음
  (참고로 Node.js에서 setTimeout을 실행하면 타이머 객체 반환)
  <img width="443" alt="image" src="https://github.com/group7temp/7-1-/assets/125563995/baefbccd-faf8-469e-adf3-7dbb2c414b60">
  <img width="487" alt="image" src="https://github.com/group7temp/7-1-/assets/125563995/1a9ea994-9d4f-45bf-95a4-28fa6ade199f">

5. 중첩 setTimeout

- 일정 간격을 두고 함수를 실행 할수 있음

```javascript
let timerId = setTimeout(function tick() {
  console.log("째깍");
  timerId = setTimeout(tick, 2000);
}, 2000);
```

- 중첩 setTimeout 사용 예시

```javascript
let delay = 5000;

let timerId = setTimeout(function request() {
  ...요청 보내기...

  if (서버 과부하로 인한 요청 실패) {
    // 요청 간격을 늘립니다.
    delay *= 2;
  }

  timerId = setTimeout(request, delay);

}, delay);
```

- 작업에 걸리는 시간에 따라 다음 작업을 유동적으로 계획할 수 있기 때문에 CPU 소모가 많은 작업을 주기적으로 실행하는 경우에도 setTimeout을 재귀 실행하는 방법이 유용함
- **중첩 setTimeout을 이용하는 방법은 지연 간격을 보장**하지만 **setInterval은 이를 보장하지 않는다**

### setInterval

3.setInterval?

- 함수를 비동기적으로 실행하는 JS내장 함수
- etTimeout과 동일한 문법을 사용하고 인수 역시 동일하다
- _**setTimeout이 함수를 단 한 번만 실행하는 것과 달리 setInterval은 함수를 주기적으로 실행함**_

```javascript
let timerId = setInterval(hello, [delay], [arg1], [arg2], ...)
// 함수를 중단하는 방식도 같다
clearInterval(timerId)
```

<img width="518" alt="image" src="https://github.com/group7temp/7-1-/assets/125563995/0e560c6e-4e8a-4851-874d-60073d677968">

### setInterval & 중첩 setTimeout

```javascript
let i = 1;
setInterval(function () {
  func(i++);
}, 100);

let i = 1;
setTimeout(function run() {
  func(i++);
  setTimeout(run, 100);
}, 100);
```

1. setInterval

- setInterval을 사용하면 func호출 사이의 지연 간격이 실제 명시한 간격(100ms)보다 짧아짐
- func을 실행하는 데 ‘소모되는’ 시간도 지연 간격에 포함하기 때문
- **func을 실행하는 데 걸리는 시간이 명시한 지연 간격보다 길 때** 어떤 일이 발생할까?
  - 이런 경우는 엔진이 func의 실행이 종료될 때까지 기다리고 func의 실행이 종료되면 엔진은 스케줄러를 확인하고, 지연 시간이 지났으면 다음 호출을 바로 시작함 > 따라서 함수 호출에 걸리는 시간이 매번 delay 밀리초보다 길면, 모든 함수가 쉼 없이 계속 연속 호출된다 무한하게!!!
    <img width="576" alt="image" src="https://github.com/group7temp/7-1-/assets/125563995/eb0a05b8-352b-45ef-b81b-411bb8b582b1">

2. 중첩 setTimeout

- 반면 중첩 setTimeout을 사용하면 명시한 지연(여기서는 100ms)이 보장됨
- 지연 간격이 보장되는 이유
  - 이전 함수의 실행이 종료된 이후에 다음 함수 호출에 대한 계획이 세워지기 때문
    <img width="607" alt="image" src="https://github.com/group7temp/7-1-/assets/125563995/382d8413-e3ef-4870-85c5-3c7ce3720089">

### 가비지 컬렉션과 setInterval & setTimeout

- etInterval이나 setTimeout에 함수를 넘기면, 함수에 대한 내부 참조가 새롭게 만들어지고 이 참조 정보는 스케줄러에 저장됨 **따라서 해당 함수를 참조하는 것이 없어도 setInterval과 setTimeout에 넘긴 함수는 가비지 컬렉션의 대상이 되지 않음**

```javascript
// 스케줄러가 함수를 호출할 때까지 함수는 메모리에 유지됩니다.
setTimeout(function() {...}, 100);
```

- setInterval의 경우는, clearInterval이 호출되기 전까지 함수에 대한 참조가 메모리에 유지 됨
- setInterval & setTimeout을 사용해 함수를 취소해야하는 이유
  - 외부 렉시컬 환경을 참조하는 함수가 있다고 가정한다면, **이 함수가 메모리에 남아있는 동안엔 외부 변수 역시 메모리에 남아있게 됨** 그런데 이렇게 되면 실제 함수가 차지했어야 하는 공간보다 더 많은 메모리 공간이 사용됩니다. 이런 부작용을 방지하고 싶다면 스케줄링할 필요가 없어진 함수는 아무리 작더라도 취소해야함!!

### 대기 시간이 0인 setTimeOut

- setTimeout(func, 0)이나 setTimeout(func)을 사용하면 setTimeout의 대기 시간을 0으로 설정하면 func을 ‘가능한 한’ 빨리 실행할 수 있음
- 이런 특징을 이용하면 현재 스크립트의 실행이 종료된 ‘직후에’ 원하는 함수가 실행될 수 있게 할 수 있음
- 스케줄링 메서드를 사용할 땐 명시한 지연 간격이 보장되지 않을 수도 있다는 점에 유의해야함
  - 아래와 같은 상황에서 브라우저 내 타이머가 느려지면 지연 간격이 보장되지 않음
    - CPU가 과부하 상태인 경우
    - 브라우저 탭이 백그라운드 모드인 경우
    - 노트북이 배터리에 의존해서 구동 중인 경우
