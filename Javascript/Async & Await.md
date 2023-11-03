## Async & Await

자바스크립트 ES8(ECMAScript 2017) 문법으로 콜백 함수와 프로미스의 단점을 보완하여 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 구현할 수 있는 문법입니다. 프로미스를 기반으로 동작하며, 프로미스의 후속 처리 메소드 없이 동기 처리처럼 프로미스가 결과를 반환하도록 구현할 수 있습니다. 

<br />

## Async

async 키워드를 함수 앞에 붙여 사용하며, 이렇게 만들어진 async 함수는 언제나 프로미스를 반환합니다. 프로미스가 아닌 값을 반환하더라도 async 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환합니다. 즉, async 키워드가 붙어있는 함수는 반드시 프로미스를 반환하고, 프로미스가 아닌 것은 프로미스로 감싸서 반환합니다.

```jsx
async function fuBao() {
  return '푸바오는 귀여워';
}
```

```bash
fuBao().then(v => console.log(v))

푸바오는 귀여워
Promise { <fulfilled>: undefined }
```

<br />

## await

await는 async 키워드로 감싸진 함수, 즉 async 함수 내에서만 작동합니다. 프로미스가 settled 상태(비동기 처리가 수행되어 성공 또는 실패를 호출한 상태)가 되기를 기다리다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환합니다.

await는 단어 의미 그대로 프로미스가 처리될 때까지 해당 함수의 실행을 기다리도록 만들어 줍니다. 그리고 프로미스가 처리되면 그 결과와 함께 해당 함수의 실행이 다시 시작됩니다. 프로미스가 처리되기를 기다리는 동안에 자바스크립트 엔진은 다른 일을 할 수 있기 때문에 CPU 리소스가 낭비되지 않습니다.

```jsx
async function getYourGitHubName(id) {
  const res = await fetch(`https://api.github.com/users/${id}`);
  const { name } = await res.json();
  return name;
}
```

```bash
getYourGitHubName("dev-junehee");

Promise {<pending>}
[[Prototype]]: Promise
[[PromiseState]]: "fulfilled"
[[PromiseResult]]: "JuneHee"
```

- fetch 함수가 수행한 HTTP 요청에 대한 서버 응답이 도착해서 fetch 함수가 반환한 프로미스가 settled 상태가 될 때까지 대기합니다.
- fetch 함수가 반환한 프로미스가 settled 상태가 되면 프로미스가 resolve한 처리 결과가 res 변수에 할당됩니다.

<br />

### Quiz

다음 함수에서 콘솔에 출력되는 값과, 해당 함수의 소요 시간은 무엇일까요?

```jsx
async function numberGame() {
  const a = await new Promise(resolve => setTimeout(() => resolve(1), 7000))
  const b = await new Promise(resolve => setTimeout(() => resolve(3), 5000))
  const c = await new Promise(resolve => setTimeout(() => resolve(5), 3000))
  const d = await new Promise(resolve => setTimeout(() => resolve(7), 1000))
  
  console.log([a, b, c, d]);
}
```

- 정답은?
    
    16초
    [1, 3, 5, 7]
    
<br />

위에 보이는 numberGame 함수는 내부에 있는 모든 프로미스에 각각 await 키워드가 사용되었습니다. 그러나 numberGame 함수가 가지고 있는 4개의 비동기 처리들은 앞선 비동기 처리가 수행될 때까지 기다릴 필요가 없습니다. 왜냐하면 각각의 로직이 실행하는데 서로가 연관되어 있지 않기 때문입니다. 아래와 같이 수정할 수 있으며 모든 프로미스에 await 키워드를 사용하는 것을 주의해야 합니다.

```jsx
async function numberGame() {
  const res = await Promise.all([
    new Promise(resolve => setTimeout(() => resolve(1), 7000)),
    new Promise(resolve => setTimeout(() => resolve(3), 5000)),
    new Promise(resolve => setTimeout(() => resolve(5), 3000)),
    new Promise(resolve => setTimeout(() => resolve(7), 1000))
  ])
  
  console.log(res);
}
```

```bash
numberGame();

[1, 3, 5, 7]
```

<br />

## 에러 처리

async/await에서는 try ~ catch를 사용하여 에러를 처리할 수 있습니다. async 함수 내에서 catch 문을 사용해서 에러를 처리하지 않을 경우 async 함수는 발생한 에러를 reject하는 프로미스를 반환합니다. 그렇기 때문에 async 함수를 호출하고 Promise.prototype.catch 후속 처리 메소드를 통해 에러를 캐치할 수도 있습니다.

```jsx
async function getYourGitHubName(id) {
  try {
    const res = await fetch(`https://api.github.com/users/${id}`);
    const { name } = await res.json();
    return name;
  } catch (error) {
    console.error(error)
  }
}
```

```jsx
async function getYourGitHubName(id) {
  const res = await fetch(`https://api.github.com/users/${id}`);
  const { name } = await res.json();
  return name;
}

getYourGitHubName("dev-junehee")
  .then(console.log)
  .catch(console.error)
```
