## 불변성이란?

불변성(Immutability)란 사전적으로 값이나 상태를 변경할 수 없다는 의미입니다. 자바스크립트 엔진에는 콜 스택(call stack)과 메모리 힙(memory heap) 2개의 메모리 공간을 가지고 있는데, 콜 스택에는 원시 타입의 데이터들이 저장되고, 힙에는 참조 타입의 데이터가 저장됩니다. `원시 타입`은 콜 스택에 할당된 값이 그대로 저장되지만, `참조 타입`은 실제 값이 메모리 힙에 저장되고, 해당 메모리 힙의 주소를 콜 스택에 저장합니다. 

<img width="800" src="https://github.com/dev-junehee/CS_Study_for_Interview/assets/116873887/70bb1d5e-5b85-448e-b29b-9042d11f138b" />
<img width="1000" src="https://github.com/dev-junehee/CS_Study_for_Interview/assets/116873887/35a8f871-8d5d-4ddb-8252-bbbe9dfa16bf" />

<br /><br />

`원시 타입`의 데이터는 변수에 할당된 값을 변경할 경우(재할당), 기존의 값을 변경하는 것이 아니라 새로운 값을 추가해 해당 변수가 새로운 값의 주소를 가리킬 수 있도록 하는데, 이것이 바로 **‘불변성’** 입니다. 즉 불변성을 지킨다는 것은 **‘메모리 영역에서 값을 변경할 수 없도록 한다.’**는 의미입니다.

<img width="400" src="https://github.com/dev-junehee/CS_Study_for_Interview/assets/116873887/a27f8b75-9022-4ae3-a921-cd5d4c537992" />
<img width="400" src="https://github.com/dev-junehee/CS_Study_for_Interview/assets/116873887/36769c23-fb82-4e36-9fc3-3cfae3a48c9d" />
<img width="400" src="https://github.com/dev-junehee/CS_Study_for_Interview/assets/116873887/9534f07b-1ad4-4c7b-b547-9ff79d3877ee" />

*(더이상 사용하지 않는 데이터는 가비지 컬렉터에 의해 메모리에서 해제됩니다.)*

<br /><br />

반대로 `참조 타입`의 데이터는 값을 재할당 했을 때,<br />
처음 변수가 가리키고 있던 콜 스택의 주소가 새로운 값의 주소로 바뀌는 것이 아니라 메모리 힙에 있는 데이터 자체가 변경되어 **불변성이 유지되지 않습니다.**

<img width="" src="https://github.com/dev-junehee/CS_Study_for_Interview/assets/116873887/1cf2ecac-e396-4d7d-bb2b-99b444c85e72" />
<img width="500" src="https://github.com/dev-junehee/CS_Study_for_Interview/assets/116873887/749cab13-220e-4293-abe9-db0b456096a9" />


<br /><br />

## 불변성을 지켜야하는 이유는?

불변성을 지킨다는 것은 앞서 말했듯이 **‘메모리 영역에서 값을 변경할 수 없도록 한다.’** 는 것입니다.

리액트는 상태(state)의 값이 변경되면 렌더링이 일어납니다. 상태의 변화를 감지하기 위해서는 ‘얕은 비교’를 수행하는데, 얕은 비교란 이전 참조값과 현재 참조값을 비교하여 변화를 감지하는 것입니다. 즉, 리액트의 상태 변화 감지 기준은 ‘콜 스택의 주소값’ 입니다.

`원시 타입`의 경우 재할당이 일어나면 참조한 주소가 바뀌어 쉽게 변화를 감지할 수 있는 반면에 `참조 타입`은 콜 스택 주소값이 아닌 메모리 힙에 할당된 실제 데이터가 변경되기 때문에 상태 변경 감지가 어려워 개발자가 원하는 타이밍에 렌더링이 일어나지 않는 예외 상황들이 발생합니다. 이것이 바로 리액트에서 불변성을 유지해야 하는 이유입니다.

<br /><br />

## 어떻게 불변성을 지킬까?

`참조 타입`의 불변성을 지키기 위해서는 `참조 타입`의 값을 통째로 갈아주어 새로운 콜 스택 주소를 참조할 수 있도록 하는 것입니다. 대표적으로 전개 연산자(spread operator)를 사용하여 새로운 참조 값을 가지는 객체를 생성할 수 있도록 하는 방법이 있습니다.

> **불변성을 지키는 방법**
> 
- Spread Operator, map(), filter(), slice(), reduce() 등의 새로운 값을 반환하는 메소드 사용

> **불변성을 지켰을 때 좋은 점**
> 
- 얕은 비교(참조값 비교)로 인해 계산 리소스를 줄여 효율적인 상태 업데이트 가능
- 참조 타입의 경우 원본 데이터가 훼손될 가능성이 있기 때문에, 불변성을 지켜주어 예상치 못한 사이드 이펙트를 방지

```jsx
// 원시 타입
const [value, setValue] = useState('기본값');
setValue('새로운 값');

// 참조 타입
const [value, setValue] = useState(['기', '본', '값']);
setValue([...value, '새로운 값']);
```
