## 서론

- 프론트엔드 최적화 1순위는 이미지 최적화이다.
- Next.js의 Image 컴포넌트는 초보자도 이미지 최적화를 할 수 있게 도와주는 매우 강력한 기능이다.
- 그러나 공식 문서상 설명이 애매한 부분이 있기 다소 있기 때문에 이 기회에 제대로 정리 하고자 한다.

## Warm-up Quiz

1. Image 컴포넌트는 유료서비스이다.

1. width 속성을 입력하여 이미지의 너비를 줄이면 파일 크기가 줄어든다.

1. Image fill 속성을 사용했는 때에는 size속성이 기본으로 "100vw"로 설정된다.

1. 원격 이미지는 반드시 width와 height를 입력해야한다.

1. 로컬 이미지에서 width, height를 비율에 맞지 않게 입력하면 이미지가 찌그러든다.

## Image 컴포넌트를 사용해야 하는 이유?

### 사용할 이미지 정보

<img alt="cat" src="https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D">

- 3.4MB
- 5194 x 3456
- from unsplashed.com

### html img 태그 사용시

```js
export default function ImagePage() {
  return <img src="/cat.jpg" alt="cat" />;
}
```

<img width="530" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/23393fca-6320-471b-a437-d36313d3c199">

- 원본 파일과 동일한 파일 이름과 파일 크기
- fast 3g 환경에서 31.77초 소요, 참고로 light house LCP 기준은 2.5초 이하

### Nextjs Image 컴포넌트 사용시

```js
import Image from 'next/image';
import cat from '@/public/cat.jpg';

export default function ImagePage() {
  return (
    <Image
      src={cat}
      alt="cat"
      quality={75} // 이미지 품질, 기본값 75
    />
  );
}
```

<img width="526" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/0479acc8-5f99-4fa6-80ad-694f4adadcde">

- 파일 이름이 `http://localhost:3000/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fcat.0d7457c2.jpg&w=3840&q=75`로 바뀜
  - w는 width를 의미. 원본은 width가 5194인데 next/image에서 3840으로 줄임 → srcset 내용, 밑에 나옴
  - q는 quality를 의미. 원본 품질을 100이라고 하면 next/image에서 75% 수준으로 낮춤

- jpg에서 웹친화적 이미지 형식인 webp로 변경
- 675kB로 파일 크기 감소
- fast 3g 환경에서 11.8초 소요
- Image 컴포넌트만 사용했을 뿐인데 굉장한 성능향상을 보인다.

### Image 컴포넌트 + size속성

```js
import Image from 'next/image';
import cat from '@/public/cat.jpg';

export default function ImagePage() {
  return <Image src={cat} alt="cat" size="100vw" />; // 이미지가 100% view width를 차지한다는 의미
}
```

<img width="451" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/998dd4d1-a2ef-46fe-ab3f-717a9444c0a4">

- 102kb로 파일 크기 감소
- fast 3g 환경에서 2.69초 소요
- size 속성은 아래에서 자세히 다룰 예정
- size하나 넣었다고 이미지 크기가 1/6이로 줄어듬

### 결론

- Image 컴포넌트 + size 속성 사용시 html img태그 사용시보다 파일크기는 34.12배 감소
- 로딩 시간 11.8배 감소,
- 무조건 사용하자 ??

## Pricing?

https://vercel.com/docs/image-optimization/limits-and-pricing

- nextjs 공식 문서가 아닌 vercel 공식문서 보면 다음과 같은 요금제를 확인 할 수 있다.

  <img width="673" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/26ab2db9-757d-4db9-b9c2-cb32d44e0c22">

- 실제 dashboard에서는 지금까지 프로젝트에서 사용한 총 이미지 source의 개수를 알 수 있다.

  <img width="775" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/715ebc18-ff78-4165-8b86-4cd08606f4af">

