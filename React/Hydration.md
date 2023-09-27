# Hydration

![image](https://github.com/FC-MINI-4/attendance-front/assets/83483378/e1df6a36-3f0c-4b82-a7f4-b3d9c10d073f)

컴포넌트를 렌더링하고 이벤트 핸들러를 연결하는 프로세스를 Hydration이라고 함.

인터랙션 기능 및 이벤트 핸들러를 이용해 '말라있는' HTML에 물을 주는 것.

Hydration 후 애플리케이션은 인터랙티브하며 클릭 등 사용자 입력에 응답함.

## ttv(time to view) & tti(time to interact)

1. viewable한 html을 우선 브라우저에 전달 (Pre-rendering된 Html) -> ttv
2. 상호작용을 위해 .js 파일이 필요하다면 클라이언트는 추가적으로 파일을 요청 (번들링된 React 코드(JS)) -> tti

> ttv와 tti가 같지 않을 때 hydration 에러가 발생할 수 있다.

## Hydration 동작 방식

`ttv → hydrate → tti`

```
ReactDOM.hydrate(element, container[, callback])
```

render()는 지정된 DOM 요소의 하위에 추가하여 렌더링

hydrate()는 특정 컴포넌트에 대해 두번째 파라미터인 지정된 DOM 요소 하위로 Hydrate.
렌더링을 통해 새로운 웹페이지를 구성하는 것이 아니라 기존 DOM Tree에서 해당되는 DOM 요소를 찾아 정해진 자바스크립트 속성들만 적용.

## Hydration Error in NextJS

```javascript
function Hydration() {
  return (
    <main>
      <SSR />
      <CSR />
      <Always />
    </main>
  );
}
```

```javascript
function SSR() {
  // 브라우저 환경에서 실행이 된다면 렌더링 하지 않음
  if (typeof window !== 'undefined') return null;
  return <div style={{ color: 'red', fontSize: '5rem' }}>SSR</div>;
}
```

```javascript
function CSR() {
  // 컴포넌트가 브라우저에서 렌더링이 되면 상태가 변화하기 때문에 CSR.
  const [visible, setVisible] = useState(false);
  useEffect(() => setVisible(true), []);

  if (!visible) return null;
  return <div style={{ color: 'blue', fontSize: '5rem' }}>CSR</div>;
}
```

```javascript
function Always() {
  return <div style={{ color: 'green', fontSize: '5rem' }}>Always</div>;
}
```


<img width="1571" alt="image" src="https://github.com/FC-MINI-4/attendance-front/assets/83483378/1d687caa-f1c5-4d74-9808-6a0fa9ee05fc">

> 에러 발생!


## Why Error?

- Next.js의 hydrate 함수의 작동 방식 체크

1. 서버에서 받아온 DOM 트리와 자체적으로 렌더링한 트리를 비교한다.
2. 두 tree 사이의 diff를 얻어낸 뒤, 자체적으로(CSR) 렌더링 한 트리에 비교하면서 어떤 DOM이 어떻게 매칭되는지 이해한다.
3. 이해한 내용에 따라, CSR 동작을 실행한다.

- Hydrate는 HTML파일을 렌더트리가 아닌 버츄얼 돔과 비교한다.
CSR.tsx에서 선언된 useEffect() 훅이 실행되기 전의 상태와 비교한다. 즉 실제 비교가 되는 환경은 아래와 같다.

```javascript
// Server Side tree
<main>
  <SSR />
  <Always />
</main>

// Client Side tree
<main>
  <CSR />
  <Always />
</main> 
```

라고 생각 할 수 있지만. 

React Hydration은 
텍스트나 속성값은 비교하지 않는다. 결국 렌더링된 엘리먼트 타입과 순서만 비교한다

```javascript
// Server Side tree
<main>
  <div /> // SSR
  <div /> // ALWAYS
</main>

// Client Side tree
<main>
  <div /> // ALWAYS
</main> 
```

<img width="472" alt="image" src="https://github.com/FC-MINI-4/attendance-front/assets/83483378/eb689f44-6c0f-46bd-a8e1-edb0a4b32d02">


1. Hydration 과정에서 Server Side tree의 첫 번째 태그 속성이 Client Side Tree의 첫 번째 태그로 매칭이 된다.

2. 이후 CSR 과정에서의 `<CSR />`은 어트리뷰트가 보장이 된 채 렌더 된다.
