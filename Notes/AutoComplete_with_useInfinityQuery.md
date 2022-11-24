# AutoComplete Select Box with useInfinityQuery

## AutoComplete란?

기본적으로는 selectBox와 같은 기능을 하면서, MenuItem을 Drag로 찾지 않고 검색을 가능하게 해주는 MUI에서 제공하는 컴포넌트이다. 

![AnyConv com__203685531-dc0996de-f569-4e3e-a82b-49df18debb9b](https://user-images.githubusercontent.com/98517680/203685699-aecf8223-f041-46c1-887a-0875eb017d1c.gif)


궁금하다면 → [여기로](https://mui.com/material-ui/react-autocomplete/)

## 과제는 무엇이냐면,

검색이 가능한 selectBox를 만들고 싶은데, menuItem 개수의 확장성을 생각했을 때 한번에 많은 Item 보여주는 것이 아니라 일정 개수로 끊어서 보여주고 항상 마지막 menuItem에 붙어있을 "더보기"를 클릭했을 때만 추가 menuItem을 더 붙여서 보여주고 싶은 것이다.

여기서 추가로 불러와서 보여주는 것은 useInfinityQuery를 사용한다면 구현이 가능한 내용이고, 문제는 이를 MUI AutoComplete에서 구현하는 것이었다.

## 해결기

일단 기본적인 AutoComplete의 코드를 살펴보면 아래와 같이 생겼다.

```typescript
import * as React from 'react';
import TextField from '@mui/material/TextField';
import Autocomplete from '@mui/material/Autocomplete';

export default function ComboBox() {
  return (
    <Autocomplete
      disablePortal
      id="combo-box-demo"
      options={top100Films}
      sx={{ width: 300 }}
      renderInput={(params) => <TextField {...params} label="Movie" />}
    />
  );
}
```
먼저 찬찬히 살펴봤을 때, disablePortal은 공식문서에 따르면 아래와 같은데,
![image](https://user-images.githubusercontent.com/98517680/203686347-f2421e87-c715-47d3-a3c0-65716e12c729.png)

정리하자면, popper로 뜨는 select가 가능한 menuItem이 DOM 계급에서 부모 컴포넌트 아래로 들어가게 된다는 것이다. ~~(당연한거 아닌가...아님 말고)~~ 

또 중요한게, options인데 이게 바로 이 안에서 렌더링 될 data의 원본 상태를 정의하는 것이다. 공식 문서에 의하면 options의 data type은 반드시 배열이어야 한다.

![image](https://user-images.githubusercontent.com/98517680/203687015-73734be3-7b3a-4b05-95d8-38b1135b09f7.png)

그리고 마지막으로 renderInput은 실제로 화면에 보여질 부분을 정의하는 것이다. 어떤 모습으로 정해진 범위안에서 그려졌으면 좋겠는지를 정의한다고 말 할 수 있다. return 타입도 rendering이 가능한 ReactNode 타입임을 볼 수 있다.

![image](https://user-images.githubusercontent.com/98517680/203687189-d87130c4-a313-4a2f-9445-c486d2876b85.png)

그러면 이제 2가지 고민이 든다.

1. useInfiniteQuery를 사용헤서 받아온 데이터를 어떻게 AutoComplete에 넣어줄 것인가
2. MenuItem 마지막에는 항상 "더보기"라는 항목이 있어야 하는데, 내가 받아온 데이터를 어떻게 조작해서 그게 가능하게 할 수 있을 것인가.

사실 첫번째 고민은 생각보다 어렵지 않게 해결이 가능하긴 하다. useInfiniteQuery로 받아오는 데이터의 결과값을 보고 입맛에 맞게 map과 filter, flat등을 조합하여 만들 수 있다.

두번째 고민도 의외로 간단하게 해결이 가능했다. useInfiniteQuery를 통해 받아와서 내 입맛에 맞게 데이터를 변경한 배열과 "더보기"라는 이름을 가진 dummyData를 입맛에 맞게 변경한 데이터의 타입에 맞게만 넣어준다면, 그 배열을 options에 넣을 수 있고 그게 rendering될 data의 원본이 되는 것이다.

여기서 새로운 고민이 생기게 된다.

> 더보기를 눌렀을 때 SelectBox가 닫히지 않았으면 좋겠는데...




