## Context API

리액트의 Context는 props를 사용하지 않고 컴포넌트 사이에서 필요한 데이터를 공유할 수 있도록 도와주는 기능입니다.<br />
주로 전역적(global)으로 사용하는 데이터를 다루지만, 무조건 전역적일 필요는 없습니다.

> **Context lets the parent component make some information available to any component in the tree below it—no matter how deep—without passing it explicitly through props.**

<br />

### 사용하는 이유

리액트는 단방향 **데이터 흐름(one-way data flow)** 을 가지는 라이브러리입니다.<br />
리액트에서는 상위 컴포넌트에서 하위 컴포넌트로 단방향의 데이터 바인딩만 가능하기 때문에,<br />
깊은 위치에 존재하는 컴포넌트에 데이터를 전달해야 할 경우 여러 컴포넌트를 걸쳐 props를 전달하게 되어 **Props Drilling** 현상이 발생합니다.

```jsx
function App() {
  return <FirstComponent value="Learn React Context" />;
}

function FirstComponent({ value }) {
  return <SecondComponent value={value} />;
}

function SecondComponent({ value }) {
  return <ThirdComponent value={value} />;
}

function ThirdComponent({ value }) {
  return <FourthComponent value={value} />;
}

.
.
.

function TwentyFiveComponent({ value }) {
  return <div>Topic! : {value}</div>;
}
```

Props Drilling 현상이 발생하는 자체는 큰 문제가 되지 않지만, 단순 props의 전달이 5~6개 이상의 컴포넌트를 넘어간다면 어떨까요? **컴포넌트의 깊이가 깊어질수록 props를 추적하기 힘들고, 유지보수가 어려워집니다.**

<br />

### 사용법

1. 리액트 패키지에서 createContext 함수를 불러온 후 Context를 만들어 줍니다.

```jsx
import { createContext } from 'react';

export const ThisIsContext = createContext();
```

2. 만들어진 Context 객체 안에 포함되어 있는 Provider 컴포넌트에 공유하고자 하는 값을 value로 설정하면 자식 컴포넌트들에서 props를 사용하지 않고 value 값에 접근할 수 있습니다.

```jsx
import { ThisIsContext } from './context.js'
import { Children1 } from './Children1.jsx'

export default function App() {
  return (
    <ThisIsContext.Provider value="Learn React Context">
      <Children1 />
    </ThisIsContext.Provider>
  );
}
```

3. Provider로 감싸진 자식 컴포넌트에서는 useContext 훅을 이용해 value 값을 조회하여 사용합니다.

```jsx
import { useContext } from 'react';
import { ThisIsContext } from './context.js'

export function Children1() {
  const value = useContext(ThisIsContext);

  return (
    <div>{value}</div>
  )
};
```

4. Provider로 감싸지 않고 사용할 경우, createContext() 함수에 인자로 기본값을 넣어 사용합니다.

```jsx
import { createContext } from 'react';

export const ThisIsContext = createContext("Learn React Context");
```

```jsx
import React from 'react';
import { Children1 } from './Children1.jsx'

export default function App() {
  return (
    <Children1 />
  );
}
```

<br />

### 장점과 단점

- 별도 라이브러리 설치 없이 전역적으로 사용 가능한 값을 관리할 수 있습니다.
- Provider 하위에서 context를 구독하는 모든 컴포넌트는 Provider의 value prop가 바뀔 때마다 다시 렌더링 됩니다

<br />

## Context API vs Redux

### Redux란

리덕스는 애플리케이션 전체에 대한 중앙 저장소 역할을 하며 action 이라는 이벤트를 사용해 애플리케이션의 상태를 예측 가능한 방식으로 업데이트를 할 수 있도록 도와주는 라이브러리입니다.

### Context가 상태관리가 아닌 이유

상태 관리란 시간이 지남에 따라 상태가 변경되는 방식이며 **1. 초기 값 저장 2. 현재 값 읽기 3. 값 업데이트** 3가지를 모두 충족한 경우 상태관리라고 칭합니다. 리액트의 `useSate`, `useReducer` 훅의 경우가 3가지를 모두 충족하는 상태 관리의 좋은 예이며, Context API는 단순히 값을 여러 컴포넌트 사이에서 공유하는 방식이라고 할 수 있습니다.

### 언제 사용하나요?

단순히 Props Drilling을 피하기 위함이 목적이라면 Context API를 사용하는 것이 좋습니다.

그러나 여러 위치에서 많은 상태 값이 존재할 때, 그리고 이 값을 업데이트 하는 로직이 복잡할 때, 상태가 변경되는 로직의 시각화가 필요할 때 Redux와 같은 상태 관리 라이브러리를 사용할 수 있습니다.