- 여기에서 말하는 source images란 개별 이미지 파일을 말함.

  ```js
  import Image from 'next/image';
  import cat from '@/public/cat.jpg';

  export default function ImagePage() {
    return (
      <>
        <Image src={cat} alt="cat" size="100vw" />
        <Image src={cat} alt="cat" size="100vw" />
        <Image src={cat} alt="cat" size="100vw" />
        <Image src={cat} alt="cat" size="100vw" />
        <Image src={cat} alt="cat" size="100vw" />
      </>
    );
  }
  ```

  - 5번을 사용해도 1개의 source로 인정

  ```js
  import Image from 'next/image';
  import catSmall from '@/public/cat-sm.jpg';
  import catMedium from '@/public/cat-md.jpg';
  import catLarge from '@/public/cat-lg.jpg';

  export default function ImagePage() {
    return (
      <>
        <Image src={catSmall} alt="cat" />
        <Image src={catMedium} alt="cat" />
        <Image src={catLarge} alt="cat" />
      </>
    );
  }
  ```

  - 위의 경우 3개의 source로 인정
  - 따라서 어줍잖게 최적화 한다고 이미지 3개 쓰지 말고 한번에 제대로 하는게 낫다.

- 만약 웹사이트에서 사용자 이미지가 많아질 경우 pro요금제(월 2만5천원) + 1000장당 6천원이므로 요금 부담이 상당함
  - 다양한 동일 서비스 제공 업체와 가격을 비교하거나 자체적인 이미지 최적화 솔루션을 만들 수도 있음
- 따라서 넥스트에서 제공하는 이미지 최적화 기능을 끄는 법도 알아야 함

  ```js
  import Image from 'next/image';
  import cat from '@/public/cat.jpg';

  export default function Home() {
    return <Image src={cat} alt="cat" unoptimized />;
  }
  ```

## 정적 로컬 이미지

<img width="243" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/3acd41dd-429b-4c10-b6b4-eb6c2cb795d8">

- next/image에서 정의한 정적 이미지 type은 다음과 같다.

  ```ts
  export interface StaticImageData {
    src: string;
    height: number;
    width: number;
    blurDataURL?: string;
    blurWidth?: number;
    blurHeight?: number;
  }
  ```

- 이를 화면에서 확인해 보면 다음과 같다.

```js
  import Image from 'next/image';
  import cat from '@/public/cat.jpg';

  export default function Home() {
    return (
      <>
        <Image src={cat} alt="cat" size="100vw" />
        <div className="p-4">
          <p>src : {cat.src}</p>
          <p>height : {cat.height}</p>
          <p>width : {cat.width}</p>
          <p>blurDataURL : {cat.blurDataURL}</p>
          <p>blurHeight : {cat.blurHeight}</p>
          <p>blurWidth : {cat.blurWidth}</p>
        </div>
      </>
    );
  }
```

  <img width="682" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/ebf68bab-4534-4d81-808f-ea428300c890">

- 정적 이미지 설정시 자동으로 height와 width가 설정된 것을 확인 할 수 있다. 그러나 이는 웹페이지 상에서 보여질 크기가 아닌 실제 이미지 크기이다.

  - 웹페이지에서 사용할 이미지는 대부분 원본보다는 작은 크기를 사용하기 때문에 width 속성을 조절한다.
  - size속성은 일단 무시

    ```js
    import Image, { StaticImageData } from 'next/image';
    import cat from '@/public/cat.jpg';

    export default function Home() {
      return (
        <>
          <Image src={cat} alt="cat" sizes="100vw" width={500} />
          <div className="p-4">
            <p>src : {cat.src}</p>
            <p>height : {cat.height}</p>
            <p>width : {cat.width}</p>
            <p>blurDataURL : {cat.blurDataURL}</p>
            <p>blurHeight : {cat.blurHeight}</p>
            <p>blurWidth : {cat.blurWidth}</p>
          </div>
        </>
      );
    }
    ```

    <img width="626" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/81d31d43-7bfe-4f30-9b2f-55de3a8677a2">

    <img width="445" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/614e20c5-4a04-4d53-a8f2-df875dcb1417">

  - width를 조절한다고 이미지 크기가 줄어들진 않는다.
  - 정적 이미지는 이미 이미지의 비율을 알고 있기 떄문에 width 또는 height 속성만을 제공하면 된다.
  - width속성을 통해 로드 될 이미지의 크기를 미리 지정하여 CLS(cumulative layout shift)를 막을 수 있다.

