# ECMA script 2023

ECMAScript 2023 spec이 나온 걸 아시나요?
주요 내용으로는

- Array find from last
- Hashbang Grammar
- Symbols as WeakMap keys
- <b>Change Array by Copy</b>

이중에서 4번째, 새롭게 추가된 배열 매소드에 대해서 알아보겠습니다!

새롭게 추가된 메소드들은 데이터를 변경하지 않고 복사본을 만들고 해당 복사본을 변경한다는 특징이 있습니다.

해당 메소드들은 Chrome 110, Safari 16.3, Node.js 20, Deno 1.31 이상에서 사용가능합니다.

# Mutation and side effects

알고리즘 문제를 풀 때 항상 겪는 문제가 있습니다.

> 원본이 바뀌던가, 복사본이 나오던가?

sort, reverse, splice 메소드 배열의 원본을 변경합니다.
다른 메소드들, 예를 들어 concat, map, filter 등은 배열의 복사본을 만든 다음 해당 복사본에 대해 작업을 수행하죠?

원본을 변경하는 작업을 수행하는 것은 side effect이며 코드 다른 곳에서 이 원본배열을 참조한다면 예기치 않은 동작을 일으킬 수 있습니다.

```js
const pets = ['howoo', 'berry', 'mango'];
const reversedPet = pets.reverse();
console.log(reversedPet);
// ['mango', 'berry', 'howoo']
console.log(pet);
// ['mango', 'berry', 'howoo']

// 예상치 못한 에러
console.log('첫번째 반려동물은?', pets[0]);
// 첫번째 반려동물은? mango

console.log(Object.is(pets, reversedPet));
// true
```

# Mutating arrays and React

과거에는 메모리를 조금이라도 아껴야 했기에 원본을 변경하는 것이 오히려 좋은 선택이였을 수 있습니다.
그러나 현재는 메모리가 넘쳐나기 떄문에 원본놔두고 복사본을 변경하는 방식이 선호됩니다.

그리고 이러한 변화에는 리액트의 유행과도 연관이 있습니다.
리액트는 원본을 변경할 경우 재렌더링이 되지 않습니다.

```jsx
import { useState } from 'react';

export default function App() {
  const [pets, setPets] = useState(['howoo', 'berry', 'mango']);

  return (
    <>
      <pre>{JSON.stringify(pets)}</pre>
      <button onClick={() => setPets((prev) => prev.reverse())}>
        reverse
      </button> // 재랜더링 x
    </>
  );
}
```

재랜더링을 위해서는 배열을 복사한 다음 복사본을 변경하고 이를 새 state로 설정해야 합니다.

```jsx
import { useState } from 'react';

export default function App() {
  const [pets, setPets] = useState(['howoo', 'berry', 'mango']);

  return (
    <>
      <pre>{JSON.stringify(pets)}</pre>
      <button onClick={() => setPets((prev) => [...prev].reverse())}>
        reverse
      </button>;
    </>
  );
}
```

개발자 입장에서 각 매서드가 원본을 변경하는지를 알아야 하고 복사하는 법도 알아야 하는 비효율이 발생합니다.

# New methods change by copy

위와 같은 문제를 해결하기 위해 새로운 메소드들이 추가되었습니다.
`toSorted`, `toReversed`, `toSpliced`, `with` 는 자동으로 원본을 복사하고 이후의 작업결과물을 리턴합니다.

1. toSorted

```js
const pets = ['howoo', 'berry', 'mango'];
const sortedPets = pets.toSorted();

console.log(sortedPets);
// ['berry', 'howoo', 'mango']
console.log(pets);
// ['howoo', 'berry', 'mango']
```

2. toReversed

```js
const pets = ['howoo', 'berry', 'mango'];
const reversedPets = pets.toReversed();

console.log(reversedPets);
// ['mango', 'berry', 'howoo']
console.log(pets);
// ['howoo', 'berry', 'mango']
```

3. toSpliced

```js
const pets = ['howoo', 'berry', 'mango'];
const splicedPets = pets.toSpliced(0, 1, 'cherry');
// 참고 array.splice(start[, deleteCount[, item1[, item2[, ...]]]])

console.log(splicedPets);
// ['cherry', 'berry', 'mango']
console.log(pets);
// ['howoo', 'berry', 'mango']
```

4. with

```js
const pets = ['howoo', 'berry', 'mango'];
// 망고를 체리로 변경
pets[2] = 'cherry';

console.log(pets);
// ['howoo', 'berry', 'cherry'];
```

```js
// with 사용
const updatedPets = pets.with(2, 'cherry');

console.log(updatedPets);
// ['howoo', 'berry', 'cherry']
console.log(pets);
// ['howoo', 'berry', 'mango']
```

```js
// with 사용
const pets = ['howoo', 'berry', 'mango'];
const updatedPets = pets.with(2, 'cherry');

console.log(updatedPets);
// ['howoo', 'berry', 'cherry']
console.log(pets);
// ['howoo', 'berry', 'mango']
```

나머지 3개의 메소드보다 `with`라는 명칭이 직관적이지는 않습니다.

# Usage in React

```jsx
import { useState } from 'react';

export default function App() {
  const [pets, setPets] = useState(['howoo', 'berry', 'mango']);

  return (
    <>
      <pre>{JSON.stringify(pets)}</pre>
      <button onClick={() => setPets((prev) => prev.toReversed())}>
        reverse
      </button>
    </>
  );
}
```
