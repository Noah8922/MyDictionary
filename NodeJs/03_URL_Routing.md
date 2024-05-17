# URL Routing 처리

#### 1. get 요청 처리

우리가 어떤 주소창에 URL을 입력하고 Enter를 치면 서버로 get 요청이 가게된다.
지금의 경우 **127.0.0.1:3000**으로 요청을 하게 되면 3000 뒤에 '/'로 요청하게 되는 것이다.

그래서 아래와 같이 처리를 해줄 수 있다.

```javascript
var express = require("express");
var app = express();

app.listen(3000, () => {
  console.log("app is running on port 3000!!!");
});

/* get 요청 처리 */
app.get("/", (req, res) => {
  res.send("hi friend!");
});
```

서버의 응답을 정의해 두면 localhost:3000으로 접속했을 때 (⬅️ '/'라는 API로 get요청을 했을 때)
아래와 같은 화면을 볼 수 있다.

<img width="680" alt="image" src="https://github.com/Noah8922/MyDictionary/assets/98517680/1c0a2c80-c5b3-46e5-9d8a-1cb186f77d01">

서버에서 보내준 'hi friend!'라는 값을 출력하고 있다.
응답값은 html로도 보낼 수 있다.

```javascript
/* get 요청 처리 */
app.get("/", (req, res) => {
  res.send("<h1>hi friend!</h1>");
});
```

이렇게 보내게 되면, 브라우저에서 html을 해석한 후 그려주게 된다.

<img width="508" alt="image" src="https://github.com/Noah8922/MyDictionary/assets/98517680/31eb7daa-e0aa-419f-aa1e-7e0fbecac9f2">

**만약 server에서 html 파일을 보내려고 한다면??**

server에서 public/main.html 파일을 가지고 있다고 가정해 보자.

그러면 client에서 이걸 받아오기 위해서

> localhost:3000/public/main.html

이런식으로 요청하는 것이 아니라, main.html 파일을 받기 위한 API 주소를 설정하고 client에서는 그 주소에 맞게 요청해야 한다.

또한 server에서는 해당 API로 요청이 들어올 것을 대비해서 해당 파일이 위치한 절대 경로를 모두 작성해야 한다. ~~(이게 좀 번거로움)~~

이를 해결하기 위해서 Node에서 제공되는 식별자 '_\_\_dirname_'을 사용하게 되면 최상위 경로에서 현재 파일의 경로까지를 표현해준다. 이걸 사용하면 파일이 속한 directory 이름부터 시작하면 된다.

그래서 위 식별자를 포함하여 get 요청을 처리하는 코드를 수정하게 되면,

```javascript
/* get 요청 처리 */
app.get("/", (req, res) => {
  res.sendFile(__dirname + "/public/main.html");
});
```

이렇게 되면 main.html 파일에 접근하게 되고 아래와 같이 화면이 나오는 것을 볼 수 있다.

![image](https://github.com/Noah8922/MyDictionary/assets/98517680/21c7f5b9-6cc7-49b6-955c-f0b2c2516968)

-끝-
