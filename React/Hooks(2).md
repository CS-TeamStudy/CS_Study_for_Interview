# REACT의 여러가지 HOOKS

## 1. useMemo()

useMemo를 사용하면 함수 컴포넌트 내부에서 발생하는 연산을 최적화할 수 있다.

그런데 버튼을 클릭할 때뿐만 아니라 인풋 내용이 수정될 때도 console.log가 호출되는 것을 확인할 수 있다. 인풋 내용이 바뀔 때는 세터함수를 다시 작동할 필요가 없는 것은 분명하다.

useMemo Hook을 사용하면 이러한 작업을 최적화할 수 있다. 렌더링하는 과정에서 특정 값이 바뀌었을 때만 useEffect를 실행하고, 원하는 값이 바뀌지 않았다면 이전에 연산했던 결과를 다시 사용하는 방식이다.

```javascript
import { useMemo, useEffect, useState } from 'react';

function App() {
  const [number, setNumber] = useState(1);
  const [isCu, setIsCu] = useState(true);

  // 1번 location
  // const location = {
  //   conveni: isCu ? "CU" : "GS25"
  // };

  // 2번 location
  const location = useMemo(() => {
    return {
      conveni: isCu ? 'CU' : 'GS25',
    };
  }, [isCu]);

  useEffect(() => {
    console.log('useEffect 호출!');
  }, [location]);

  return (
    <header className='App-header'>
      <h2>하루에 몇 끼 먹어요?</h2>
      <input
        type='number'
        value={number}
        onChange={(e) => setNumber(e.target.value)}
      />
      <hr />

      <h2>내가 있는 편의점은?</h2>
      <p>편의점: {location.conveni}</p>
      <button onClick={() => setIsCu(!isCu)}>Update</button>
    </header>
  );
}

export default App;
```

<br />
<br />

<br />
<br />

## 2. useCallback()

```javascript
import { useEffect, useState, useCallback } from 'react';

function App() {
  const [number, setNumber] = useState(1);
  const [isCu, setIsCu] = useState(true);

  // 1번 convenience
  // const getConveni = {
  //   conveni: isCu ? "CU" : "GS25"
  // };

  // 2번 convenience
  const getConveni = useCallback(() => {
    return {
      conveni: isCu ? 'CU' : 'GS25',
    };
  }, [isCu]);

  useEffect(() => {
    console.log('useEffect 호출!');
  }, [getConveni]);

  return (
    <header className='App-header'>
      <h2>하루에 몇 끼 먹어요?</h2>
      <input
        type='number'
        value={number}
        onChange={(e) => setNumber(e.target.value)}
      />
      <hr />

      <h2>내가 있는 편의점은?</h2>
      <p>편의점: {getConveni().conveni}</p>
      <button onClick={() => setIsCu(!isCu)}>Update</button>
    </header>
  );
}

export default App;
```

useMemo는 값을 캐시하는 개념이라면 이와 다르게 useCallback은 함수를 캐시하여 불필요한 계산과 렌더링을 방지하는 hook이다.

<br />
<br />

<br />
<br />

## 3. useMemo? useCallback?

- useMemo 함수는 메모이제이션된 값을 반환한다
- useCallback 함수는 메모이제이션 된 함수를 반환한다

<br />
<br />

<br />
<br />

# 4. useRef

JavaScript 를 사용 할 때에는, 우리가 특정 DOM 을 선택해야 하는 상황에 getElementById, querySelector 같은 DOM Selector 함수를 사용해서 DOM에 접근한다.

React를 사용하는 프로젝트에서도 가끔씩 DOM 을 직접 선택해야 하는 상황이 발생 할 때도 있다. 스크롤바 위치를 가져오거나 설정해야된다던지, 또는 포커스를 설정해줘야된다던지 등 정말 다양한 상황에서 DOM을 참조해야하는데, 그럴 때 React에서는 useRef 라는 Hook 함수를 사용한다.

```javascript
import React, { useRef, useEffect } from 'react';

function App() {
  // DOM 요소에 대한 참조
  const inputRef = useRef(null);

  useEffect(() => {
    // 컴포넌트가 마운트될 때 input 요소에 포커스
    inputRef.current.focus();
  }, []);

  return (
    <div>
      <input type='text' ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>포커스 주기</button>
    </div>
  );
}

export default App;
```

## 5. useReducer()

보통 상태를 업데이트 할 때에는 useState 를 사용해서 새로운 상태를 설정한다. 상태를 관리하게 될 때 useState가 아닌 useReducer를 사용할 수도 있다.

이 Hook 함수를 사용하면 컴포넌트의 상태 업데이트 로직을 컴포넌트에서 분리시킬 수 있다. 상태 업데이트 로직을 컴포넌트 바깥에 작성 할 수도 있고, 심지어 다른 파일에 작성 후 불러와서 사용 할 수도 있다.

### 4-1 reducer?

```javascript
function reducer(state, action) {
  // 새로운 상태를 만드는 로직
  // const nextState = ...
  return nextState;
}
```

reducer 에서 반환하는 상태는 곧 컴포넌트가 지닐 새로운 상태가 된다.

여기서 action 은 업데이트를 위한 정보를 가지고 있다.
주로 type 값을 지닌 객체 형태로 사용하지만, 꼭 따라야 할 규칙은 따로 없다.

---

### 4-2 useReducer의 사용법

```javascript
const [state, dispatch] = useReducer(reducer, initialState);
```

여기서 state 는 우리가 앞으로 컴포넌트에서 사용 할 수 있는 상태를 가리키게 되고, dispatch 는 액션을 발생시키는 함수이다.

그리고 useReducer 에 넣는 첫번째 파라미터는 reducer 함수이고, 두번째 파라미터는 초기 상태이다.

### 4-3 useReducer()를 활용한 카운터 함수

```javascript
import React, { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
}
function Counter() {
  const [number, dispatch] = useReducer(reducer, 0);

  const onIncrease = () => {
    dispatch({ type: 'INCREMENT' });
  };

  const onDecrease = () => {
    dispatch({ type: 'DECREMENT' });
  };

  return (
    <div>
      <h1>{number}</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
  );
}

export default Counter;
```

### 4-4 그래서 왜?

`useReducer()`는 컴포넌트의 상태 관리에 복잡한 로직이 필요한 경우에 유용하다.

특히 여러 개의 액션을 처리해야 하는 경우에 적합하다.
(하나의 동작에 여러 state가 가동될 때 등)

이를 통해 상태 관리 코드를 보다 예측 가능하고 유지보수하기 쉽게 만들 수 있다.
