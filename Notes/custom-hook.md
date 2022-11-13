# Custom Hook

## Custom hook 사용 이유

우리가 코드를 작성하다보면, 같은 코드의 내용을 여러 파일에서 반복해서 작성할 때가 있다. 주로 같은 기능을 하는 코드이지만 컴포넌트가 다르다는 이유로 반복해서 작성하는 것이다.

대표적인 경우가 Input을 작성할 때인데, 컴포넌트에서 Input을 다루게 되면 보통 아래와 같은 코드 작성하게 된다.

```typescript
import { useState } from 'react';
import './App.css';

function App() {
  const [inputValue, setInputValue] = useState("")

  const handleChange = (e) => {
    setInputValue(e.target.value)
  }
  
  return (
    <div className="App">
      <h1>Hello World</h1>
      <input value={inputValue} onChange={handleChange} />
      <button onClick={() => alert(inputValue)}>{"확인"}</button>
    </div>
  )
}

export default App;

```
