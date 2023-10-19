# Javascript

## web storage & cookie

### cookie

- 웹 브라우저 상에 작은 ‘텍스트 파일’로 저장이 되는 만료 기간이 존재하는 저장소를 의미
- **'Cookie-name : Cookie-value'** 형태의 저장소 구조
- 쿠키는 특정 시간 초과 후에 데이터를 무효화하는 것을 권장하기에 사용자 인증이 유효한 시간을 명시할 수 있으며, 유효 시간이 정해지면 브라우저가 종료되어도 인증이 유지된다는 특징있음 **만료기간이 존재함**
- 서버에 접속한 방문 정보를 클라이언트에 저장하기에 매번 서버 전송되어 자원 소모가 발생
  <img width="879" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/6b282153-a3c2-4949-99e5-7dbd0586bf1f">
- 쿠키의 동작 방식
  - 클라이언트가 페이지를 요청
  - 서버에서 쿠키를 생성
  - HTTP 헤더에 쿠키를 포함 시켜 응답
  - 브라우저가 종료되어도 쿠키 만료 기간이 있다면 클라이언트에서 보관하고 있음
  - 같은 요청을 할 경우 HTTP 헤더에 쿠키를 함께 보냄
  - 서버에서 쿠키를 읽어 이전 상태 정보를 변경 할 필요가 있을 때 쿠키를 업데이트 하여 변경된 쿠키를 HTTP 헤더에 포함시켜 응답
- 쿠키는 오랜시간 동안 유지될 수 있고, 자바스크립트를 이용하여 쿠키에 접근할 수 있기에 쿠키에 민감한 정보를 담았을 시 **CSRF 공격에 취약**
  - 사이트 간 요청 위조 CSRF(Cross Site Request Forgery)란?
    - 비 정상적으로 사용자의 의도와 무관하게 다른 사이트에 HTTP 요청 보내는 것을 CSRF 공격이라 합니다.
- JavaScript에서 쿠키 접근 방법

  ```javascript
  document.cookie; // 를 통해 cookie에 접근 가능
  document.cookie = "color=red"; // key-value 형태로 쿠키값 생성 및 변경 가능
  document.cookie = "color=red; secure; Domain=nolto.app; SameSite=Lax"; // 쿠키 옵션 설정 가능
  ```

  <img width="566" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/e291938a-65b4-4edc-a1df-ab8b904a9853">

  - 위와 같은 방식으로 쿠키에 접근하고 쿠키 값을 추가 생성 및 변경 할 수 있음
  - 쿠키 옵션
    - expires: 쿠키 만료 날짜를 설정하며, 설정하지 않으면 브라우저를 닫을 때 쿠키가 만료됨
    - max-age: 쿠키가 생성된 시간부터 시작하여 만료되어야 하는 시간(초)을 설정
    - path: 쿠키에 접근할 수 있는 절대 경로를 설정
    - Domain: 쿠키가 특정 도메인을 대상으로 설정 됨 > 따라서 특정된 도메인으 요청에만 쿠키가 활성화 됨 (별도의 명시가 없을 시 요청을 보낸 도메인으로 지정됨)
    - secure: 쿠키가 https를 통해서만 전송 됨 > Http에서 secure 옵션이 적용된 쿠키는 활성화 되지 않음
    - samesite: 쿠키가 다른 웹 사이트에 전송되지 않도록 함 > Cross-Site의 요청은 무시됨
      - None : 크로스사이트 요청의 경우에도 항상 전송
      - Strict : 크로스 사이트 요청에는 항상 전송 불가. 즉, 퍼스트 파티 쿠키만 전송
      - Lax : 대체적으로 서드 파티 쿠키는 전송되지 않지만 예외적인 요청에는 전송

- 쿠키의 사용예시
  - 로그인 화면에서 ‘아이디 자동완성’ 기능
  - 팝업 화면에서 ‘오늘 하루 보지 않기' 기능
  - 미 로그인 상태에서 ‘장바구니 담기’ 기능

### web storage?

- 웹 스토리지 (web storage)는 서버가 아닌, 클라이언트에 데이터를 저장할 수 있도록 지원하는 HTML5의 새로운 기능
- 데이터 구조는 key-value 형태의 저장소 구조를 가지며 문자열로 반환 저장됨
- 로컬 스토리지는 반영구적으로 브라우저를 종료하거나 OS를 종료해도 유지 되며 **강제적으로 삭제나 초기화를 할 때만 사라짐**
- 로컬 스토리지는 서버에 전송되지 않고 클라이언트 내에서만 유효 (자원 소모 적음)
- cookie 와 유사한 기능을 가졌지만 차이점을 보임
- cookie 와 web storage 의 차이점?

  - cookie _4KB까지 밖에 저장공간_ 을 가지지 못하는 반면 웹 스토리지는 약 _5MB정도의 저장공간_ 을 가질 수 있음

- 로컬 스토리지와 세션 스토리지

  - 로컬 스토리지 (local Storage)

    - 브라우저에 반영구적으로 데이터를 저장하여 브라우저를 종료해도 데이터가 유지되는 특성이 있음
    - 브라우저 자체에 반영구적으로 데이터가 유지되지만, 도메인 (domain)이 다른 경우에는 로컬 스토리지에 접근할 수 없음

      > www.google.com에서 로컬 스토리지에 저장한 데이터를 www.naver.com 에서 접근할 수 없다!

    - Java script를 통해 로컬 스토리지에 접근

      ```javascript
      localStorage.setItem("item1", 10);
      localStorage.setItem("item2", "new item");
      localStorage.setItem("item3", 0.543534);

      console.log(localStorage.getItem("item1"));
      console.log(localStorage.getItem("item2"));
      console.log(localStorage.getItem("item3"));
      ```

        <img width="568" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/ca1e8018-1f6a-4188-806c-66cc4e31f727">

  - 세션 스토리지 (Session Storage)

    - 세션 스토리지는 각 세션마다 데이터가 개별적으로 저장됨
    - 만약, 브라우저에서 여러개의 탭을 실행하면 탭마다 **개별적으로 데이터가 저장** 되는 것
    - 로컬 스토리지와는 다르게 세션을 종료하면 데이터가 **자동으로 제거 되며, 같은 도메인이라도 세션이 다르면 데이터에 접근이 불가** 하다

    - Java script를 통해 로컬 스토리지에 접근

      ```javascript
      sessionStorage.setItem("item1", "toss");
      sessionStorage.setItem("item2", "naver");
      sessionStorage.setItem("item3", "kakao");

      console.log(sessionStorage.getItem("item1"));
      console.log(sessionStorage.getItem("item2"));
      console.log(sessionStorage.getItem("item3"));
      ```

      <img width="570" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/642cbae2-c41a-4f3d-9cba-8ef832756e2d">
