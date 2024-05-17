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

    <script src="/public/main.js"></script>
  </body>
</html>
```

이 상태로 3000번으로 get 요청을 하면 다음과 같이 404에러가 뜬다.

![image](https://github.com/Noah8922/MyDictionary/assets/98517680/0ade8c0c-f198-477c-9383-6648e6408b9a)

왜 그럴까?

server에서는
