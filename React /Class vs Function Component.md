## 클래스형 컴포넌트

#### 예시 코드
```jsx
import { Component } from 'react';

class Home extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "클래스형",
      content: "컴포넌트"
    }
  }

  render() {
    return(
        <div>
          {this.state.name}
          {this.state.content}
        </div>
    )
  }
}
```

### 선언 방식
- React에서 제공하는 `Component`를 상속받아 사용
- `Class` 키워드 사용
- 화면에 표시하기 위한 `render()` 메서드 사용
- `Props` 사용 시 `this` 키워드 사용
- `state`는 객체 형태로 사용, `this.setState` 함수를 통해 값을 변경
  
 ```jsx
onClick = () => {
  this.setState({
      price: price + 10;
  })
}
```

### 장점
- state와 LifeCycle API 사용이 가능합니다.
- 임의 메서드를 정의할 수 있습니다.

### 단점
- 코드 구성이 어렵고 복잡하며 재사용성이 낮습니다.
- this를 핸들링하기가 어렵습니다. *(this는 mutable!)*
- 코드 최적화가 어렵습니다.

<br />

## 함수형 컴포넌트

```jsx
import { useState } from 'react'

function Home(props) {
  const [state, setState] = useState('초기값');

  const onClickHandle = () => {
    setState('변경될 값');
  }

  return (
    <div>
      <h2>함수형 컴포넌트</h2>
      <h3>{props}</h3>
      <h4>{state}</h4>
    </div>
  )
}

export default Home;
```

### 선언 방식
- `render` 함수의 파라미터로 `props`를 전달받아 사용
- `return`문을 사용하여 `JSX` 작성
- `useState` 훅을 통해 `state`를 관리

### 장점
- 적은 코드로 간편한 선언이 가능하며 가독성이 좋습니다.
- 리랜더링 될 때의 값을 유지하며, props에 따른 렌더링 결과를 보장합니다. *(props와 state는 immutable!)*
- 클래스형 컴포넌트보다 메모리 자원 사용이 적습니다.
- 빌드한 결과물의 크기가 작습니다.

### 단점
- state와 LifeCycle API 사용이 불가합니다.<br />
→ 그러나 리액트 v16.8 업데이트 이후 리액트 훅(Hooks)을 사용할 수 있기 때문에 현재는 해결
    
<br />

## LifeCycle

모든 리액트 컴포넌트는 **생성(mount) → 업데이트(update) → 제거(unmount)의 생명주기**를 갖습니다.<br />
적절한 생명주기에 어떠한 작업을 처리해야 하는지 지정해줘야 불필요한 업데이트를 방지할 수 있습니다.

- **클래스형** : LifeCycle API 사용
- **함수형** : React Hooks 사용

<br />

## 마무리

현재 리액트 공식 문서에서는 **함수형 컴포넌트를 사용하는 것을 권장**하고 있습니다. 리액트 16.8버전 이후부터는 **훅(Hooks)**을 통해 함수형 컴포넌트에서도 클래스형 컴포넌트의 장점이었던 state와 LifeCycle을 컨트롤 할 수 있게 되었습니다. 그러나 클래스형 컴포넌트로 개발을 진행하고 유지보수를 하고 있는 프로젝트를 언제든 만날 수 있기 때문에 클래스형 컴포넌트의 기본적인 개념 정도는 알고 있으면 좋습니다.
