## LazyLoading

- 페이지를 불러오는 시점에 당장 필요하지 않은 리소스들을 추후에 로딩하게 하는 기술
- 즉, 사용자가 보지 않는 것들을 당장 로딩하지 않고, 나중에 사용자가 필요로 하는 시점에 로딩하는 것
- lazy loding 을 하는 대표적인 방법

### 사용 예시

- Intersection Observer API를 이용하여 image load를 일으키는 방법

  - 엘리먼트가 뷰포트에 들어간 것은 API가 감지했을 때, isIntersecting 속성을 이용해서 URL을 data-src 속성에서 src 속성으로 이동시켜서 브라우저가 이미지를 로드하도록 트리거를 일으킴
  - 전부 로드되면 lazy 클래스명을 이미지에서 삭제하고 부착했던 옵저버를 제거

  ```javascript
  document.addEventListener("DOMContentLoaded", function () {
    var lazyloadImages;

    if ("IntersectionObserver" in window) {
      lazyloadImages = document.querySelectorAll(".lazy");
      var imageObserver = new IntersectionObserver(function (
        entries,
        observer,
      ) {
        entries.forEach(function (entry) {
          if (entry.isIntersecting) {
            var image = entry.target;
            image.src = image.dataset.src;
            image.classList.remove("lazy");
            imageObserver.unobserve(image);
          }
        });
      });

      lazyloadImages.forEach(function (image) {
        imageObserver.observe(image);
      });
    } else {
      var lazyloadThrottleTimeout;
      lazyloadImages = document.querySelectorAll(".lazy");

      function lazyload() {
        if (lazyloadThrottleTimeout) {
          clearTimeout(lazyloadThrottleTimeout);
        }

        lazyloadThrottleTimeout = setTimeout(function () {
          var scrollTop = window.pageYOffset;
          lazyloadImages.forEach(function (img) {
            if (img.offsetTop < window.innerHeight + scrollTop) {
              img.src = img.dataset.src;
              img.classList.remove("lazy");
            }
          });
          if (lazyloadImages.length == 0) {
            document.removeEventListener("scroll", lazyload);
            window.removeEventListener("resize", lazyload);
            window.removeEventListener("orientationChange", lazyload);
          }
        }, 20);
      }

      document.addEventListener("scroll", lazyload);
      window.addEventListener("resize", lazyload);
      window.addEventListener("orientationChange", lazyload);
    }
  });
  ```

- Native Lazy Loading 방식

  - Google Chrome 브라우저 버전(Chroem 76)에서는 native lazy loading을 지원합
  - lazy loading을 구현하기 위해 임베딩 할 이미지에 'loading' 속성 추가
    - lazy - 뷰포트에서 일정한 거리에 닿을 때까지 로딩을 지연시킴
    - eager - 현재 페이지 위치가 위, 아래 어디에 위치하던 상관없이, 페이지가 로딩되자마자 해당 요소를 로딩함
    - auto - 이 속성은 디폴트로 로딩을 지연하는 것을 트리거합니다. 기본적으로 이것은 loading 속성을 쓰지 않을 것과 같음

  ```javascript
  <img src="example.jpg" loading="lazy" alt="..." />
  <iframe src="example.html" loading="lazy"></iframe>
  ```

### 장점

1. 사용자가 처음 웹사이트에 접근할 때 리소스의 일부만 다운로드 되기 때문에 콘텐츠의 제공 속도가 빠르다. 즉 성능이 향상된다.
2. 콘텐츠가 지속적으로 공급되기 때문에 사용자가 웹사이트를 이탈할 확률을 낮출 수 있다.
3. 사용자가 필요로 하는 경우에만 콘텐츠를 불러오기 때문에 배터리, 시간, 시스템 부담 등이 낮아진다. 즉 비용이 감소된다.
