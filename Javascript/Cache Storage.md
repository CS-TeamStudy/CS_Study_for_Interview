# JavaScript

## Cache Storage

- what is Cache API?

  - cache storage 를 설명하기 전, Cache란 무엇일까?
    - Cache란 어떠한 데이터를 임시 저장하는 장소를 말함
    - 원래의 서버에서 정보를 찾아오는 것이 아닌, Cache로부터 데이터를 가져올수 있고 이를 통해, 더 빠른 속도로 데이터에 접근 할 수 있음

  > Cache API? <br/>
  > Request에 대한 HTTP response를 저장하기 위한 획기적인 방식의 클라이언트 저장소

  - 특징
    - 네트워크 연결 없이도 정보를 사용할 수 있도록 함
    - 오프라인 상태에서도 cache 된 데이터를 불러와 사용가능함
    - 주로 Service Worker API와 사용됨

<img width="1068" alt="image" src="https://github.com/CS-TeamStudy/CS_Study_for_Interview/assets/125563995/d7d04e8a-0b97-4ad0-b07b-dbb1f2f5602b">

- cache strage?

  - 따라서 cache stroage 는 캐싱된 데이터를 저장하는 공간이다
  - cache storage method

    > 캐시를 열거나 생성하는 method

    - caches.open({name})
      - {name}과 일치하는 cache를 열거나, 일치하는 cache가 없다면 {name}으로 새로운 cache를 생성함
      - Cache 객체로 확인되는 Promise를 반환

    ```javascript
    const cache = await caches.open("my-cache");
    ```

    > 캐시에 추가하는 method

    - caches.add()
      - Request 또는 URL (string) 중 하나의 매개변수를 사용
      - 네트워크에 요청하고 응답을 캐시에 저장 > 만약 요청 실패시 아무것도 저장되지 않고 Promise가 거부됨
    - cache.addAll()
      - .add() 와 유사하지만 Request 객체 또는 URL (string)의 배열을 매개변수로 사용

    ```javascript
    // Request 객체를 사용하는 경우
    cache.add(new Request("/data.json"));
    // url을 사용하는 경우
    cache.add("/data.json");
    // url 배열을 사용하는 경우
    const urls = ["/cu.json", "/gs.json"];
    cache.addall(urls);
    ```

    - cache.put()
      - **네트워크의 응답을 저장하거나 자체 Response을 만들고 저장할 수 있음**
      - 매개변수
        1. Request 객체 또는 URL(string)
        2. 네트워크에서 가져온 Response 혹은 코드에 의해 생성된 Response
      - 비 CORS 응답 또는 응답의 상태 코드가 200 범위에 속하지 않는 다른 응답을 저장할 수 있음

    ```javascript
    // server 혹은 stroe 응답 저장
    cache.put("/data.json");
    // 자체 응답을 저장
    cache.put("/test.json", new Response('{"foo": "bar"}'));
    // 3rd party site 혹은 store 응답 저장
    cache.put("https://example.com/data.json");
    ```

    > 저장된 캐시를 검색하는 method

    - cache.match()

      - Request 또는 URL을 매개변수로 사용
      - cache된 데이터 중 일치하는 항목이 발견되면 Response로 확인되는 Promise를 반환하고 그렇지 않으면 undefined를 반환함

      ```javascript
      const response = await cache.match(url);
      ```

      - 캐시된 요청이 2개 이상 일치하면 먼저 생성된 요청 반환 > 일치하는 모든 응답을 검색하려면 cache.matchAll() 사용

      ```javascript
      const responses = await cache.matchAll(url);
      console.log(`There are ${responses.length} matching responses.`);
      ```

      - 추가로 options 객체를 매개변수로 넘겨 검색 조건을 완화 할 수 있음

      ```javascript
      const options = {
        ignoreSearch: true,
        ignoreMethod: true,
        ignoreVary: true,
      };
      const response = await cache.match(url, options);
      ```

      - ignoreSearch
        - default: false
        - true일때, query string을 무시하고 검색함
        - ` http://foo.com/?value=bard`에서 `?value=bar` 를 제외하고 검색
      - ignoreMethod
        - default: false
        - true일때, 기본 메소드 유효성 검사를 우회하거나 무시할 수 있음 (GET) 이외의 metod를 허용
      - ignoreVary
        - default: false
        - true일때, VARY 헤더 일치를 수행하지 않도록 설정 다시 말해, URL이 일치하면 Response 객체에 VARY 헤더가 있는지 여부에 관계없이 일치로 간주하고 반환

    > 캐시 및 항목 삭제 method

    - cache.delete(request)
      - 캐시 항목 삭제
      - Request 또는 URL을 매개변수로 사용
      - cache,match() 마찬가지로 옵션 사용 가능
      ```javascript
      cache.delete("/example/file.txt", {
        ignoreVary: true,
        ignoreSearch: true,
      });
      ```
    - cache.delete({name})
      - 캐시 삭제
      - cache name을 매개변수로 받음
      - 함수는 캐시가 존재하고 삭제된 경우 true로 확인되고 그렇지 않은 경우 false로 확인되는 Promise를 반환
