## 434. What is debouncing

<img src="https://blog.kakaocdn.net/dn/JF91e/btrCW1H0yB0/4zUwtPLlGE2wofdMY3TCV0/img.png"/>

이벤트 기반의 프로그래밍에서 사용되며 특히 사용자의 입력을 처리하거나 센서에서 데이터를 읽는 등의 작업에서 자주 사용된다.

예를 들어, 사용자가 버튼을 빠르게 여러 번 클릭하는 경우, 각 클릭마다 함수를 실행하면 시스템에 부담을 줄 수 있다.

이럴 때 디바운싱을 이용하면, 일정 시간 동안의 여러 입력을 하나로 간주하고 처리할 수 있다.

즉, '특정 시간 동안 추가 작업이 없으면 함수를 실행하라'는 개념이다.

자바스크립트에서의 디바운싱도 이와 마찬가지로 마지막으로 발생한 이벤트를 기준으로 특정 time이 지난 후 하나의 이벤트만 실행되도록 한다.

검색 버튼을 누르지 않아도 사용자가 입력한 값이 변경될 때마다 자동으로 검색 요청을 보내는 기능을 만든다고 한다면, 사용자가 검색어를 다 입력하지 않았을 때에도 요청이 보내지게 되므로 비효율적이다.

이러한 경우에 디바운싱을 적용하면 사용자의 입력이 끝나고 일정 시간이 지난 후에 검색 요청이 1번만 보내지므로 성능을 향상시킬 수 있다.

```js
const inputElement = document.querySelector('#인풋요소');
const DELAY = 500;
const handleChange = () => {
  console.log('요청!');
};

let timer;

inputElement.addEventListener('input', () => {
  if (timer) clearTimeout(timer);
  timer = setTimeout(handleChange, DELAY);
});
```

디바운싱을 구현하기 위해서는 setTimeout 함수와 time(기준 시간)이 필요하다.

이벤트가 발생할 때마다 timer 변수에 콜백 함수를 반환하는 setTimeout 함수를 값으로 할당한다.

time 이내에 새로운 이벤트가 발생할 때마다 타이머를 초기화 해준다고 이해하면 된다.

time 이내에 새로운 이벤트가 발생한다면 clearTimeout을 해줌으로써 콜백 함수는 실행되지 않으며, 발생하지 않으면 setTimeout은 콜백 함수를 실행하게 된다.

## 435. What is throttling?

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbKf2ea%2FbtrC4gxA6oV%2FuGJPEWHqyUWjkeZdNqbsi0%2Fimg.png"/>

조절 혹은 목을 조른다는 뜻으로, 주로 PC나 하드웨어의 성능 제한과 관련하여 많이 사용되는 개념이다.

기기의 CPU, GPU 등이 지나치게 과열되어 일정 온도 이상에 다다르면 기기에 손상을 일으키게 되는데, 이를 방지하기 위해 클럭이나 인가 전압을 제한하여 발열을 낮추는 기법이다.

자바스크립트에서의 쓰로틀링은 짧은 시간 내에 과도한 이벤트 실행으로 인해 발생하는 성능 저하를 막기 위해 일정한 time slice를 주기로 이벤트가 한 번만 실행되도록 제한한다.

쓰로틀링은 주로 성능 최적화에 사용되는데, 예를 들어 웹 페이지에서 스크롤 이벤트나 마우스 이동 이벤트와 같은 빈번하게 발생하는 이벤트에 대응하는 처리를 제한해서, 브라우저의 부하를 줄이는 데 사용된다.

예를 들어 스크롤 이벤트의 경우, 크롬 기준 2px 단위로 이벤트가 발생하게 된다.

쓰로틀링을 적용하면 좋은 예시로는 scroll, resize, drag, mouse 이벤트, 애니메이션 등이 있다.

예를 들어 스크롤 이벤트의 경우, 크롬 기준 2px 단위로 이벤트가 발생하게 된다.

사용자가 1초에 1000px의 스크롤을 하게 되는 경우에는 약 500번의 이벤트가 발생되므로 성능에 좋지 않을 수 있다.

이럴 때 쓰로틀링을 사용하면, 예를 들어 500밀리초에 한 번씩만 이벤트 처리를 수행하도록 한다면 2번의 이벤트만 발생하므로 브라우저의 부하를 크게 줄일 수 있다.

쓰로틀링은 디바운싱(debouncing)과 비슷한 개념이지만, 디바운싱이 '연속된 이벤트를 그룹화하여 단 한 번만 처리'하는 것에 반해, 쓰로틀링은 '일정 시간 동안 이벤트를 정해진 횟수만큼만 처리'한다는 차이가 있습니다.

```js
const DELAY = 500;
const handleScroll = () => {
  timer = null;
  console.log('스크롤!');
};

let timer;

window.addEventListener('scroll', () => {
  if (!timer) timer = setTimeout(handleScroll, DELAY);
});
```

쓰로틀링도 디바운싱과 마찬가지로 setTimeout 함수와 time(기준 시간)이 필요하다.

이벤트가 발생했을 때, timer 변수의 값이 비어있다면 콜백 함수를 반환하는 setTimeout 함수를 값으로 할당한다.

그리고 콜백 함수 내에서는 timer를 null 값으로 초기화 해준다.

이러한 방법으로 time 이내에 최대 1번의 콜백 함수만 실행하도록 보장하게 된다.
