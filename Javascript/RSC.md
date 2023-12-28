# RSC


## Before RSC

```js
function Home({ memberId }) {
  return (
    <MemberDetails memberId={memberId}>
      <MoneyBalance memberId={memberId}></MoneyBalance>
      <PaymentHistory memberId={memberId}></PaymentHistory>
    </MemberDetails>
  );
}
```

부모 컴포넌트인 `<MemberDetails>`와 자식 컴포넌트인 `<MoneyBalance>`, `<PaymentHistory>` 모두 다른 유저의 정보가 필요하고 필요한 정보는 서버 API를 호출하여 받아와야 한다고 가정하자.


여러분은 어떤 방법으로 필요한 유저 정보를 받아올 것인가?

> 1. 모든 정보를 부모 컴포넌트에서 하나의 거대한 API로 호출하여 자식으로 내려준다.
> 2. 컴포넌트에 필요한 API를 각 컴포넌트에서 호출한다.

---

<br />

1️⃣ **첫** 번째 방법의 경우 클라이언트에서 서버로 요청하는 API 요청수를 줄일 수 있지만 그로 인해 부모와 자식 컴포넌트가 <span style="color:pink">결속되고 유지보수가 어려워지게 된다.</span> <br />
만일 컴포넌트의 구성이 바뀌거나 자식 컴포넌트가 다른 컴포넌트로 이동되는 경우, 해당 API를 다른 컴포넌트에서도 호출해줘야 하며 불필요한 정보를 <span style="color:pink">over-fetching</span>하게 된다.


