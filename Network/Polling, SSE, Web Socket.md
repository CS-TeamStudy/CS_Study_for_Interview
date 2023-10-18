## 기술 등장 배경
 
전통적인 HTTP통신은 그 특성상 요청/응답이 발생하면 연결은 끊어진다. 

따라서 서버에서 이벤트 및 상태변화가 발생하였을 때 클라이언트는 이 변화를 요청을 하기 전까지 반영하지 못한다.

short polling, long polling, websocket, server sent events 등은 클라이언트가 실시간으로 서버의 상태를 반영하기 위해 고안된 방법 및 기술들이다.

## Polling
- HTTP 프로토콜
- 주기적으로 request를 보내는 방법

### short polling

![](https://velog.velcdn.com/images/junsgk/post/b7c31d4a-7b8e-4600-a642-234be577b0e4/image.png)

클라이언트에서 고정된 시간 간격으로 서버에 request를 보냄<br/>
👇<br/>
서버는 request를에 대해 바로 response를 줌 <br/>
👇<br/>
시간이 되면 다시 서버에 request를 보냄... 반복

#### 장점
- 클라이언트에서 설정할 수 있다.
- 쉽다.

```js
const getTodos = async () => {
  try {
    const response = await fetch(
      'https://jsonplaceholder.typicode.com/todos'
    );
    const todos = await response.json();
    console.log(todos);
  } catch (error) {
    console.error(error);
  }
};
setInterval(() => {
  getTodos();
}, 1000);
```


#### 단점
- 진짜 real time은 아님, real time과 최대한 가깝게 구현하기 위해서는 요청 주기를 짧게 잡아야함.
- network load
- 비효율적

소규모 프로젝트에서만 부분적으로 사용될 수 있으나 권장되지 않음

### long polling

![](https://velog.velcdn.com/images/junsgk/post/0e2799b5-61f8-4ad7-ab57-2534e6ac7210/image.png)


클라이언트에서 request를 보냄<br/>
👇<br/>
서버는 요청에 대해 일정시간동안 대기 하다가 조건을 만족(이벤트발생)하면 response를 보냄<br/>
👇<br/>
클라이언트에서 request를 보냄

#### 장점
- real time
- short polling보다는 적은 network load

#### 단점
- 서버에서 설정해야함, 복잡
- 서버 부담 가중

## SSE(Server Sent Events)
![](https://velog.velcdn.com/images/junsgk/post/6364a103-e68b-4042-8a78-1a2f01b7c5d0/image.png)


- 단일 HTTP연결을 통해 서버에서 클라이언트로 데이터를 전달하는 방법
- 헤더 `text/event-stream` 설정 필요함

```js
(sevrer)
const express = require('express');
const app = express();
app.get('/', (req, res) => res.send('hello!'));

app.get('/sse', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.write('data: Initial message\n\n');

  const intervalId = setInterval(() => {
    res.write(`data: New message at ${new Date().toLocaleTimeString()}\n\n`); // 형식 중요
  }, 1000);
});

app.listen(8080, () => console.log('server running'));

(client) / 브라우저 콘솔에 입력하여 확인
let sse = new EventSource("http://localhost:8080/sse");
sse.onmessage = console.log
```

클라이언트에서 request(text/event-stream)를 보냄<br/>
👇<br/>
서버에서 일방적으로 데이터들을 보냄<br/>
👇<br/>
서버에서 연결을 종료함

#### 장점
- real time
- HTTP위에서 동작하며 웹소켓보다 사용이 쉽다.

#### 단점
- 단반향 소통

## Web Socket
![](https://velog.velcdn.com/images/junsgk/post/b6a4f566-d4d0-41db-9ab1-0fb13457ada1/image.png)

- 최초 접속에서만 HTTP프로토콜을 사용함
- 이후 WS or WSS 프로토콜(TCP/IP기반)
- 프레임으로 구성된 텍스트와 바이너리만이 교환가능함
- socket.io를 라이브러리 추천

```js
(index.html)
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <script src="https://cdn.socket.io/socket.io-3.0.0.js"></script>
    <script defer src="app.js"></script>
  </head>
  <body>
    <ul></ul>
    <input placeholder="메시지" />
    <button>전송</button>
    <script>
      const socket = io('ws://localhost:8080');

      socket.on('message', (text) => {
        const el = document.createElement('li');
        el.innerHTML = text;
        document.querySelector('ul').appendChild(el);
      });

      document.querySelector('button').onclick = () => {
        const text = document.querySelector('input').value;
        socket.emit('message', text);
      };
    </script>
  </body>
</html>

(index.js)
const express = require('express');
const app = express();
const httpServer = require('http').createServer(app); 

const io = require('socket.io')(httpServer, {
  cors: { origin: '*' },
});

io.on('connection', (socket) => {
  socket.on('message', (message) => {
    io.emit('message', `${socket.id.substr(0, 2)} said ${message}`);
  });
});

app.get('/', (req, res) => res.sendFile(__dirname + '/index.html'));

httpServer.listen(8080, () => console.log('서버 열림'));


```

#### 장점
- real time
- 양방향 소통
- 현대 대부분의 실시간 소통이 필요한 앱은 websocket을 사용함

#### 단점
- 확립된 규칙이 없이 sub-protocol이 존재함, 주고 받는 메시지의 형태를 약속하는 경우가 많다.
- 방화벽, 로드밸런싱, 프록시 문제가 자주 발생하며 구현이 상대적으로 어렵다.