- 정적 이미지 설정시 blur와 관련된 값들(blurDataURL, blurHeight, blurWidth)을 확인할 수 있다. 이들은 placeholder에서 사용되며 이미지가 로드되기 전 blur(뿌연)효과를 보여준다.

  ```js
  import Image, { StaticImageData } from 'next/image';
  import cat from '@/public/cat.jpg';

  export default function Home() {
    return (
      <>
        <Image
          src={cat}
          alt="cat"
          sizes="100vw"
          width={500}
          placeholder="blur"
        />
        <div className="p-4">
          <p>src : {cat.src}</p>
          <p>height : {cat.height}</p>
          <p>width : {cat.width}</p>
          <p>blurDataURL : {cat.blurDataURL}</p>
          <p>blurHeight : {cat.blurHeight}</p>
          <p>blurWidth : {cat.blurWidth}</p>
        </div>
      </>
    );
  }
  ```

  ![2023-12-04_21-09-19](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/aadeab3e-f63c-4f38-a9e1-e93a14fe8d14)

  <img width="451" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/ee239086-c24a-4592-aed4-a01e3bfa4f75">

- 위에가 blurDataURL 이미지이며 파일 형식이 svg + sml, width가 8, 이미지 품질이 70%인 이미지 파일이다.

## 원격 외부 이미지

- 원격 이미지를 가져올 경우 next.config.js 설정이 필요함

  ```js
  /** @type {import('next').NextConfig} */
  const nextConfig = {
    images: {
      remotePatterns: [
        {
          protocol: 'https',
          hostname: 'images.unsplash.com',
        },
      ],
    },
  };
  ```

  ```js
  import Image from 'next/image';

  export default function Home() {
    return (
      <Image
        src="https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
        alt="cat"
        sizes="100vw"
        width={500}
        placeholder="blur"
      />
    );
  }
  ```

- 예상 되는 에러는??

  <img width="755" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/380b2e72-ab81-4ab4-b8a3-3f41bbd62b33">

  - 원격이미지의 크기는 물론이거니와 비율도 모르기 떄문에 반드시 height 속성도 넣어줘야한다.

```js
import Image from 'next/image';

export default function Home() {
  return (
    <Image
      src="https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
      alt="cat"
      sizes="100vw"
      width={500}
      height={333}
      placeholder="blur"
    />
  );
}
```

- 예상 되는 에러는??
  <img width="728" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/1b709b59-4593-4d39-939a-f55ee9b67f98">

  - 원격 이미지의 경우 blurDataURL가 자동으로 생성되지 않는다.
  - placeholder를 제거하면 blur 로딩 효과가 없어지고 에러도 사라진다.

- 원격인 경우 loading 효과를 만들고 싶다면?

  - 로컬 placeholder 이미지 사용하는 방법

  ```js
  import Image from 'next/image';

  export default function Home() {
    return (
      <Image
        src="https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
        alt="cat"
        sizes="100vw"
        width={500}
        height={333}
        placeholder="blur"
        blurDataURL="/blur.png" // 회색의 로컬 이미지
      />
    );
  }
  ```

  ![2023-12-04_21-41-54](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/21e8bbcd-bee0-4e50-ad11-273f8b108cd2)

  - 고정된 이미지가 아니라 동적인 blurry placeholder를 사용하고 싶다면?

  ```js
  import Image from 'next/image';
  import lqip from 'lqip-modern';

  export default async function Home() {
    // 서버컴포넌트로
    const url =
      'https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D';
    const imgData = await fetch(url);
    const arrayBufferData = await imgData.arrayBuffer();
    const lqipData = await lqip(Buffer.from(arrayBufferData));

    return (
      <Image
        alt="cat"
        placeholder="blur"
        src={url}
        width={500}
        height={333}
        blurDataURL={lqipData.metadata.dataURIBase64}
      />
    );
  }
  ```

  ![2023-12-04_22-57-53](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/9b9dad0e-59dc-4dd4-b67c-36329e7d9e91)

