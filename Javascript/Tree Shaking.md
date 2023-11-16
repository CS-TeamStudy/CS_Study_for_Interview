 # JavaScript

## Tree Shaking

1. Tree Shaking?

- 나무를 흔들면 죽은 잎사귀들이 떨어지는 모습에 착안해 **Tree-shaking** 이라고 명명했다 즉, 필요한 코드(살아있는 잎사귀)를 사용되지 않는 코드로 부터 추려내는 것이다

2. Tree Shaking 동작원리

- MDN의 설명에 따르면 Tree Shaking은 ES2015(ES6)의 import/export문에 의존하여 JavaScript 파일 간의 참조 여부를 판단한다라고 되어있다.
- 따라서, **_Tree Shaking은 ES6부터 도입된 ES Modules(ESM)라고 불리는 모듈 시스템_**에 의존적이고, 이 말은 곧 우리가 알고 있는 많은 모듈 형식 중 ESM형식이 Tree Shaking을 가능하게 한다는 것을 의미한다

3. 그렇다면 ESM은 무엇일까?

- ECMAScript 모듈은 재사용을 위해 JavaScript 코드를 패키징하는 공식 표준 형식으로 import및 export문을 사용하여 정의한다.

- ESM 동작 방식

  - 구성
    - 로드해야 하는 모듈을 파악하기 위해 종속성 트리 구성
    - Module Record(export, import정보가 담긴 데이터)구조로 변환
  - 인스턴트화
    - 모듈 레코드를 모듈 인스턴스로 변환
    - import할 모든 값을 할당할 메모리 공간을 찾는 과정(_**export / import모두 해당 메모리를 가리키도록 함**_)
  - 평가
    - 코드를 실행하여 변수의 실제 값으로 메모리를 채우는 과정

- CJS에서 export/require는 같은 메모리 주소를 바라보지 않기 때문에 main모듈에서 값이 업데이트되었다고 해도 counter모듈에서는 계속 undefined값이 출력될 것입니다.

- 반대로 ESM은 export/import가 같은 메모리 주소를 바라보기 때문에 counter.js의 message변수는 undefined에서 ‘main complete’값으로 변경

- _**ESM은 정적인 구조를 가졌기 때문에 빌드 타임에 모듈간 관계를 파악할 수 있고, 이를 기반으로 사용되지 않는 코드를 제거하는 작업도 가능하다**_

4. ESM에서 Tree shaking이 유리한 이유

- 일반적으로 Tree Shaking이란 루트 노드에 연결되지 않은 노드(메소드/변수)를 제거하는 과정을 의미한다

- 여기서 **CommonJS**에서는 runtime에서 어떤 모듈을 로드할지 결정할 수 있기 때문에 빌드 단계에서는 번들러가 어떤 모듈을 포함할지, 포함하지 않을지 쉽게 결정할 수 없는 반면, ESM은 정적 분석이 가능하기 때문에 Tree shaking을 더욱 잘 지원 할 수 있다

## Tree shaking 적용

1. Babelrc 파일 설정

- Babel은 자바스크립트 문법이 브라우저에서 호환이 가능하도록 ES5 문법으로 변환하기 때문에 mport를 require() 구문으로 변환시킨다  
  그 결과,**equire은 export 하는 모든 모듈을 가져오게 됨** > Tree shaking이 동작하지 못하게 됨

```javascript
{
  “presets”: [
    [
      “@babel/preset-env”,
      {
	    "modules": false
      }
    ]
 ]
}
```

- babel-preset-env에 modules를 false로 하면, import, export의 구문을 ES5의 문법으로 변환시키지 않는다

2. 모듈 내 Side-Effect 발생 여부 확인

   ```javascript
   let football = ["kim", "lee"];

   const addfootball = (name) => {
     football.push(name);
   };
   ```

- football() 라는 함수가 쓰이지 않아 다른 코드에 영향을 주지 않는다 해도, addfootball() 함수 바깥의 변수를 변경하는 작업으로 인해 Side-Effect를 일으킨다고 판단하여 트리 쉐이킹을 하지 못하게 됨
- 최대한 Side-Effect를 발생하지 않게 하는것이 최선의 방법이지만 Side-Effect가 발생하지 않다고 단언 할 수 있으면, **Side-Effect가 일어나지 않는 모듈이라고 설정할 수 있음**

  ```javascript
  // package.json 설정
  // 모든 모듈이 사이드 이펙트를 발생하지 않는다고 정의하는 경우
  {
    "name": "webpack-tree-shaking-example",
    "version": "1.0.0",
    "sideEffects": false
  }
  // 특정 파일만 사이드 이펙트를 발생하지 않는 모듈이라고 따로 선언
  {
    "name": "webpack-tree-shaking-example",
    "version": "1.0.0",
    "sideEffects": [
      "./src/utils/utils.js"
    ]
  }
  ```

3. 필요한 모듈만 Import 해서 가져오기

   ```javascript
   import { module1, module2 } from "../utilFile";
   ```

<br/>
<br/>
<br/>

---

참고  
https://helloinyong.tistory.com/305#google_vignette  
https://so-so.dev/web/tree-shaking-module-system/#esm%EC%9D%98-%ED%8A%B9%EC%A7%95%EB%93%A4  
https://webpack.js.org/guides/tree-shaking/  
