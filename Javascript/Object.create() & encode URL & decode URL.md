## 202. How do you create an object with prototype 

### Object.create() 메소드 사용하여 prototype으로 부터 직접 객체생성을 할 수 있다. 

```js
const catPrototype = {
  name: 'default',
  sayMeow: function () {
    return `Meow I am ${this.name}`;
  },
};

const howoo = Object.create(catPrototype);

howoo.name = 'Howoo';
```

### Quiz.

```js
console.log(howoo);
console.log(howoo.meow());
```

<img width="146" alt="image" src="https://github.com/Fastcampus-Final-Team3/jober-frontend/assets/87072568/f3c8f62c-c3ed-4ce7-b0e9-7d58253f01c7">

## 210. How do you encode an URL

### 인코딩 하는 이유?

JavaScript에서 URL을 인코딩하는 이유는 URL은 특정한 형식을 따르고, 이 형식을 벗어난 문자들은 올바르게 전송되지 않을 수 있기 때문이다.
따라서 영어가 아닌 다른 언어가 포함되어있거나 특수 문자, 공백 등이 포함된 문자열을 안전하게 URL에 포함시키기 위해서는 인코딩 작업이 필요하다.

```js
const url = 'https://example.com/호우킹';
const encodedURL = encodeURI(url);
console.log(encodedURL); // https://example.com/%ED%98%B8%EC%9A%B0%ED%82%B9
```

## 211. How do you decode an URL

```js
const encodedURL = 'https://example.com/%ED%98%B8%EC%9A%B0%ED%82%B9';
const decodedURL = decodeURI(encodedURL); // URL이 아니라는 점 주의!
console.log(decodedURL); // https://example.com/호우킹
```
