# [JavaScript]

## document load event와 DOMContentLoaded event

### DOMContentLoaded event? 

- DOM tree 분석이 완료된 후 event 발생
- 즉, DOM tree 가 다 만들어진 후에 돔에 접근이 가능하기 때문에, 돔이 생성되기전 돔을 조작하는 자바스크립트 코드가 실행되어 원치 않는 결과를 내는것을 막을 수 있음
- 이미지 파일(<img>)이나 스타일시트 등의 기타 외부 자원은 기다리지 않음

  ```javascript
  <script>
    function ready() {
      alert('DOM이 준비되었습니다!');

      // 이미지가 로드되지 않은 상태이기 때문에 사이즈는 0x0입니다.
      alert(`이미지 사이즈: ${img.offsetWidth}x${img.offsetHeight}`);
    }

    document.addEventListener("DOMContentLoaded", ready);
  </script>

  <img id="img" src="https://en.js.cx/clipart/train.gif?speed=1&cache=0">
  ```
  
### DOMContentLoaded event의 특이사항

- DOMContentLoaded와 scripts

  ```javascript
  <script>
    document.addEventListener("DOMContentLoaded", () => {
      alert("DOM이 준비되었습니다!");
    });
  </script>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.3.0/lodash.js"></script>

  <script>
    alert("라이브러리 로딩이 끝나고 인라인 스크립트가 실행되었습니다.");
  </script>
  ```

  - 브라우저는 HTML 문서를 처리하는 도중에 `<script>` 태그를 만나면, DOM 트리 구성을 멈추고 `<script>`를 실행함, 스크립트 실행이 끝난 후에야 나머지 HTML 문서를 처리
  - 스크립트가 DOM 조작 관련 로직을 담고 있을 수 있기 때문
  - DOMContentLoaded 이벤트 역시 `<script>` 안에 있는 스크립트가 처리되고 난 후에 발생
  - 예시를 실행하면 "라이브러리 로딩이 끝나고…"가 먼저 보인 후 "DOM이 준비되었습니다!"가 출력되는 것을 확인할 수 있음

  #### DOMContentLoaded를 막지 않는 스크립트

      위와 같은 규칙엔 두 가지 예외사항이 있습니다.
      - async 속성이 있는 스크립트는 DOMContentLoaded를 막지 않는다
      - document.createElement('script')로 동적으로 생성되고 웹페이지에 추가된 스크립트는 DOMContentLoaded를 막지 않는다

  <br/>

- DOMContentLoaded와 styles

  - 외부 스타일시트는 DOM에 영향을 주지 않기 때문에 DOMContentLoaded는 외부 스타일시트가 로드되기를 기다리지 않음

  - 스타일이 로드 된 후 발생하는 한가지 예외 : **_스타일시트를 불러오는 태그 바로 다음에 스크립트가 위치하면 이 스크립트는 스타일시트가 로드되기 전까지 실행되지 않음_**

  ```javascript
  <link type="text/css" rel="stylesheet" href="style.css">
  <script>
    // 이 스크립트는 위 스타일시트가 로드될 때까지 실행되지 않습니다.
    alert(getComputedStyle(document.body).marginTop);
  </script>
  ```

  - 스크립트에서 스타일에 영향을 받는 요소의 프로퍼티를 사용할 가능성이 있기 때문
  - 위 예시에선 스크립트에서 요소의 좌표 정보를 사용하고 있기에 스타일이 로드되고, 적용되고 난 다음에야 좌표 정보가 확정되기 때문에 제약이 생기고 예외가 발생함

### document load event?

- load는 돔트리 이후 모든 리소스까지 완벽히 끝난 후 event 발생
- 이미지 파일(<img>)이나 스타일시트 등의 기타 외부 자원을 모두 불러오고 난 후 발생

```javascript
<script>
  window.onload = function() { // window.addEventListener('load', (event) => {와 동일합니다.
    alert('페이지 전체가 로드되었습니다.');

    // 이번엔 이미지가 제대로 불러와 진 후에 얼럿창이 실행됩니다.
    alert(`이미지 사이즈: ${img.offsetWidth}x${img.offsetHeight}`);
  };
</script>

<img id="img" src="https://en.js.cx/clipart/train.gif?speed=1&cache=0">
```

- 이미지가 모두 로드되고 난 후 실행되기 때문에 이미지 사이즈가 제대로 출력됨

> **_즉, DOMContentLoaded 이벤트가 load이벤트보다 앞서 발생하며 로딩 측면에서 DOMContentLoaded event가 우위에 있음_**

<br/>

---

### beforeunload/unload event?

– 사용자가 페이지를 떠날 때 발생함

- unload

  - 이벤트는 사용자가 페이지를 떠날 때, 즉 문서를 완전히 닫을 때 실행
  - 팝업창을 닫는 것과 같은 딜레이가 없는 작업을 수행한다

- beforeunload

  - 사용자가 현재 페이지를 떠나 다른 페이지로 이동하려 할 때나 창을 닫으려고 할 때 실행
  - beforeunload 핸들러에서 추가 확인을 요청할 수 있음

  ```javascript
  window.onbeforeunload = function () {
    return false;
  };
  ```

    <img width="280" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/8fd1e986-0d15-4338-a6a0-7d88cc7b18e6">

## <br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

참조
https://ko.javascript.info/onload-ondomcontentloaded
