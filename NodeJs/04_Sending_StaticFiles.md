# Static direcotry 설정

#### 1. app.use()

static 파일은 정적인 파일을 말한다. js 파일이나 css 파일처럼 변동이 없는 파일을 의미한다.
그래서 앞에서 html 파일을 보내듯이 js 파일을 보내려고 하는 경우에 사용한다.

예를 들어 앞에서 만든 main.html에 script를 추가한다고 해보자. src는 같은 directory에 있다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>main.html</title>
  </head>
  <body>
    <h1>main.html</h1>

    <p>
      Lorem ipsum dolor sit, amet consectetur adipisicing elit. Id, fugiat.
      Accusamus doloremque debitis officia! Earum numquam quam velit fuga, nihil
      illo iste, adipisci id perspiciatis repellat fugiat cupiditate praesentium
      ab.
    </p>

    <script src="main.js"></script>
  </body>
</html>
```

이 상태로 3000번으로 get 요청을 하면 다음과 같이 404에러가 뜬다.

![image](https://github.com/Noah8922/MyDictionary/assets/98517680/0ade8c0c-f198-477c-9383-6648e6408b9a)

왜 그럴까?

src가 main.js인 경우에는 server에 **127.0.0.1:3000/main.js** 로 get 요청을 날리는 것과 동일한데,
Node server에서는 해당 URL로 들어오는 요청에 대한 처리가 되어 있지 않기 때문에 404 Error가 뜨는 것이다.

그러면 앞에서 했던 것 처럼 동일하게 처리하여서 html파일을 넘겨주듯 main.js가 들어온 경우에 js 파일을 넘겨주는 방식으로 처리 할 수 있다.

```javascript
app.get("/main.js", (req, res) => {
  res.sendFile(__dirname + "/public/main.js");
});
```

**하지만! 매번 파일마다 이렇게 처리해 줄 수는 없는 노릇!**

그래서 js, css, image 같이 변동이 없는 static 파일들은 요청이 올 때 따로 처리하는 로직을 작성하지 않고 서버에서 바로 대응한다면 좋을 것이다.

그럴때 사용하는 것이 `app.use()` 이다.

`app.use()`를 사용하면 server에게

> 지정하는 directory안에 있는 파일들은 변경안되는 파일이니까 달라는 요청이 오면 그냥 주면 돼~

하는 것이다.

물론, 이렇게 하기 위해서는 static directory를 express에 등록하는 절차가 필요하다.

```javascript
// express야 public 이라는 이름의 directory를 static에 등록해줘~
app.use(express.static("public"));
```

이렇게 작성하게 되면 아래와 같이 main.js가 잘 받아지는 것을 확인 할 수 있다.

![image](https://github.com/Noah8922/MyDictionary/assets/98517680/4520b910-f203-441a-a3d0-7319d59a1f34)

동일한 방식으로 image 파일도 public directory안에 위치하게 되면 이미 static으로 등록해 두었기 때문에 server에서 받을 수 있게 된다.
