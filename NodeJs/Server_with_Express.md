# Express로 서버를 띄워보자

#### 1. Express로 server를 불러오자

아래와 같이 express를 통해 app을 불러올 수 있다.
express는 server에 필요한 여러가지 API를 반환하는 함수인 것이다.

```javascript
var express = require("express");
var app = express();

app.listen(3000, () => {
  console.log("app is running on port 3000!!!");
});
```

이렇게 되면 3000번 포트로 들어오는 요청들을 처리하기 위해서 server가 대기중 상태로 돌입하게 된다.

<img width="452" alt="스크린샷 2024-05-17 오후 1 01 40" src="https://github.com/Noah8922/MyDictionary/assets/98517680/c487b53a-ecfb-43ef-86e6-8f497999d817">

이 상태에서 localhost:3000에 들어가면 아무것도 뜨지 않는다.
왜냐면 server에서는 get 요청에 대한 어떠한 처리도 하지 않았기 때문이다.

**localhost:3000 = 127.0.0.1:3000** (⬅️ 두개는 같은 말이다.)

일단 여기까지 하면 아무동작 하지 않는 서버가 뜨긴 뜬거다. ~~(초간단...)~~

Q. 그러면 아래와 같은 코드로 작성하면 어떤 것이 먼저 동작할까

```javascript
var express = require("express");
var app = express();

app.listen(3000, () => {
  console.log("app is running on port 3000!!!");
});

console.log("end of server code...");
```

정답은 아래의 console이 먼저 찍힌 후 콜백 함수로 호출되는 console이 찍힌다. (아래 console이 stack에 먼저 쌓인다.)

<img width="456" alt="스크린샷 2024-05-17 오후 1 13 32" src="https://github.com/Noah8922/MyDictionary/assets/98517680/274018f9-2ab7-413f-967b-86b5b7f77131">

<mark>이유는 node는 **비동기**이기 때문!!!</mark>
즉, callback에 등록된 함수가 끝나는 것을 기다리지 않고 다른 함수들을 먼저 실행 시킨다.

~~(이렇게 동작하는 거면 실제 서버가 동작하기 전에 결정되어야 할 것들을 설정해도 될 듯? 서버가 꺼지면 날아가도 상관없는 고정 정보들...)~~

근데 또 아래와 같이 작성하면 loop가 다 돌기 전까지 server가 동작을 안한다는 소리이기도 하니까 주의가 필요할 듯 하다.

```javascript
var express = require("express");
var app = express();

app.listen(3000, () => {
  console.log("app is running on port 3000!!!");
});

for (let i = 0; i < 10000; i++) {
  console.log("end of server code", i);
}
```

#### 2. nodemon 설치

nodemon은 파일의 변화를 감지해서 server를 자동으로 종료 후 실행하는 라이브러리

```cmd
npm install nodemon -g
```

-g는 global의 약자로 PC의 어느 directory에서 nodemon을 사용할 수 있도록 한다.

_(이렇게 했을 때 권한 오류 날 수 있음. sudo를 붙이면 해결됨.)_

실행은 node 대신 nodemon을 작성해주면 된다.

```cmd
nodemon app.js
```

<img width="547" alt="image" src="https://github.com/Noah8922/MyDictionary/assets/98517680/23e0ec71-5eea-445b-b2f8-83006d15544f">

-끝-
