# Custom Hook

## Custom hook 사용 이유

우리가 코드를 작성하다보면, 같은 코드의 내용을 여러 파일에서 반복해서 작성할 때가 있다. 주로 같은 기능을 하는 코드이지만 컴포넌트가 다르다는 이유로 반복해서 작성해야 하는 경우가 다반사다.

대표적인 경우가 Input을 작성할 때인데, 컴포넌트에서 Input을 다루게 되면 보통 아래와 같은 코드 작성하게 된다.

```javascript
import { useState } from "react";
import "./App.css";

function App() {
  const [inputValue, setInputValue] = useState("");

  const handleChange = (e) => {
    setInputValue(e.target.value);
  };

  return (
    <div className="App">
      <h1>Hello World</h1>
      <input value={inputValue} onChange={handleChange} />
      <button onClick={() => alert(inputValue)}>{"확인"}</button>
    </div>
  );
}

export default App;
```

이렇게 작성했을 때의 문제점은 여기에 input이 또 생기게 되면, 같은 코드를 그만큼 똑같이 반복해줘야 한다는 점이다. 즉,input이 늘어날때 마다 useState, handler가 늘어나게 된다는 것이다.

```javascript
import { useState } from "react";
import "./App.css";

function App() {
  const [inputValue, setInputValue] = useState("");
  const [inputValue2, setInputValue2] = useState("");
  const [inputValue3, setInputValue3] = useState("");

  const handleChange = (e) => {
    setInputValue(e.target.value);
  };

  const handleChange2 = (e) => {
    setInputValue2(e.target.value);
  };

  const handleChange3 = (e) => {
    setInputValue3(e.target.value);
  };

  return (
    <div className="App">
      <h1>Hello World</h1>
      <input value={inputValue} onChange={handleChange} />
      <input value={inputValue2} onChange={handleChange2} />
      <input value={inputValue3} onChange={handleChange3} />
      <button onClick={() => alert(inputValue, inputValue2, inputValue3)}>
        {"확인"}
      </button>
    </div>
  );
}
```

사실 이런 문제는 리액트에서는 자주 발생되는 일이었다. 리액트에서는 컴포넌트안에서 State도 관리하고 있고, View도 그려주고 있으며, 동시에 컴포넌트안에서 일어나는 Event들을 처리하고 있다. 

이는 컴포넌트의 활용성이 높다는 의미이기도 하지만 하나의 컴포넌트 안에서 너무 많은 일을 처리하고 있기도 하고 단순히 View만 그려주고 싶었는데, 그 View를 그리기 위한 코드가 줄줄이 고구마 처럼 엮여 있다면 한번 그 view를 작성할 때마다 또 그 view를 위한 state와 fucntion을 하나의 set로 옮겨 다니는 불편함이 생기게 되고 이를 경계하지 않는다면 컴포넌트의 재사용성을 저해하는 아주 비효율적인 방법이라고 볼 수 있다. ~~(차라리 안쓰고 말지)~~

이러한 문제를 해결하기 위해서 등장한 것이 ["Presentational and Container Pattern"](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)인데,

쉽게 말해서, View와 Logic을 분리하는 것이다. 둘의 특징은 아래와 같다.

먼저 Presentational Component는 View와 관련된 부분인데 아래와 같은 수칙을 지켜야 한다.
- app에 대한 의존성이 없어야 한다. 즉, 다른 앱에서 복붙을 해도 아무런 문제가 없어야 한다.
- Atomic Design에 의거했을때, atom단위로 만들어야 한다.
- 상태는 UI에 대한 상태만 가질 수 있다.
- 필요시 Visual에 필요한 Props를 받아올 수 있다.

Container Component는 Logic 관련된 부분인데, 이는 아래와 같은 수칙을 지켜야 한다.
- 어떤 동작(기능)을 할 것인가에 대한 내용이다.
- 절대로 Mark Up에 관여해서는 안된다.
- 주로 state나 function을 가지고 있다.

이렇게 코드를 나눠서 작성함으로써 얻을 수 있는 이득은 크게 2가지 인데, 하나는 코드의 가독성이고 다른 하나는 컴포넌트의 재사용성이다. 

코드의 가독성은 화면과 뒤에서 작동하는 기능을 분리함으로써 코드를 읽는 사람으로 하여금, 화면에서 버그가 생기면 Presentational Component를, 기능에서 버그가 발생하면 Container Component를 수정할 수 있도록 할 수 있다는 점이다.

컴포넌트의 재사용성은 View와 Logic이 분리되어 있기 때문에, View만 사용하고 싶다면 view컴포넌트만 따로 가져가서 사용 할 수 있도록 된것이다. _(물론 input처럼 핸들러가 필요하다면 기능도 같이 가져와겠지만 그에 대한 내용은 밑에서 다룰 예정이다.)_

하지만 2019년 custom hook이 등장하면서 해당 pattern을 처음 소개했던 react의 창시자인 Dan Abramov마저도 Presentational and Container Pattern 대신 Custom HOOK을 사용할 것을 권하고 있다.

## Custom Hook의 사용 방법

위의 코드로 예시를 들어보자면, 먼저 View 파트와 Logic 파트를 구분해 줘야 한다.
위 코드에서 View 파트는 return 해주는 부분일 테고, Logic 파트는 state와 handle 부분일테다.

여기서 custom hook을 사용하게 되는데, 
