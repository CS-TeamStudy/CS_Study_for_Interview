## useState of React Hooks

리액트 16.8버전 이후부터는 훅(Hooks)을 통해 함수형 컴포넌트에서도 클래스형 컴포넌트의 장점이었던 state와 LifeCycle을 컨트롤 할 수 있게 되었습니다.
그 중에서도 useState는 **상태(state)**를 관리할 수 있는 훅으로, 가장 기본적이고 많이 사용하는 훅이라고 할 수 있습니다. 

- **기본 형태**
    
    ```jsx
    const [state, setState] = useState(initialState);
    ```
    
    - `initialState`
        - 초기 상태 값입니다. 모든 데이터 타입을 사용할 수 있습니다.
        - 함수로 지정할 경우 초기화 함수로 처리되며, 파라미터가 없어야 합니다.
    - `[state, setState]`
        - 구조 분해 할당을 통해 사용할 state의 변수명을 지정합니다. (ex. `[name, setName]`)
        - state는 현재 상태 값을 의미합니다.
        - setState는 상태 값을 다른 값으로 업데이트하고 리렌더링을 일으키는 함수입니다.

- **주의**
    
    Strict 모드에서는 혹시 모를 오류를 찾기 위해 초기화 함수를 두 번 호출합니다.
    개발 전용 동작으로 프로덕션에는 전혀 영향을 끼치지 않습니다. 호출 중 하나의 결과는 무시됩니다.
    

- **적용**
    
    ```jsx
    import { useState } from 'react';
    
    function MyComponent() {
    	const [name, setName] = useState('junehee');
    
    	const clickHandle = () => {
    		setName('siwoo');
    	}
    	
    	return (
          <>
            <div>제 이름은 {name} 입니다.</div>
    		  	<button onClick={clickHandle}>click!</button>
          </>
    			
    	)
    }
    export default MyComponent;
    ```
    
    - 최상위 컴포넌트에서 `useState` 함수를 호출합니다. (조건문, 반복문 등에서는 호출할 수 없습니다.)
    - 이벤트 핸들러가 동작하기 이전의 `name`은 사용자가 처음 제공한 초기값 `‘junehee’` 입니다.
    - 이벤트 핸들러를 작동시키면 `name`은 `setName` 함수에 의해 값이 `‘siwoo’`로 변경됩니다.
    - 리액트는 변경된 상태를 저장하고, 새로운 값으로 구성 요소를 리렌더링하여 UI를 업데이트 합니다.
        
        ![useState.gif](https://prod-files-secure.s3.us-west-2.amazonaws.com/90d2508a-fd1f-49f6-a365-9cc2f05a0da3/f18b5826-0f06-4f87-95f8-7300416ee2c6/useState.gif)
        

- **심화**
    
    useState는 이전 상태를 기반으로 업데이트 할 수 있습니다.
    
    ```jsx
    import { useState } from 'react';
    
    function MyComponent() {
    	const [age, setAge] = useState(12);
    
    	const clickHandle = () => {
    		setAge(age + 1);
    		setAge(age + 1);
    		setAge(age + 1);
    	}
    	
    	return (
          <>
            <div>제 나이는 {age}살 입니다.</div>
    		  	<button onClick={clickHandle}>click!</button>
          </>
    			
    	)
    }
    export default MyComponent;
    ```
    
    - 이벤트 핸들러에는 3번의 `setAge` 함수를 호출합니다.
    - 이벤트 핸들러가 호출 되었을 때, `age` 값은 얼마일까요?
    - **정답!?!?!?!**
        
        `13!` 코드가 실행 중인 상태에서는 state가 변경되지 않기 때문입니다.
        
        ![12to13.gif](https://prod-files-secure.s3.us-west-2.amazonaws.com/90d2508a-fd1f-49f6-a365-9cc2f05a0da3/f1086aa0-7bb1-40cd-8f89-0261fc1b292d/12to13.gif)
        
        이럴 경우에는 `보류 상태(pending state)`를 사용하여 `다음 상태`를 계산합니다.
        
        ```jsx
        const clickHandle = () => {
        		setAge(a => a + 1);
        		setAge(a => a + 1);
        		setAge(a => a + 1);
        }
        ```
        
        - 첫 번째 `setAge` 함수에서는 보류 상태값으로 기본 값인 `12`를 가져와 `12 + 1`을 계산합니다.
        - 두 번째 `setAge` 함수에서는 보류 상태값으로 `13(12 + 1)`을 가져와 `13 + 1`을 계산합니다.
        - 세 번째 `setAge` 함수에서는 보류 상태값으로 `14(13 + 1)`을 가져와 `14 + 1`을 계산합니다.
        - 보류 상태는 사용 중인 state 변수명의 첫 글자를 지정하는 것이 일반적이나 더 직관적이고 명확하다고 생각하는 이름이 있다면 개발자가 자유롭게 지정할 수 있습니다.
            
            ![12to15.gif](https://prod-files-secure.s3.us-west-2.amazonaws.com/90d2508a-fd1f-49f6-a365-9cc2f05a0da3/b44ec8f5-09a7-42cf-8709-b05adafd5a74/12to15.gif)
