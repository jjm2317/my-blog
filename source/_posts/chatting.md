---
title: chatting
date: 2020-11-16 16:12:01
tags:
---
# chatting

- 20201116-20201120 프로젝트에 사용할 채팅 기초 기능 공부 및 정리

## 환경 세팅

- npm init -y
  - 패키지 관리자 설치
- npm install express --save
  - express 모듈 설치
- npm install socket.io --save
  - socket 모듈 설치

## Socket.io

- 브라우저와 서버간의 양방향의 이벤트 기반 실시간 소통을 가능하게 해주는 라이브러리



### 소켓이란

소켓이란 네트워크상에서 동작하는 프로그램 간 통신의 종착점(Endpoint)이다.

프로그램이 네트워크에서 데이터를 통신할 수 있도록 연결해주는 연결부

**Endpoint**

IP주소와 port 번호의 조합을 뜻한다. 즉 최종 목적지이다. 사용자의 디바이스나 서버가 될수 있다.

- 데이터를 통신할 수 있도록 해주는 연결부이기 때문에 통신할 두 프로그램 모두에 소켓이 생성되야 한다.

**동작**

Server는 특정 포트와 연결된 소켓을 가지고 컴퓨터 위에서 동작하게 된다. 이 서버는 소켓을 통해 클라이언트측 소켓의 연결 요청이 있을때까지 기다리고 있다.



### Server API

**사용법**

- 모듈 가져오기

```
require('socket.io')
```

- Server 생성자 함수

```
//new 연산자 유무와 관계없이 동작한다.
const io = require ('socket.io')();
//또는
const Server = require('Socket.io');
const io = new Server();


```

**인수로 받는 값**

1. port :수신할 포트
2. option 객체 : 여러가지 옵션 지정 가능하다

추가내용 아래 참조 

 https://socket.io/docs/v3/server-api/ 



### 서버 구현 예시 코드(socket.io 문서)

```javascript
var app = require('http').createServer(handler)
var io = require('socket.io')(app);
var fs = require('fs');

app.listen(80);

function handler (req, res) {
  fs.readFile(__dirname + '/index.html',
  function (err, data) {
    if (err) {
      res.writeHead(500);
      return res.end('Error loading index.html');
    }

    res.writeHead(200);
    res.end(data);
  });
}

io.on('connection', function (socket) {
  socket.emit('news', { hello: 'world' });
  socket.on('my other event', function (data) {
    console.log(data);
  });
});
```

**코드 대략적인 설명**

HTTP 서버를 생성하여  80 번포트를 연결하여 해당 엔드포인트로 클라이언트의 요청을 대기하고 있는 것이다. 

그리고 만들어진 서버에 소켓을 생성하였다. 

**소켓이 생성된다면**

서버소켓이 생성되면 클라이언트 소켓은 이 엔드포인트로 연결을 요청한다.  연결이되면 서버와 클라이언트가 연결된 소켓을 통해 실시간 데이터 통신이 가능해진다.



**연결이 완료된이후**

연결이 되면 서버소켓은 새로운 소켓을 하나 더 만든다. 새로운 클라이언트의 연결요청을 받아햐하기 때문이다. 



## 서버 만들기

