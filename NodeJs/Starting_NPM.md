# NPM Project 시작하기

#### 1. NPM이란?

javascript 개발을 쉽게 하는 라이브러리들의 모음집

#### 2. NPM으로 시작하기

NPM을 설치한 후, 빈 Directory를 만들고 npm을 초기화 해준다.

```cmd
npm init
```

이렇게 하고 project의 이름과 버전과 같은 기본 정보를 입력하게 된다. 여기까지 하면 노드 기반의 프로젝트를 시작하기 위한 기본 설정 파일(package.json)이 준비가 된것이다.

```
[package.json의 예시]

{
  "name": "nodeserver",
  "version": "1.0.0",
  "description": "nodeserver test",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Noah",
  "license": "ISC",
}
```

이렇게 준비가 되면 'Express'라는 Node 기반의 웹서버 프레임워크를 사용해서 서버를 만들어볼 것이다. express를 설치하기 위해서는 다음 명령어를 실행시켜야 한다.

```
npm install express --save
```

여기서 '--save'의 의미는 express를 package.json에 넣어 둘텐데, 이 패키지를 설치하는 사람은 따로 express를 설치하는 명령어를 돌리지 않아도 패키지만 설치하면 express를 사용할 수 있게 하겠다는 의미다.

설치 후 pakage.json의 형태는 아래와 같다. dependecy에 express가 추가된 것을 볼 수 있다.

```json
{
  "name": "nodeserver",
  "version": "1.0.0",
  "description": "nodeserver test",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Noah",
  "license": "ISC",
  "dependencies": {
    "express": "^4.19.2"
  }
}
```

이제 express를 사용해서 app을 만들어 볼 것이다.

-끝-
