# Javascript

## service worker

- what is service worker?

  - 웹 페이지와 별도로 백그라운드에서 실행되며 웹 페이지나 사용자 상호 작용이 필요하지 않은 기능을 제공하는 스크립트(JavaScript 파일)

    <img width="725" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/fc1f9e20-fdee-45ba-ba02-decf48481997">

### service worker의 특징

1. service worker의 생명주기

- 웹 페이지의 생명주기와는 완전히 별개로 존재
  - 웹 페이지가 닫히더라도 자동으로 비활성화되지 않음
  - DOM이나 window요소에 접근이 불가능 함
- service worker의 생명주기  
   <img width="686" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/0e1ebd4e-a8bc-47fc-a42e-4f2d1721a541">

  - Installing

    - 자바스크립트 파일이 다운로드되고 파싱되고 나면 서비스 워커는 설치중(installing) 상태가 되고, 설치가 성공적으로 이루어지면 설치됨(installed/watiing)상태가 되고, 설치가 실패한다면 중복(redundant) 상태가 됨
    - Install 이벤트 콜백에서 waitUntil() 메서드를 호출하면 호출 시 전달한 프로미스가 완료 또는 실패될 때까지 설치중 상태를 유지할 수 있게 함
    - `navigator.serviceWorker.register`를 사용해 새로운 서비스 워커 등록 가능

    ```javascript
    self.addEventListener("install", (e) => {
      console.log("[Service Worker] Install");

      e.waitUntil(
        caches.open(cacheName).then((cache) => {
          console.log("[Service Worker] Caching all: contentToChache");
          return cache.addAll(contentToCache);
        }),
      );
    });
    ```

    `caches`는 데이터를 저장할 수 있는 서비스워커 코드 범위 내에서 사용할 수 있는 객체 > `cache storage`에 저장

    웹 저장소는 동기적이므로 이 데이터를 웹 저장소에 저장할 수는 없기 때문에, cache API를 사용함

    다음 요청 때에는 캐시 된 파일이 있다면 추가로 요청하지 않고 캐싱 된 파일을 반환함

    > 따라서 오프라인 상태라도 cached된 데이터가 있다면 이를 반환해 사용 할 수 있음

  - Installed/waiting

    - 서비스 워커 설치 완료 후 현재 활성화된 서비스 워커가 없다면 곧바로 instaled 된 서비스 워커가 활성화를 시작함 > Activating
    - 이미 활성화된 서비스 워커가 앱을 제어하고 있다면 waiting 상태가 됨

  - Activating

    - 서비스 워커가 활성화 되기 직전의 상태
    - 이 상태에서 activate 이벤트가 발생함
    - 설치중 상태와 같이, waitUntil() 메서드를 활용하여 활성화 중 상태를 유지할 수 있음

    ```javascript
    self.addEventListener("activate", (event) => {
      console.log("activaiting!");
    });
    ```

  - Activated

    - 서비스 워커가 활성화 된 상태
    - 이 상태에서 서비스 워커는 앱을 제어 할 수 있고 fetch event 와 같은 동작 이벤트를 받아서 처리 할 수 있음

    ```javascript
    self.addEventListener('fetch', (e) => {
      e.respondWith(
        caches.match(e.request).then((r) => {
          // 캐싱된 리소스가 있다면 이를 반환
          return r || (
            // 없다면, fetch진행
            fetch(e.request)
              .then((response) {
                return caches
                  // 새로운 캐쉬 생성
                  .open(cacheName)
                  .then((cache) => {
                    // 캐시에 요청(url)/응답 저장
                    cache.put(e.request, response.clone());
                    // 응답 반환
                    return response;
                  });
              });
          )
        });
      );
    })
    ```

    - respondWith()는 FetchEvent되는 페이지에서 들어오는 요청에 대한 사용자 정의 응답을 생성하는 메소드

  - Redundant

    - 서비스 워커가 등록 중, 설치 중 **실패하거나 새로운 버전으로 교체**되면 중복 상태가 됨
    - 이 상태의 서비스 워커는 앱에 아무런 영향을 미칠 수 없음

  - service worker의 종료시점
    - 서비스 워커 범위 내에서 이벤트가 발생한다면 서비스 워커는 활성화 되고 이벤트를 처리한 후 종료됨
    - 다시말해 사용자가 사이트를 방문할 때, 서비스 워커가 시작되고, 페이지에서 이벤트 처리를 완료하는 즉시 종료 > 다른 이벤트가 나중에 들어온다면 서비스 워커는 다시 시작되고 완료되는 즉시 종료됨