``` javascript
//서버 파일(app.js) 생성 후

//require : 모듈 불러오는 함수

//기본 내장 모듈 fs : 파일관련 처리
const fs = require('fs');

const express =require('express');

const socket = require('socket.io');

const http = require('http');

//express(): express 객체 생성
const app = express();

//기본 내장 모듈 http의 createServer메서드 : expree 객체인 app에 서버 생성 
const server = http.createServer(app)

//socket함수(서버객체) : 서버를 socket.io 에 바인딩
const io = socket(server) 



// 서버작업 없이 클라이언트에서 액세스하면 거부됨
// 아래 코드로  해당 폴더의 파일은 외부 클라이언트들이 접근가능
app.use('/css', express.static('./static/css'));
app.use('/js', express.static('./static/js'))




// get 방식으로 접속하면 콜백 호출
// response.send : 클라이언트로 서버가 데이터를 돌려준다
app.get('/', function(request, response) {
    //fs.readFile : 지정된 파일을 읽어서 데이터를 가져온다. 에러시 첫번 째 인수로, 아니면 데이터를 두번째 인수로 받아온다. 
    fs.readFile('./static/index.html', (err,data) => {
        if(err) {
           response.send('에러');
        }else{
            //응답 헤더에 내용 파일 정보 전달
            response.writeHead(200, {'Content-Type' : 'text/html'});
            // 데이터 전송
			response.write(data)
            //전송 완료: end() 필수 작성
 			response.end()
        })
    })
})

//socket.io 
//on() 첫번째 인수로 전달된 이벤트가 발생하면 콜백함수 실행됨
//io.sockets: 접속되는 모든 소켓

io.sockets.on('connection', socket=>{
    console.log('유저 접속됨')
    //send이벤트를 받을 경우 콜백 호출
    socket.on('send', data => {
        console.log('전달된 메시지:', data.msg);
    })
    // 접속 종료시 자동실행
    socket.on('disconnect', () => {
        console.log(접속 종료);
    })
})
//server
server.listen(8000,function(){
    console.log('서버 실행중')
})



```



**디렉토리**

- static
  - css
    - index.css
  - js
  - index.html

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Chatting</title>
  <link rel="stylesheet" href="/css/index.css">
  <script src="/socket.io/socket.io.js"></script>
  <script src = "/js/index.js"></script>

</head>
<body>
  <div id = "main">
    <input type="text" id = "test">
    <button onclick= "send()">전송</button>
  </div>
</body>
</html>
```

**index.js**

```javascript
let socket = io() ;
// 접속 이벤트 발생시 콜백 호출 ('connect는 기본 이벤트')
socket.on ('connect', ()=>{
  let $input = document.getElementById('test')
  $input.value = '접속 중'
})

const send = () => {
  let message = document.getElementById('test').value;

  document.getElementById('test').value = ''

  socket.emit('send', {msg: message});
}
```





## 채팅 기능 구현

구현할 기능

- 신규접속
- 메시지 전송
- 메시지 수신
- 접속종료 알림



**과정**

1. 클라이언트가 서버로 메시지 전송
2. 서버에서 받은 메시지를 다른 클라이언트에게 전송





### 서버코드

```javascript
// io.sockets 모든 소켓에 해당
// 커넥션 이벤트가 발생하면 newUser(사용자 정의 이벤트)이벤트에 의해 새유저 알림이 뜸
// 그리고 해당 새 소켓의 name 프로퍼티에 name 인수 데이터가 저장
// 모든소켓에 대하여 접속 사실을 알린다.
io.sockets.on('connection', socket => {
	socket.on('newUser', (name)=>{
	console.log(name +'이 접속하였습니다.');
	socket.name = name;
	
	io.sockets.emit('update', {type: 'connect', name: 'SERVER', message: name+'님이 접속하였습니다.'})
	})
})

// 메시지 이벤트
// message(사용자 정의) 이벤트가 발생하면 메시지의 이름에 소켓이름을 저장하고 전체에 데이터를 뿌린다.
socket.on('message', data => {
	data.name = socket.name
    console.log(data)
    socket.broadcast.emit('updata', data);
})

socket.on('disconnect', () =>{
    console.log(socket.name + '님이 나가셨습니다.');
    
   	socket.broadcast.emit('update', {type: 'disconnect', name: 'SERVER', message: socket.name + '님이 나가셨습니다.'})
})
```

### 클라이언트 코드

```javascript
let socket = io() ;
socket.on ('connect', ()=>{

  let name = prompt('반갑습니다!', '');
  if(!name) name = '익명';

  socket.emit('newUser', name);
})

socket.on('update', data => {
  console.log(`${data.name} : ${data.message}`)
})
const send = () => {
  let message = document.getElementById('test').value;

  document.getElementById('test').value = ''

  socket.emit('message', {type: 'message', message: message});
}
```

