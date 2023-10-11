# Class Component & Pure Components

## 언제 함수형 컴포넌트가 아닌 Class 컴포넌트를 사용해야 하는가?

Hooks가 도입된 이후(React 16.8 버전부터), React에서는 항상 Function 컴포넌트(Function Component)를 Class 컴포넌트 대신 사용하는 것이 권장된다.

왜냐하면 Function 컴포넌트에서는 클래스 컴포넌트에서만 사용할 수 있던 상태(state), 라이프사이클 메서드, 그리고 다른 기능들을 사용할 수 있기 때문이다.

이 중 최근까지 Class 컴포넌트를 사용해야했던 두 가지 예시를 살펴보자

---

### 1. Function 컴포넌트의 대체물이 없었던 에러 바운더리

![image](https://github.com/FC-MINI-4/attendance-front/assets/83483378/3c9d728c-e263-40d6-8109-ea6780aff038)

UI의 일부분에 존재하는 자바스크립트 에러가 전체 애플리케이션을 중단시켜서는 안된다. React 사용자들이 겪는 이 문제를 해결하기 위해 React 16에서는 에러 바운더리(“error boundary”)라는 새로운 개념이 도입되었다.

에러 바운더리는 하위 컴포넌트 트리의 어디에서든 자바스크립트 에러를 기록하며 깨진 컴포넌트 트리 대신 폴백 UI를 보여주는 React 컴포넌트이다. 렌더링 도중 생명주기 메서드 및 그 아래에 있는 전체 트리에서 에러를 잡아낸다.

```javascript
<ApiErrorBoundary>
    <CommentFetcher>
      <CommentContainer />
    </CommentFetcher>
  </ApiErrorBoundary>

...

class ApiErrorBoundary {
	// 하위 트리에서 throw된 error를 받습니다.
	static getDerivedStateFromError(error: Error): State {
	  if (이 Error Boundary에서 처리할 수 없는 에러 코드) {
			return {
				shouldHandleError: false,
				// 여기서 처리 할 수 없는 에러라면 render 단계에서 rethrow 하여 상위 에러 바운더리에서 처리하도록 합니다.
				shouldRethrow: true,
				error,
			};
	  }
		return {
			shouldHandleError: true,
			shouldRethrow: false,
			serializedPagewebError,
		};
	}

	render() {
		if(this.state.shouldRethrow) {
			throw this.state.error;
		}
		if(!this.state.shouldHandleError) {
			return this.props.children
		}
		if(미로그인 에러 코드) {
			return (
				<AuthError />
			)
		}
		if(네트워크 에러 코드) {
			// ApiErrorBoundary와 중복되는 에러 처리 코드입니다.
			// Fetcher위에 ApiErrorBoundary가 누락 혹은 제외된 경우
			return (
				<NetworkError onClickRetry={() => this.setState({ shouldHandleError: false})} />
			)
		}
		...
		return (
			<UnknownError onClickRetry={() => this.setState({ shouldHandleError: false})} />
		)
	}
}

```
https://fe-developers.kakaoent.com/2022/221110-error-boundary/

<br />
<br />

하지만 에러 바운더리 라이브러리의 등장으로 더 이상 Class Component를 사용해야하는 필수불가결한 상황은 사라졌다.

```javascript
'use client';

import { ErrorBoundary } from 'react-error-boundary';

<ErrorBoundary fallback={<Error />}>
  <ExampleApplication />
</ErrorBoundary>;
```

---

### 2. 이전 버전의 React에서는 컴포넌트가 상태(state)나 라이프사이클 메소드가 필요한 경우에는 Class 컴포넌트를 사용해야했다.

```javascript
class CountUp extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        <p>카운트: {this.state.count}</p>
        <button onClick={() => this.increment()}>증가 버튼</button>
      </div>
    );
  }
}
```

Class 컴포넌트는 내부 상태(state)를 가질 수 있으며, 이 상태를 변경할 때 setState 메서드를 사용하여 UI를 업데이트할 수 있다.

그러나 현재는 Function 컴포넌트에서 상태 관리를 위해 useState Hook을 사용할 수 있으므로 Class 컴포넌트를 사용해야할 이유는 사라졌다.

***

## Pure Components
![image](https://github.com/FC-MINI-4/attendance-front/assets/83483378/5b31f094-099c-4c55-b467-696519347cda)
Pure Components란 동일한 상태(state)와 속성(props)에 대해 항상 동일한 출력을 렌더링하는 컴포넌트를 의미한다.

함수 컴포넌트에서는 React.memo()를 사용하여 Pure Component를 구현할 수 있다. Pure Component는 이전 props와 새로운 props를 비교함으로써 불필요한 다시 렌더링을 방지할 수 있다.

React.memo()를 사용한 일반적인 선언 방식은 다음과 같다.

```javascript
const PureComponent = memo(SomeComponent, arePropsEqual?);
```

***

아래 코드는 EmployeeProfile 컴포넌트가 동일한 props를 사용하면 다시 렌더링되지 않도록 React의 memo 함수를 사용하는 예제이다.

EmployeeProfile 컴포넌트는 부모 컴포넌트인 EmployeeRegForm으로부터 name과 email이라는 두 개의 props를 전달받는다.

```javascript
import { memo, useState } from 'react';

  const EmployeeProfile = memo(function EmployeeProfile({ name, email }) {
    return (<>
          <p>Name:{name}</p>
          <p>Email: {email}</p>
          </>);
  });

  export default function EmployeeRegForm() {
    const [name, setName] = useState('');
    const [email, setEmail] = useState('');
    return (
      <>
        <label>
          Name: <input value={name} onChange={e => setName(e.target.value)} />
        </label>
        <label>
          Email: <input value={email} onChange={e => setEmail(e.target.value)} />
        </label>
        <hr/>
        <EmployeeProfile name={name}/>
      </>
    );
  }
```

`EmployeeProfile` 컴포넌트는 React.memo를 사용하여 메모이제이션되며, 이로 인해 동일한 props로 인한 재렌더링을 방지할 수 있다. 

코드 내에서 email prop은 전달되지 않았으므로 email prop이 변경되더라도 EmployeeProfile은 다시 렌더링되지 않는다.
