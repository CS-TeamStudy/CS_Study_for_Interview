# NextJS 사용 이유 

1. 파일기반 라우팅 시스템

- 매우 직관적
- 특정 기능의 예약파일
- nested routing, dynamic routing

2. 다양한 랜더링 기법 사용 가능

- client side, server side
- 서버사이드 랜더링의 장점만으로도 nextjs를 사용하는 충분한 이유가 될 수 있음<br/>
  [SSR 장점 공식문서](https://nextjs.org/docs/app/building-your-application/rendering/server-components)

3. Data fetching

- 서버 컴포넌트에서 바로 데이터를 받아와서 사용할 수 있음(SSR장점)
- 다양한 data fetching 방법(static, dynamic, ISR)
- 강려크한 메모이제이션, 캐싱 기능

4. 이미지 최적화

- 프론트엔드 성능 최적화 1순위

5. 쉬운 폰트설정

6. 쉬운 메타정보 수정, SEO(SSR장점)

7. 타입스크립트 지원
   <br/>
   <br/>

# 예약파일 설명

### 폴더 구조

<img width="126" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/7caade85-d45f-4c0e-ab99-0a17763f9430">

### page.tsx : 특정 라우트 화면에 보여지는 페이지

- app/fastcampus/page.tsx

```tsx
import Link from "next/link";

export type Post = {
  userId: number;
  id: number;
  title: string;
  body: string;
};

export default async function Posts() {
  //loading.tsx에서 설명
  await new Promise((resolve) => setTimeout(resolve, 2000));

  const response = await fetch("https://jsonplaceholder.typicode.com/posts");
  if (!response.ok) {
    // error.tsx에서 설명
    throw new Error("failed to fetch posts");
  }
  const posts: Post[] = await response.json();

  return (
    <div className="flex flex-col gap-2 p-2">
      {posts.map((post) => (
        <Link href={`/fastcampus/${post.id}`} key={post.id}>
          {post.title}
        </Link>
      ))}
    </div>
  );
}
```

<img width="472" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/0b0f7263-1405-4e79-be3e-aeccd06c50bb">

- app/fastcampus/[id]/page.tsx

```tsx
import { notFound } from "next/navigation";
import type { Post } from "../page";

export default async function Post({
  params: { id },
}: {
  params: { id: string };
}) {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/posts/${id}`
  );
  if (!response.ok) {
    throw new Error("failed to fetch a post");
  }

  const post: Post = await response.json();

  if (!post.title) {
    // not-found.tsx에서 설명
    notFound();
  }

  return (
    <div>
      <h1 className="text-2xl mb-10">제목 : {post.title}</h1>
      <p>내용 : {post.body}</p>
    </div>
  );
}
```

<img width="458" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/53b3dd86-35c9-4e3b-aff0-fec787c6f944">

## not-found.tsx : 특정 라우트 서스펜스 바운더리 내에 notFound 함수가 호출되면 보여짐

- app/fastcampus/not-found.tsx

```tsx
import Link from "next/link";

export default function NotFound() {
  return (
    <div className="text-center">
      <h2 className="text-red-400 text-3xl">Not Found</h2>
      <p>Could not find requested resource</p>
      <Link href="/">Return Home</Link>
    </div>
  );
}
```

<img width="320" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/9df3d178-0077-4e4e-8beb-eb0b1304dfb3">

### layout.tsx : 특정 라우트에서 공통적으로 보여지는 부분, nested route에도 적용이 된다.

- app/fastcampus/layout.tsx

```tsx
import { Suspense } from "react";
import Loading from "./laoding";

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <section>
      <nav className="bg-red-200 h-10">NAVIGATION BAR</nav>
      <Suspense fallback={<Loading />}>{children}</Suspense>
    </section>
  );
}
```

<img width="481" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/6b348e57-16ca-414b-ba4b-73a2ebb724a6">
<img width="480" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/94cff836-1543-426e-b588-b60d9acb7bab">

## template.tsx : layout과 비슷한 역할, 조금 다름

- template vs layout
  - 페이지를 이동(navigate)할 때 layout은 state를 보존함, template은 state가 보존되지 않음
  - layout은 children만 재랜더링, template은 template까지 모두 재랜더링
  - 보통의 경우는 layout을 사용하고 아래와 같은 특성을 사용할 때만 template을 사용
    - enter/exit animation
    - Features that rely on useEffect (e.g logging page views) and useState (e.g a per-page feedback form).
    - To change the default framework behavior. For example, Suspense Boundaries inside layouts only show the fallback the first time the Layout is loaded and not when switching pages. For templates, the fallback is shown on each navigation.

## loading.tsx : 특정 라우트 서스펜스 바운더리 내에 있는 비동기작업 loading이 발생할 때 보여지는 화면

- app/fastcampus/loading.tsx

```tsx
export default function Loading() {
  return <p>Loading...</p>;
}
```

<img width="316" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/b76dc614-4ab3-483e-bbe8-f1f0b1fa01b4">

## error.tsx : 특정 라우트 서스펜스 바운더리 내에서 에러가 발생하였을 때 보여지는 화면

- app/fastcampus/error.tsx

```tsx
"use client"; // 반드시 클라이언트 컴포넌트