## fill 속성

- 원격이미지는 사이즈는 물론 가로세로 비율도 모르기 때문에 만약 일정한 크기의 이미지를 보여주고 싶다면 다음과 같은 문제가 발생할 수 있다.

```js
import Image from 'next/image';

export default async function Home() {
  return (
    <div className="flex">
      <Image
        alt="cat"
        src="https://images.unsplash.com/photo-1533738363-b7f9aef128ce?q=80&w=2535&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
        width={500}
        height={500}
      />
      <Image
        alt="cat"
        src="https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
        width={500}
        height={500}
      />
    </div>
  );
}
```

  <img width="881" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/e3b66277-14d8-4e83-b33f-0baec08b70a8">

- flex로 인해 두번째 이미지의 비율이 깨지게 된다.
- 첫번째 이미지는 width와 height가 500px이지만 실제 이미지 비율이 다르므로 height가 무시된다.
- 이럴 때 사용하는 속성이 fill이다.

```js
import Image from 'next/image';

export default async function Home() {
  return (
    <div className="flex">
      <div className="w-[500px] h-[500px] relative">
        <Image
          fill
          alt="cat"
          src="https://images.unsplash.com/photo-1533738363-b7f9aef128ce?q=80&w=2535&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
        />
      </div>
      <div className="w-[500px] h-[500px] relative">
        <Image
          fill
          alt="cat"
          src="https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
        />
      </div>
    </div>
  );
}
```

- fill을 사용하게 되면 부모요소(position or absolute or fixed, 너비 500px, 높이 500px)에 가득 차게 된다. 그리고 부모요소에 따라 크기가 결정되기 때문에 width와 height속성을 넣으면 안된다(넣으면 에러남).
  <img width="832" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/8ed43c5c-67fb-451b-bf01-40eb5d25c97d">

- 그러나 이렇게 되면 사진의 비율이 꺠지게 된다. 이는 css onject 속성을 cover로 해주면 비율을 유지한채 부모요소를 가득 채우게 된다. 경우에 따라 contain이나 다른 값을 사용해도 된다.

  ```js
  import Image from 'next/image';

  export default async function Home() {
    return (
      <div className="flex">
        <div className="w-[500px] h-[500px] relative">
          <Image
            fill
            alt="cat"
            src="https://images.unsplash.com/photo-1533738363-b7f9aef128ce?q=80&w=2535&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
            className="object-cover"
          />
        </div>
        <div className="w-[500px] h-[500px] relative">
          <Image
            fill
            alt="cat"
            src="https://images.unsplash.com/photo-1472491235688-bdc81a63246e?q=80&w=2670&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D"
            className="object-cover"
          />
        </div>
      </div>
    );
  }
  ```

  <img width="830" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/cf6cf6b1-a806-47d0-afff-c3c41045140a">

- fill 속성은 정적파일에서도 사용가능하다.
- fill 속성을 사용하면 width와 height가 없으므로 CLS가 발생하지 않을까?
  - 사이즈를 정해주는 부모가 있음

## size 속성

- srcset과 더불어 반응형 이미지 구현을 위해 필요한 속성이다.
- srcset과 size에 관한 [내용](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)은 mdn에서 읽어보길 바란다.

```js
import Image from 'next/image';
import cat from '@/public/cat.jpg';

export default async function Home() {
  return (
    <>
      <Image alt="cat" src={cat} />
      <Image alt="cat" src={cat} sizes="100vw" />
    </>
  );
}
```

<img width="737" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/5564658d-921b-4cc1-90a0-5ff99ed0bf61">

