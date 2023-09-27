# React

## useLayoutEffect란 무엇인가?

- 데이터에 따라 DOM 조작이 필요, 이때 화면의 깜빡임을 없애기 위해 useEffect 대신 useLayoutEffect를 사용함
- 그렇다면 useEffect와 useLayoutEffect의 차이는 무엇일까?

  - useEffect
    <img width="508" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/b913122a-3ae3-46b9-a8ee-e928bf924f8e">

    - useEffect는 화면 렌더링이 완료된 후 혹은 어떤 값이 변경되었을 때 사이드 이펙트를 수행하는 Hook
    - 실행시점
      - useEffect로 전달된 함수는 layout과 paint가 완료된 후에 비동기적으로 실행
      - **DOM에 영향을 주는 코드가 있을 경우, 렌더시 화면의 깜빡임과 동시에 화면 내용이 달라지는 현상을 방지** 하기 위해 중요한 정보일 경우, 화면이 다 렌더되기 전에 동기화 필요
        > useLayoutEffect라는 훅이 나왔으며, 기능은 동일하되 실행 시점이 다름

  - useLayoutEffect
    <img width="556" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/deba5a90-f7df-4c21-9edb-d3d577c6f275">


    - useLayoutEffect는 useEffect와 동일하지만, 렌더링 후 layout과 paint 전에 동기적으로 실행됨
    - But, 로직이 복잡할 경우 **사용자가 레이아웃을 보는데까지 시간이 오래걸린다는 단점이 있어**, _기본적으로는 항상 useEffect 만을 사용하는 것을 권장_
    - 설령 DOM을 조작하는 코드가 존재하더라도, 사용자는 깜빡임을 보지 않음

- useEffect, useLayoutEffect를 사용시 찰나의 깜빡임의 차이만 존재함
  > 따라서 스크롤 위치를 찾거나 어떤 element의 스타일 요소를 변경하는 등 직접적으로 DOM을 조작하는 곳 제외하고는 useEffect를 사용하는 것을 추천
- 공식 문서에서도 useEffect를 먼저 사용한 후에, 문제가 생긴다면 그때 useLayoutEffect를 사용하는 것을 추천하고 있음

### 서버사이드에서의 useLayoutEffect

- Next.js 와 같이 서버 렌더링의 경우,
  자바스크립트가 모두 다운되기 전까지 useEffect, useLayoutEffect 모두 실행되지 않기 때문에 useLayoutEffect 사용시에 react 오류를 띄우게 됨
- useLayoutEffect는 페인트 전에 실행되기 때문에 **서버에서 렌더되는 화면과 클라이언트에서 렌더되는 화면이 다를 수 있음**

  > useLayoutEffect는 오직 클라이언트 사이드에서만 실행되어야 한다

  - useLayoutEffect 대체 방안

    1.  useEffect 사용

    ```javascript
    function MyComponent() {
      useEffect(() => {
        // ...
      });
    }
    ```

    2.  해당 컴포넌트를 lazy 하게 로드

    ```javascript
    function Parent() {
      const [showChild, setShowChild] = useState(false);

      // 클라이언트 사이드의 hydration 이후에 보여주기
      useEffect(() => {
        setShowChild(true);
      }, []);

      if (!showChild) {
        return null;
      }

      return <Child {...props} />;
    }

    function Child(props) {
      useEffect(() => {
        // ...
      });
    }
    ```