import { useEffect } from "react";

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    console.error(error);
  }, [error]);

  return (
    <div>
      <h2 className="text-3xl text-red-400">Something went wrong!</h2>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

<img width="321" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/fa73cc7c-191a-4ae7-b4b9-a217f40d5234">

## global-error.tsx : app(루트 경로)의 error.tsx

## route.ts : 커스텀 Request와 Response을 만들 수 있는 라우트 핸들러

- app/fastcampus/api/route.tsx

```tsx
export async function GET() {
  const data = { name: "howoo", age: 6 };

  return Response.json({ data });
}
```

<img width="294" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/39ab31f8-ea18-4264-a9f2-c0dca9916528">

- fastcampus/route.ts로 작성될 경우, page.tsx와 route가 곂치게 되면서 이 경우 route.ts가 화면에 보여진다.
  <br/>
  <br/>
  <br/>

# NextJS 랜더링 방법

클라이언트 사이드 랜더링은 생략.<br/>
아래는 모두 서버사이드 랜더링이며 서버컴포넌트에서 행해짐.

### Static Rendering

- route들은 빌드 타임에 미리 랜더링이 된다.
- 사용자들은 클라이언트에서는 캐싱이 된 데이터 받으므로 매우 빠르다.
- 즉 빌드 후에 db에 변화가 발생하여도 클라이언트에서는 수정사항을 반영되지 않는다.
- 서버 컴포넌트에서 아무런 옵션을 주지 않은 경우 default는 static rendering이다.

```tsx
import supabase from "@/app/utils/supabase";

export default async function Posts() {
  // supabase db에서 posts 테이블을 가져오는 로직
  const { data: posts } = await supabase.from("posts").select();

  return <pre>{JSON.stringify(posts, null, 2)}</pre>;
}
```

<img width="684" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/7db00d4c-14a4-4f8b-b863-bb34b9690792">
<img width="441" alt="image" src="https://github.com/howooking/KDT5-M5/assets/87072568/b971c572-1408-4197-ae1d-815fd85a4828">

- db에서 변경이 발생하고 새로고침 하였을 때 화면에 반영이 되는지 여부

  - 개발 환경(npm run dev) + 새로고침 👉 반영되지 않는다.
  - 개발 환경 + 강력한 새로고침 👉 반영된다.
  - 배포 환경(npm run build && npm run start) + 새로고침 👉 반영되지 않는다.
  - 배포 환경 + 강력한 새로고침 👉 반영되지 않는다.

- 사용처

  - 데이터 변화가 거의 없는 사이트
  - 데이터의 주체가 사용자가 아닌 사이트 주인에게 있는 경우
  - 회사 소개 홈페이지, 개인 블로그

### Dynamic Rendering

- 라우트들은 요청이 있을 때 서버에서 실시간으로 랜더링이 된다.

```tsx
import supabase from "@/app/utils/supabase";

export const revalidate = 0;

export default async function Posts() {
  const { data: posts } = await supabase.from("posts").select();

  return <pre>{JSON.stringify(posts, null, 2)}</pre>;
}
```

- revalidation 시간을 조정하여 해당 시간 간격마다 클라이언트는 최신 db정보를 반영할 수 있다. (Incremental Static Regeneration)

- db에서 변경이 발생하고 새로고침 하였을 때 화면에 반영이 되는지 여부

  - revalidation = 0 + 개발 환경 + 새로고침 👉 반영된다.
  - revalidation = 0 + 배포 환경 + 새로고침 👉 반영된다.
  - revalidation = 30 + 개발 환경 + 30초 전 + 새로고침 👉 반영되지 않는다.
  - revalidation = 30 + 개발 환경 + 30초 전 + 강력한 새로고침 👉 반영된다.
  - revalidation = 30 + 개발 환경 + 30초 후 + 새로고침 👉 반영된다.
  - revalidation = 30 + 배포환경 + 30초 전 + 새로고침 👉 반영되지 않는다.
  - revalidation = 30 + 배포환경 + 30초 전 +강력한 새로고침 👉 반영되지 않는다.
  - revalidation = 30 + 배포환경 + 30초 후 + 새로고침 👉 반영된다.

- 사용처

  - 데이터의 변화가 빈번한 사이트
  - 데이터의 주체가 사용자인 경우
  - 데이터 변화가 빈번하지는 않은경우 ISR를 통해 static rendering의 장점을 가져올 수 있다.
  - 요청시에 비로소 요청 정보를 알 수있는 경우(search param 검색)
