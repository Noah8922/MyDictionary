# Check Box with Select All check-box

## 체크박스와 전체선택

처음 앱을 만들 때 체크박스와 전체선택 체크박스를 구현해야 하는 일이 있었음.

그때 나의 생각은

> 체크박스마다 개별 state를 주어서 true/false를 주고 전체선택 체크박스에서는 전체를 true/false가 되게 하자.

였다. 그럴싸해보이지만 굉장히 위험한 생각이었다.

이렇게 됐을 때 문제점은 관리해야할 state가 무한히 늘어난다는 점과, 2개의 state가 유기적으로 연동되게 하는 것이 매우 어렵다는 점이었다.

그래서 state는 하나이고 그 state를 바꿀 수 있는 체크박스가 2개라고 생각하면 더 쉽다.

> 만약, 체크박스가 한두개가 아니고 몇십개라면, 이를 어떻게 한 state로 관리해야 하나 싶었다.

생각해보면 체크박스라는게 2가지의 값을 가질 수 밖에 없는데 체크됨/안됨, 이렇게 2가지이다.

그래서 모든 체크박스의 state를 가진 배열을 만들고 그 체크박스가 가지고 있는 index에 있는 것만 state를 변경해준다면, state를 한곳에서 관리할 수 있다.

여기서 전체선택은 배열안에 있는 값들을 한번에 true 혹은 false로 만들어주면 되는 것이다.

예를 들어 설명해보자면, 아래와 같이 10개의 체크박스가 있고 이를 전체선택할 수 있는 체크박스가 하나 있고, 마지막에는 줄에는 각 번호의 선택 여부를 알 수 있는 내용이 있다고 해보자

![스크린샷 2022-11-15 오후 1 01 40](https://user-images.githubusercontent.com/98517680/201823444-91929dc9-7b4c-45a1-8541-516b5f2c40a9.png)

이를 구현하기 위해서는 state는 하나여야 하기 때문에 가장 먼저 10개의 state를 담는 배열을 만들어서 그 배열로 체크박스를 구현해 볼 수 있겠다.

```tsx
function App() {
  // 스프레드 연산자를 쓰지 않으면 길이가 10이라는 속성만 들어있는 배열이 나오게 된다.
  // 0 : (10) [empty × 10]
  const array4CheckBox = [...new Array(10)].map(() => false);
}
```

이렇게 하게 되면 10개의 false로 차있는 배열이 만들어지게 된다. 10개의 checkbox state는 여기서 관리하게 되는 것이다.

그러면 이제 이 배열을 초기값으로 가지는 변수와 handler를 만들기 위해 useState를 사용하여 만들어준 후에, 그 배열을 가지고 user번호와 각각의 체크박스를 그려준다.

```tsx
function App() {
  const array4CheckBox = [...new Array(10)].map(() => false);
  const [checkArray, setCheckArray] = useState<boolean[]>(array4CheckBox);

  return (
    <div className="App">
      {checkArray.map((item, idx) => (
        <div key={idx}>
          <h1>user{idx + 1}</h1>
          <input type={"checkbox"} />
        </div>
      ))}
    </div>
  );
}
```

이렇게 되면 각 체크박스에 속성을 부여하게 되는데, checked와 onChange 속성을 준다.

```tsx
function App() {
  const array4CheckBox = [...new Array(10)].map(() => false);
  const [checkArray, setCheckArray] = useState<boolean[]>(array4CheckBox);

  return (
    <div className="App">
      {checkArray.map((item, idx) => (
        <div key={idx}>
          <h1>user{idx + 1}</h1>
          <input
            type={"checkbox"}
            checked={item}
            onChange={() => {
              const newArray = [...checkArray];
              newArray[idx] = !newArray[idx];
              setCheckArray([...newArray]);
            }}
          />
        </div>
      ))}
    </div>
  );
}
```

checked는 map으로 받은 item이 곧 boolean이기 때문에 그대로 사용하게 되고 setCheckArray를 통해 새로운 배열을 만들어서 update하게 된다.

그후에 전체선택 체크박스는 10개가 모두 선택 되었을 때와 아닐때를 구분해야하기 때문에 이를 위한 state를 만들어 주고 해당 UI도 그려준다. 그리고 전체 선택 여부를 알려주는 설명문도 추가할 수 있을 것이다.

```tsx
import { useState } from 'react';
import './App.css';

function App() {
  const array4CheckBox = [...new Array(10)].map(() => false)
  const [checkArray, setCheckArray] = useState<boolean[]>(array4CheckBox)
  const isAllChecked = checkArray.filter((item) => item === true).length === checkArray.length ? true : false
  return (
    <div className="App">
      <h1>전체선택</h1>
      <input type={"checkbox"} checked={isAllChecked} onChange={() => {
        isAllChecked ?
          setCheckArray([...new Array(10)].map((item) => false)) :
          setCheckArray([...new Array(10)].map((item) => true))
      }} />
      {checkArray.map((item, idx) => (
        <div key={idx}>
          <h1>user{idx + 1}</h1>
          <input type={"checkbox"} checked={item} onChange={() => {
            const newArray = [...checkArray]
            newArray[idx] = !newArray[idx]
            setCheckArray([...newArray])
          }} />
        </div>
      ))}
      <hr />
      <div style={{ display: 'flex', justifyContent: "center" }}>
        {checkArray.map((item, idx) => (
          <p key={idx}>{idx + 1}번 : {item ? "체크됨" : "체크안됨"} </p>
        ))}
      </div>
    </div>
  );
}

export default App;
```
__checked 와 onChange를 같이 사용하지 않으면 controlled vs uncontrolled error가 발생하니 꼭 같이 사용 할 수 있도록!__
## 결론

이렇게 되면 CheckArray라는 하나의 state를 가지고 모두가 움직이기 때문에 결과가 하나로 모이게 되고 데이터가 산재되지 않아 관리하기 쉽다는 장점이 있다. 앞으로 체크박스를 만들어야 한다면 위와 같은 방식으로 만들고자 한다.