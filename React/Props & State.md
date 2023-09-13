# React

## props와 state의 차이

![image](https://github.com/1017yu/this-is-money/assets/83483378/441b9a4f-37d6-4929-8bff-29e3de01e8e1)

### props

**props**는 "properties"의 줄임말로, 컴포넌트가 외부에서 받는 데이터를 의미한다.

즉, 부모 컴포넌트로부터 자식 컴포넌트로 전달되는 데이터로서, props는 읽기 전용이므로 컴포넌트 내부에서 직접 수정할 수 없다.

대신, 부모 컴포넌트에서 값을 변경하여 자식 컴포넌트가 새로운 값을 받게 할 수 있다.

props는 컴포넌트 간에 데이터를 전달하는 데 사용된다.

---

### state

반면 **state**는 컴포넌트 내부에서 관리되는 데이터를 의미한다.

이는 컴포넌트 자체가 가지고 있는 데이터라고 일컫을 수 있다.

state는 컴포넌트의 상태를 변경하거나 업데이트할 때 사용된다.

state는 읽기와 쓰기 모두 가능하며, setState() 함수를 사용하여 값이 변경된다.

---

### props로 state 전달하기

부모 컴포넌트에서 state 값을 자식 컴포넌트로 props로 전달할 수 있다.

이 경우, 자식 컴포넌트에서는 전달받은 props를 사용하여 state 값을 사용할 수 있다.

이를 통해 부모 컴포넌트에서 state 값을 관리하고, 자식 컴포넌트에서 해당 값을 사용하면서도 자식 컴포넌트는 해당 state 값을 직접 변경하지 않도록 할 수 있다.

```javascript
import React, { useState } from 'react';

function Parent() {
  const name = 'CU';
  const name = 'GS25';
  const name = 'MINISTOP';
  const name = '7/11';
  const name = 'Emart24';

  const [count, setCount] = useState('0');

  return <Child name={name} count={count} setCount={setCount} />;
}

function Child(props) {
  return (
    <div>
      <p>Name: {props.name}</p>
      <p>Count: {props.count}</p>
      <button onClick={() => props.setCount(props.count + 1)}>Increment</button>
    </div>
  );
}
```