![image](https://github.com/livable-final/client/assets/83483378/8d073b48-680c-47d0-84f0-c0361d8c07d2)


2️⃣ **두** 번째 방법은 각 컴포넌트가 렌더링 될 때 필요한 데이터만 가져와 보여줄 수 있다는 장점이 있지만 <span style="color:#ffd33d">high latency</span>를 가진 클라이언트부터의 서버 요청은 늘어나게 된다. <br /> 또한 부모 컴포넌트는 렌더링 된 후 필요한 데이터를 받아오기 시작하고 이 과정이 끝나기 전까지 자식 컴포넌트의 렌더링과 API 호출 또한 지연된다. <br /> 결국 연속된 <span style="color:#ffd33d">client-server API 요청과 중첩된 컴포넌트 내 API 호출 지연</span>으로 인한 waterfall은 사용자 경험을 떨어트릴 수 있다.

---

## What is RSC

**위와 같이 클라이언트 컴포넌트에서의 비동기 data fetching은 client-server waterfall을 야기하고 성능을 저하시키는 원인이 된다.**

이를 해결하기 위해 `서버 컴포넌트`가 등장했다.

![image](https://github.com/livable-final/client/assets/83483378/063bfa59-a0cd-4c15-ae77-35b424a59d57)

리액트 서버 컴포넌트는 용어 그대로 서버에서 동작하는 리액트 컴포넌트이다. 

서버 컴포넌트를 사용하면 컴포넌트 렌더링을 클라이언트가 아닌 서버에서 수행할 수 있다. 서버에서 Render를 수행하기 때문에 API를 통한 데이터 요청의 `latency를 줄일 수 있고`, 클라이언트에서의 연속된 API 호출을 제거하여 `client-server waterfall를 막을 수 있다.`

<br />
<br />

잠깐! latency가 어쩌구 waterfall이 어쩌구?

1. Low Latency
> 서버 컴포넌트를 사용하면 서버에서 렌더링을 수행하므로, 초기 렌더링 시에 필요한 데이터를 서버에서 가져와 클라이언트로 전송할 수 있다. 이로써 레이턴시가 감소하고 페이지 로딩이 빨라진다.

2. Protect Client-Server Waterfall 

> 클라이언트에서 렌더링하는 경우, 초기 렌더링을 완료한 후에 추가적인 데이터를 가져오기 위해 클라이언트에서 서버로 API 요청을 계속 보내야 할 수 있다. 이 때 서버 컴포넌트를 사용하면 초기 렌더링 시 필요한 데이터를 서버에서 가져오기 때문에, 추가적인 API 호출이 필요 없어진다.

<br />
<br />

## Features of RSC

### 1. Can Access Server Resource

서버 컴포넌트는 서버에서 동작하기 때문에 데이터베이스, 파일 시스템 그리고 인터널 서비스 같은 서버 사이드 데이터 소스에 직접 접근할 수 있다.

```js
// Note.server.js - 서버 컴포넌트
import fs from 'react-fs';
import db from 'db.server';

function Note(props) {
  // NOTE: loads *during* render, w low-latency data access on the server
  const note = db.notes.get(props.id); // 데이터베이스 접근
  const noteFromFile = JSON.parse(fs.readFile(`${id}.json`)); // 파일 접근

  if (note == null) {
    // handle missing note
  }
  return (/* render note here... */);
}
```

> 서버 컴포넌트를 통해 db, fs에 접근한다!


### 2. Zero-bundle size 

```js
// ClientComponent.client.jsx - 클라이언트 컴포넌트 = 기존의 리액트 컴포넌트

import marked from 'marked'; // 35.9K (11.2K gzipped)
import sanitizeHtml from 'sanitize-html'; // 206K (63.3K gzipped)

function ClientComponent({text}) {
  const html = sanitizeHtml(marked(text));
  return (/* render */);
}
```

프론트엔드 앱을 개발하다 보면 무수한 라이브러리를 사용하게 된다. 직접 구현하기 까다로운 라이브러리들을 하나 둘 추가하다 보면 번들 사이즈가 늘게 되고, 퍼포먼스에 악영향을 끼치기 마련이다. 

요즘은 많은 라이브러리들이 트리 셰이킹을 지원하고 필요에 따라 code splitting으로 렌더링에 필요한 번들 사이즈를 최대한 줄일 수 있지만 궁극적으로 번들 사이즈가 늘어나는 것은 막을 수 없다.

<br />

하지만 이와 다르게 `서버 컴포넌트 코드`는 브라우저에 다운로드되지 않고 서버에서 미리 렌더링 된 static content를 전달하기 때문에 패키지를 추가해도 번들 사이즈에 영향을 끼치지 않는다.

```js
// ServerComponent.server.jsx - 서버 컴포넌트
import marked from 'marked'; // ZERO IMPACT on bundle size
import sanitizeHtml from 'sanitize-html'; // ZERO IMPACT on bundle size

function ServerComponent({ text }) {
  const html = sanitizeHtml(marked(text));
  return (/* render */);
}
```

### 3. Auto Code-Splitting

클라이언트 컴포넌트에서는 `React.lazy`와 `dynamic import`를 사용하여 렌더링에 필요한 컴포넌트를 동적으로 로드해왔다.

```js
// 부모 컴포넌트가 렌더링 된 이후 lazy-loading을 시작한다.
const OldPhotoRenderer = React.lazy(() => import('./OldPhotoRenderer.js'));
const NewPhotoRenderer = React.lazy(() => import('./NewPhotoRenderer.js'));

function ClientComponent(props) {
  
  if (FeatureFlags.useNewPhotoRenderer) {
    return <NewPhotoRenderer {...props} />;
  } else {
    return <OldPhotoRenderer {...props} />;
  }
}
```

이는 웹 퍼포먼스를 끌어올릴 수 있으나, 극명한 단점이 존재한다.

```
1️⃣ lazy loading이 필요한 컴포넌트마다 일일이 `React.lazy와` `dynamic import`를 적용

2️⃣ 부모 컴포넌트가 렌더링 된 이후 로딩을 시작하기 때문에 화면에 보이기 전 어느 정도의 딜레이가 존재
```

**서버 컴포넌트는 이러한 단점을 두 가지 방식으로 해결한다.**

```
1️⃣ 서버 컴포넌트에서 import 되는 모든 클라이언트 컴포넌트를 code splitting 포인트로 간주하기 때문에 더 이상 React.lazy로 메뉴얼 하게 명시하지 않아도 된다.

2️⃣ 서버에서 미리 필요한 컴포넌트를 선택하기 때문에 클라이언트는 렌더링 프로세스 초기에 번들을 다운로드할 수 있다.
```


```js
// ServerComponent.server.js - Server Component

import NewPhotoRenderer from './NewPhotoRenderer.client.js';
import OldPhotoRenderer from './OldPhotoRenderer.client.js';

function ServerComponent(props) {
  if (FeatureFlags.useNewPhotoRenderer) {
    return <NewPhotoRenderer {...props} />;
  } else {
    return <OldPhotoRenderer {...props} />;
  }
}
```


## SSR 그리고 RSC
(이제는 `최근에는..` 이라는 수식어가 필요가 없다.) 많은 프론트엔드 개발자들은 서버 사이드 렌더링 프레임워크를 경험해보았을 것이다. <br /> 덕분에 서버 컴포넌트 또한 많은 관심을 받았지만 `서버`가 들어간 이름과 서버에서 렌더링 된다는 점 때문에 서버 사이드 렌더링과 혼동되고 서로를 대체할 수 있는 기술로 혼동하기 쉽다.

그러나 서버 컴포넌트와 서버 사이드 렌더링은 서버에서 렌더링 된다는 유사점이 있지만 `해결하고자 하는 문제점`이 다르다.

1️⃣
<details>
<summary><span style="color:#86B6F6">서버 컴포넌트의 코드는 클라이언트로 전달되지 않는다</span>.</summary>
클라이언트는 <strong>최종적으로 렌더링된 결과물</strong>만 받아와 화면에 표시
</details>

<br />

<details>
<summary><span style="color:#FF90BC">하지만 서버 사이드 렌더링의 모든 컴포넌트의 코드는 자바스크립트 번들에 포함되어 클라이언트로 전송된다.</span> </summary>
서버에서 렌더링된 HTML을 클라이언트에 전송 후, 렌더링에 사용된 모든 컴포넌트의 자바스크립트 코드가 자바스크립트 번들에 포함되어 클라이언트로 전송된다.
</details>

<br />

2️⃣ 
![image](https://github.com/livable-final/client/assets/83483378/cebe0d42-e637-45bf-b260-806de0c2b761)

![image](https://github.com/livable-final/client/assets/83483378/0343419a-cc65-4115-a825-958e6ff9e90b)
> 서버에서 전달받은 컴포넌트

<details>
<summary><span style="color:#86B6F6">서버 컴포넌트는 클라이언트 상태를 유지하며 refetch 될 수 있다. </span></span></summary>

서버 컴포넌트는 HTML이 아닌 <strong>특별한 형태</strong>로 컴포넌트를 전달하기 때문에 필요한 경우 state, 포커스, 인풋 입력값 같은 클라이언트 상태를 유지하며 여러 번 데이터를 가져오고 리렌더링하여 전달할 수 있다.
</details>

<br />

▶ <span style="color:#FF90BC">하지만 SSR의 경우 HTML로 전달되기 때문에 새로운 refetch가 필요한 경우 HTML 전체를 리렌더링 해야 하며 이로 인해 클라이언트 상태를 유지할 수 없다.</span>




## 마치며

`서버 컴포넌트`는 클라이언트와 함께 사용될 수 있고 필요에 의해 자유롭게 추가나 마이그레이션 될 수 있다. 또한 서버 컴포넌트는 서버 사이드 렌더링과 유사한 점이 있으나 대체제가 아니며 사용자 경험을 향상을 위해 서버 사이드 렌더링과 함께 서로 보완하여 사용될 수 있다.


React 개발을 하며 모든 컴포넌트를 서버 컴포넌트로 구성할 수 없지만, **클라이언트 컴포넌트와 서버 컴포넌트를 적재적소에 배치하여** 다양한 이점으로 사용자 경험뿐만이 아니라 개발자 경험을 증진시키는 것이 중요허다!