- 일반 size값을 주지 않은 위의 경우와 다르게 아래의 경우 next Image 컴포넌트가 알아서 다양한 width에 해당하는 image srcse을 제공해준다.
- size 계산이 간단한 경우라면 쉽게 sizes 속성을 설정해줄 수 있다.

  ```js
  import Image from 'next/image';
  import cat from '@/public/cat.jpg';

  export default async function Home() {
    return <Image alt="cat" src={cat} width={500} />;
  }
  ```

  - 위와 같은 경우 기기의 width가 500이하인 경우 이미지는 device의 100vw를 차지하고 500보다 크더라고 사진의 width는 500으로 일정하다. 이를 sizes속성에 적용하면 다음과 같다.

    ```js
    import Image from 'next/image';
    import cat from '@/public/cat.jpg';

    export default async function Home() {
      return (
        <Image
          alt="cat"
          src={cat}
          width={500}
          sizes="(max-width: 500px) 100vw, 500px"
        />
      );
    }
    ```

- device width에 따라 size를 계산해주는 사이트 https://ausi.github.io/respimagelint/

  - 북마크를 일부로 잘못된 size를 입력한다음 린트를 돌리면 된다.

  ```js
  import Image from 'next/image';
  import cat from '@/public/cat.jpg';

  export default async function Home() {
    return <Image alt="cat" src={cat} width={500} sizes="10vw" />;
  }
  ```

  <img width="708" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/4998a202-d4e2-470d-aece-5d01a720ad1a">

  - 정확한 값은 아니지만 얼추 비슷하게 나온다.

  - 다음과 같이 복잡한 size속성을 갖는 경우에 유용하다.

  ```js
  import Image from 'next/image';
  import cat1 from '@/public/cat1.jpg';
  import cat2 from '@/public/cat2.jpg';
  import cat3 from '@/public/cat3.jpg';
  import cat4 from '@/public/cat4.jpg';
  import cat5 from '@/public/cat5.jpg';
  import cat6 from '@/public/cat6.jpg';
  import cat7 from '@/public/cat7.jpg';

  export default async function Home() {
    return (
      <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
        <Image alt="cat" src={cat1} width={500} />
        <Image alt="cat" src={cat2} width={500} />
        <Image alt="cat" src={cat3} width={500} />
        <Image alt="cat" src={cat4} width={500} />
        <Image alt="cat" src={cat5} width={500} />
        <Image alt="cat" src={cat6} width={500} />
        <Image alt="cat" src={cat7} width={500} />
      </div>
    );
  }
  ```

  ![ezgif com-video-to-gif](https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/2d5b58a7-4e53-439f-bd3d-d779ebac37fe)
  <img width="996" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/4ac93e8d-5d33-4f3e-87a6-0fea610c16ce">

## priority 속성

- img태그의 기본 속성인 fetchPriority를 설정함

```js
import Image from 'next/image';
import cat1 from '@/public/cat1.jpg';
import cat2 from '@/public/cat2.jpg';
import cat3 from '@/public/cat3.jpg';
import cat4 from '@/public/cat4.jpg';
import cat5 from '@/public/cat5.jpg';
import cat6 from '@/public/cat6.jpg';
import cat7 from '@/public/cat7.jpg';

export default async function Home() {
  return (
    <div className="flex flex-col gap-4">
      <Image alt="cat" src={cat1} width={500} placeholder="blur" priority />
      <Image alt="cat" src={cat2} width={500} placeholder="blur" priority />
      <Image alt="cat" src={cat3} width={500} placeholder="blur" priority />
      <Image alt="cat" src={cat4} width={500} placeholder="blur" />
      <Image alt="cat" src={cat5} width={500} placeholder="blur" />
      <Image alt="cat" src={cat6} width={500} placeholder="blur" />
      <Image alt="cat" src={cat7} width={500} placeholder="blur" />
    </div>
  );
}
```

- 첫화면에 보이는 부분의 이미지의 우선순위를 높임

<img width="837" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/2521ee7d-cc58-4920-a5fd-8ae67dde090b">

- 다운로드 시점이 다른 이미지보다 빠르다.

<img width="719" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/87072568/dd1b0f59-d14f-4c54-ac8c-f7d9a81e1ae7">

- fetchPriority가 high로 바뀐 것을 확인 할 수 있다.
