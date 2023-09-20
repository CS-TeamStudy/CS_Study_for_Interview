## Custom Hook?
리액트 애플리케이션에서 상태 관리, 부수 효과 처리와 같은 로직을 **함수 형태**로 재사용하기 위한 방법이다. 즉 특정 상태를 `useState`로 정의하고, 이 상태를 변경시킬 함수들을 객체로 담아 리턴하여 `캡슐화`하는 과정이다.
`useState`, `usEffect`와 같은 훅은 커스텀 훅이 아닌 내장 훅라고 한다.

## Why?
커스텀 훅을 사용하면 코드 **재사용성**을 향상시키고, 컴포넌트 간에 공통된 로직을 **추상화**할 수 있다. 이로써 코드의 가독성을 높이고 유지 보수를 용이하게 할 수 있다.

## 예시
커스텀 훅의 개념을 처음 접하는 사람에게 위와같은 설명은 하나도 도움이 되지 않는다.
커스텀 훅을 이해하는 가장 좋은 방법은 난이도가 낮은 훅을 사용해보면서 직접 필요성을 느끼고 자신만의 커스텀 훅을 만들어 보는 것이다.

**주의사항!** 커스텀 후크 함수이름은 use로 시작해야하며 내부에 useEffect를 사용할 경우 항상 clean up을 생각해야 한다.

### 1. useToggle: boolean 상태와 그 상태를 변경하는 함수 캡슐화

#### 커스텀 훅을 사용하지 않은 경우
```ts
import { useState } from 'react';

export default function App() {
  const [isKor, setIsKor] = useState(true);
  const [isDark, setDark] = useState(false);

  const toggleKor = () => {
    setIsKor((prev) => !prev);
  };
  const toggleDark = () => {
    setDark((prev) => !prev);
  };

  return (
    <div>
      <div>
        <div>{isKor ? '한국어' : 'English'}</div>
        <button onClick={toggleKor}> 한글 ↔ English</button>
      </div>
      <div>
        <div>{isDark ? '다크' : '라이트'}</div>
        <button onClick={toggleDark}>Dark ↔ Light</button>
      </div>
    </div>
  );
}

```

#### 커스텀 훅을 사용한 경우
```ts
import { useCallback, useState } from 'react';

export default function useToggle(initialState = false) {
  const [state, setState] = useState(initialState);
  const toggleState = useCallback(() => setState((prev) => !prev), []);
  
  return { state, toggleState };
}
```
<br/>

```ts
import useToggle from './useToggle';

export default function App() {
  const { state: isKor, toggleState: toggleKor } = useToggle(true);
  const { state: isDark, toggleState: toggleDark } = useToggle();
  return (
    <div>
      <div>
        <div>{isKor ? '한국어' : 'English'}</div>
        <button onClick={toggleKor}> 한글 ↔ English</button>
      </div>
      <div>
        <div>{isDark ? '다크' : '라이트'}</div>
        <button onClick={toggleDark}>Dark ↔ Light</button>
      </div>
    </div>
  );
}

```


#### 2. useFetch: GET요청과 관련된 모든 상태(서버데이터, 에러, 로딩)를 캡슐화

```ts
import { useEffect, useState } from 'react';

type FetchData<T> = {
  data?: T;
  error?: Error;
  loading: boolean;
};

export default function useFetch<T>(url: string): FetchData<T> {
  const [data, setData] = useState<T>();
  const [error, setError] = useState<Error>(); // 에러핸들링은 api따라 달라질 수 있음
  const [loading, setLoading] = useState<boolean>(false);

  useEffect(() => {
    const abortController = new AbortController();
    const signal = abortController.signal;

    const getData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url, { signal });
        if (!response.ok) { // 에러핸들링은 api따라 달라질 수 있음
          throw new Error('error while data fetching');
        }
        const processedData: T = await response.json();
        setData(processedData);
      } catch (error) {
        setError(error as Error);
      } finally {
        setLoading(false);
      }
    };
    getData();

    // 클린업
    return () => {
      abortController.abort();
    };
    
  }, [url]);
  return { data, error, loading };
}

```
<br/>

```ts
import useFetch from './useFetch';

type TodoType = {
  completed: boolean;
  id: number;
  title: string;
  userId: number;
};

export default function App() {
  const {
    data: todos,
    error,
    loading,
  } = useFetch<TodoType[]>('https://jsonplaceholder.typicode.com/todos');

  if (error) {
    return (
      <>
        <div>에러 이름 : {error.name}</div>
        <div>에러 메세지 : {error.message}</div>
        <div>에러 스택 : {error.stack}</div>
      </>
    );
  }
  if (loading) {
    return <div>loading</div>;
  }
  return (
    <ul>
      {todos?.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}

```

#### 3. useDebounce: 디바운스 결과 값을 캡슐화
```ts
import { useState, useEffect } from 'react';

export default function useDebounce(value: string, delay: number) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(timer);
    };
  }, [value, delay]);

  return debouncedValue;
}

```
<br/>

```ts
import { useState } from 'react';
import useDebounce from './useDebounce';

const DEBOUNCE_TIME_IN_MS = 300;

export default function App() {
  const [inputValue, setInputValue] = useState('');
  const debouncedValue = useDebounce(inputValue, DEBOUNCE_TIME_IN_MS);
  return (
    <>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
      <span>{debouncedValue}</span>
    </>
  );
}

```
