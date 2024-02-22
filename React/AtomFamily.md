# React Recoil

## AtomFamily

### what is AtomFamily?

- _family는_ 비슷한 기능을 하는 별개의 atom(혹은 selecor)가 필요한 경우 필요한 만큼 각각 선언하는 대신 하나의 형태를 선언해 놓고 팩토리처럼 언제든 새롭게 만들어 사용할 수 있는 기능

### Recoil의 atomFamily API

```javascript
const NameList = atom<Name[]>({
  key: "nameList",
 default: [],
  ...
})
```

- 위의 경우 Name에 변경이 일어날 때마다, nameList atom에 매번 새로운 배열이 할당 되어야하고, nameList를 이용해 여러개의 Namedmf 렌더링할 때 결국 별도의 memoization이 필요하게 된다

- 또 한 atom은 고유한 key를 갖는, **컴포넌트가 구독할 수 있는 가장 작은 단위의 상태값(원자값)이 되도록 구성하는 것이 이상적인 recoil의 형태**

- 따라서 위의 예시처럼 한 배열 안에 여러 개의 name을 관리하는 패턴은 작동엔 문제가 없으나 Recoil의 이상적인 설계에는 정확하게 들어맞지 않음

```javascript
const PotatoNameAtom = atom<Name>({
  key: "Potato",
  ...
})

const MilkyNameAtom = atom<Name>({
  key: "Milky",
  ...
})
```

- 하지만 위와 같이 동일한 형태의 atom을 여러개 생성하는것 또한 최적의 방법은 아님(같은 형태의 코드를 여러번 사용하는 것이 비효율적)

#### atomFamily API

- 이 경우에 atomFamily API를 사용 할 수 있음
- 동일한 형태의 atom을 생성해주는 팩토리 함수 제공(정확히 말하면, 팩토리 함수를 리턴하는 함수 리턴)
- 따라서 **atomFamily를 호출할 때마다 지정한 형식의 Atom을 생성**

```javascript
const attendancesAtomFamily = atomFamily<NameInfo, AttendancesId>({
  key: "attendancesAtomFamily",
  // atomFamily로 만들어질 Atom의 초기값
  default: (id) => ({
    id,
    isOpen: false,
    name: "",
  }),
})
```

- NameInfo: 생성될 Atom의 타입
- AttendancesId: default 값이 파라미터를 받는 함수인 경우, 그 파라미터의 타입

```javascript
const [myName, setMyName] = useRecoilState(modalsAtomFamily("myName"));
const [hisName, sethisName] = useRecoilState(modalsAtomFamily("hisName"));
```

#### Atom key 관리

- atomFamily를 이용하면 동일한 형식의 Atom을 쉽게 여러 번 만들어낼 수 있지만, 또 다른 문제점이 있습니다. atomFamily는 Atom을 생성해주는 팩토리 함수일 뿐이기에, 지금까지 이 atomFamily를 이용해서 어떤 Atom들을 생성했는지는 전혀 알 수 없음

- atomFamily와 동시에 **atomFamily를 통해 생성된 Atom의 key를 별도로 관리**할 필요가 있음

```javascript
export const AttendancesIdsAtom = atom<AttendencesId[]>({
  key: "AttendencesIdsAtom",
  default: [],
})
```

```javascript
const setAttendebcesIdsAtom = useSetRecoilState(AttendencesIdsAtom);

/* 1. atomFamily로 츌석부 Atom 생성 */
const myName = AttendencesAtomFamily("myName");

/* 2. 생성한 Atom의 key를 별도의 배열에 넣기 */
setAttendencesIdsAtom((prev) => [...prev, AttendencesIdsAtom]);
```
