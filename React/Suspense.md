# Suspense?

Suspense는 리액트 16.6ver에서 업데이트된 기능으로 하위 항목이 로드될 때까지 대체 컴포넌트를 먼저 렌더링 시켜 화면에 표시할 수 있는 내장 기능입니다.

```jsx
import { Suspense } from 'react';

<Suspense fallback={<Loading />}>
  <SomeComponent />   // children
</Suspense>
```

- `children` : 렌더링을 하려는 실제 UI. children의 렌더링이 일시 중시되면 Suspense는 fallback으로 전환됩니다.
- `fallback` : 실제 UI 대신 렌더링할 대체 UI. React Node 대부분이 허용되지만 실제로는 로딩 스피너 또는 스켈레톤과 같은 가벼운 자리 표기를 나타냅니다. children의 렌더링이 일시 중단되면 fallback으로 자동 전환되고 데이터가 준비되면 다시 children으로 전환됩니다. 렌더링하는 동안 fallback이 일시 중단될 경우 가장 가까운 부모 Suspense를 활성화 합니다.

<br />

### 1. 콘텐츠가 로드되는 동안 대체 표시

Suspense는 하위 항목에 필요한 코든 코드와 데이터가 로드될 때까지 폴백을 통해 대체 컴포넌트를 표시합니다.

```jsx
import { Suspense } from 'react';
import Albums from './Albums';
import Loading from './Loading';

function ArtistPage({ artist }) {
  return (
    <>
      <h1>{artist.name}</h1>
      <Suspense fallback={<Loading />}>
        <Albums artistId={artist.id} />
      </Suspense>
    </>
  );
}

export default ArtistPage;
```

```jsx
function Loading() {
  return <h2>Loading...</h2>;
}
```

<br />

### 2. 콘텐츠를 한 번에 함께 공개

Suspense는 기본적으로 내부에 포함하고 있는 전체 트리를 단일 단위로 처리합니다. 만약 구성 요소 중 하나에서 일부 데이터가 일시 중지된다면 전체 구성 요소가 함께 로딩 표시로 대체되며, 모든 항목이 준비를 마쳤을 때 동시에 화면에 표시됩니다.

```jsx
<Suspense fallback={<Loading />}>
  <MyName />
  <MyContact>
    <EmailCard />
  </MyContact>
</Suspense>
```

더불어 구성 요소가 꼭 Suspense의 직계 하위 요소로 존재할 필요는 없습니다. 가장 가까운 상위 Suspense를 참조합니다.

```jsx
<Suspense fallback={<Loading />}>
  <Details artistId={artist.id} />
</Suspense>

function Details({ artistId }) {
  return (
    <>
      <Biography artistId={artistId} />
      <Panel>
        <Albums artistId={artistId} />
      </Panel>
    </>
  );
}
```

<br />

### 3. 로드 시 중첩된 콘텐츠 표시

구성 요소의 로드가 일시 중지되면 가장 가까운 상위 Suspense 참조하기 때문에 Suspense를 중첩하여 사용할 수 있습니다. 중첩 Suspense를 사용할 경우 어떤 요소들이 동시에 표시되어야 하는지 등의 로딩 순서를 조정할 수 있습니다. 그러나 모든 구성요소 각각에 Suspense를 사용하는 것은 권장하지 않습니다. Suspense를 사용자에게 경험하게 하려는 로딩 순서보다 세분화하여 사용할 수 없습니다.

```jsx
<Suspense fallback={<BigSpinner />}>
  <Biography />
  <Suspense fallback={<AlbumsGlimmer />}>
    <Panel>
      <Albums />
    </Panel>
  </Suspense>
</Suspense>
```

- `Biography`가 아직 로드되지 않은 경우 `BigSpinner`가 전체 콘텐츠 영역에 대신 표시됩니다.
- `Biography`의 로드가 완료되면 `BigSpinner`가 `Biography` 콘텐츠로 대체됩니다.
- `Albums`가 아직 로드되지 않은 경우 `Albums`와 `Panel` 영역에 `AlbumsGlimmer`가 대신 표시됩니다.
- `Albums`의 로드가 완료되면 `AlbumsGlimmer`가 `Panel`과 `Albums` 콘텐츠로 대체됩니다.

<br />

### 4. 새로운 콘텐츠가 로드되는 동안 오래된 콘텐츠 표시

검색 기능에서는 검색 결과를 가져오는 동안 SearchResults 구성 요소를 일시 중단됩니다. "a"를 입력하고 결과를 기다린 다음 "ab"로 편집합니다. "a"에 대한 결과는 로드 폴백으로 대체됩니다.

```jsx
import { Suspense, useState } from 'react';
import SearchResults from './SearchResults';

function Search() {
  const [query, setQuery] = useState('');

  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={query} />
      </Suspense>
    </>
  );
}

export default Search;
```

<br />

### 5. React.lazy와 함께 사용 (router)

SPA의 단점은 바로 사용할 필요가 없는 컴포넌트까지 모두 불러오기 때문에 렌더링 시간이 오래 걸린다는 것입니다. 리액트는 lazy 기능을 통해 컴포넌트를 동적으로 import 할 수 있기 때문에 초기 렌더링 시간을 줄여줄 수 있습니다. 이 때 Suspense를 함께 사용할 수 있습니다.

```jsx
import { lazy, Suspense } from 'react';
import { Routes, Route, BrowserRouter } from 'react-router-dom';
import Loading from './component/Loading';

import Main = lazy(() => import('./pages/Main'));
import About = lazy(() => import('./pages/About'));
import MyPage = lazy(() => import('./pages/MyPage'));
import Login = lazy(() => import('./pages/Login'));
import SignUp = lazy(() => import('./pages/SignUp'));

function App() {
  return (
    <BrowserRouter basename={process.env.PUBLIC_URL}>
      <Suspense fallback={<Loading />}>
        <Routes>
          <Route path="/" element={<Main />} />
          <Route path="/about" element={<About />} />
          <Route path="/mypage/" element={<MyPage />} />
          <Route path="/login" element={<Login />} />
          <Route path="/signup" element={<SignUp />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

export default App;
```
