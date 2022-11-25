# 배열안에 있는 object 값 가져오기

## 1. object인데 value로 n개의 배열을 가진...

API로 호출 하여 들어온 데이터의 타입이 아래와 같은 형태라고 하자.

```typescript
type ProductKeyType = number;
type ProductValueType = [
  {
    storeId: number;
    storePlace: string;
    available: boolean;
    reason: string;
  }
];
```
이런 형태로 받은 데이터가 아래와 같다. 

```typescript
const fetchedProductData: Record<ProductKeyType, ProductValueType> = {
  1: [
    {
      storeId: 1,
      storePlace: "홍대",
      available: true,
      stock: 10,
    },
    {
      storeId: 2,
      storePlace: "이태원",
      available: true,
      stock: 5,
    },
  ],
  2 : [
    {
      storeId: 11,
      storePlace: "한남",
      available: true,
      stock: 10,
    },
    {
      storeId: 12,
      storePlace: "잠실",
      available: false,
      stock: "재고 소진",
    },
  ]
};
```
우리는 제품을 관리하는 사람이 화면을 보고

> 어떤 제품이 - 어떤 지점에서 - 지금 판매 가능한가 아닌가 - 안되면 이유는 무엇인가

에 대한 정보를 확인할 수 있도록 만든다고 하자.

## 생각보다 간단하거 아닌가...?

그냥 Table에서 자주 쓰는 배열에 map 메소드를 쓴다면 되지 않는가 싶었다.

처음에는 그렇게 생각했으나 문제는 데이터의 전체 타입이 배열이 아니라 객체이다. 즉 key : value 값으로 정의 되어 있어서 우리가 table에서 흔히 쓰는 `Array.map()`도 쓸수가 없다는 의미이다.

게다가 하나의 키에 물린 value가 2개라서 하나의 키를 두개의 데이터에 따로따로 할당해 줘야 하고, 2개의 value는 전체 vlaue의 value라서 더욱 까다롭다고 할 수 있겠다.~~(지금은 2개이지만 수없이 늘어난다고 하면 더욱 머리가 아파진다.)~~

## 해결기

#### 1. 먼저 key와 value를 구분해주자

데이터가 배열이 아니기 때문에 Table에 적합한 배열로 형태를 바꿔줘야 한다. Object에는 3가지 메소드가 있는데,
- `Object.keys()` : 객체의 key를 배열로 return하는 메소드.
- `Object.values()` : 객체의 value를 배열로 return하는 메소드.
- `Object.entries()` : 객체의 key, value를 배열로 return하는 메소드.

받은 data를 각 메소드로 실행해 보면 아래와 같은 모습이다.
![image](https://user-images.githubusercontent.com/98517680/203952753-e61b2eeb-4bcf-4fc9-b1fd-9babcf86c2c2.png)

#### 2. 각 항목에 맞게 넣어주자

이렇게 보면 제품 ID에 맞는 배열과 각 항목에 들어갈 정보가 담긴 배열이 잘 추출되었고 이를 잘 넣기만 하면 될 것 같다.

그래서 먼저 `tableHead`를 먼저 그리면 아래와 같이 그릴 수 있을 것이다.

![image](https://user-images.githubusercontent.com/98517680/203954045-6609cffa-93d4-4722-a87e-31afba3ef9ad.png)

> `Object.entries()` 로 받아온 데이터를 사용하면 안되는가?

단순히 모든 정보가 담긴 배열을 돌리는 건 어려울 뿐만 아니라, 실제 추출해야 할 data는 map으로 돌리는 data보다 한단계 더 깊게 있기 때문에, 뽑아내는 과정이 다소 깔끔하지 않다.

일단 `Object.keys()`로 받아온 배열로 제품 ID를 채우면 될 것이고, `Object.values()`로 나머지 테이블을 채우면 될 것이다.

```typescript
import { columns, data } from './data';

function App() {

  const tableHeader = columns
  const productKeys = Object.keys(data)
  const productValue = Object.values(data)

  return (
    <Box 
    width={"100vw"} height={"100vh"} sx={{ display : 'flex', justifyContent : 'center'}}>
      <TableContainer sx={{ width : "50%", height: "50%",  display: 'flex', justifyContent : 'center', alignItems : 'center'}}>
        <Table>
          <TableHead>
            <TableRow>
              {tableHeader.map((item, idx) => (
                <TableCell key={idx}>{item}</TableCell>
              ))}
            </TableRow>
          </TableHead>
          
          <TableBody>
            {productValue.map((item, idx) => (
              <TableRow key={idx}>
                <TableCell>{productKeys[idx]}</TableCell>
                <TableCell>{item.storeId}</TableCell>
                <TableCell>{item.storePlace}</TableCell>
                <TableCell>{item.available? "가능" : "불가능"}</TableCell>
                <TableCell>{item.reason && item.reason}</TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </TableContainer>
    </Box>
  );
}

export default App;
```
테이블을 채워야 하는 주된 데이터는 `value`값이기 때문에 해당 `data`로 `map`을 돌리고 제품 Id는 key만 추출해 놓은 배열을 value 배열의 index로 조회하여 가져오면 될 것이다. 이렇게 생각하고 코드를 실행하면, 아래와 같은 결과를 맞게 된다.

![image](https://user-images.githubusercontent.com/98517680/203956692-85452379-d55e-4d51-a730-6c41a57895e7.png)

#### 3. 왜 그럴까?

생각해보면 당연한 결과였다. key만 담긴 배열은 `[1,2]`이 인데, value가 담긴 배열의 index는 0,1,2,3이기 때문에 2와 3으로 조회한 값은 undefined고 때문에 빈 값이 되는 것이다.

**결국 value가 map을 도는 만큼 유효한 key가 필요한 것이다.**

#### 4. value개수 만큼 key를 만들어보자.

이렇게 하기 위해서는 각 value의 개수(N)만큼 돌면서 해당 key를 배열에 넣어주는 함수를 작성하면 된다.

```typescript
const duplicateKeys = Object.entries(data).map(item => {
    const result : number[] = []
    for (let val of item[1]) {
      result.push(+item[0])
    }

    return result
  }).flat(2)
```
위와 같이 작성하게 되면 아래와 같은 모양을 볼 수 있다.

![image](https://user-images.githubusercontent.com/98517680/203959440-04aec3bc-1b4c-4c22-aced-c2586a1c860c.png)

> ** flat(2)를 해주는 이유는 flat없이 하면,
![image](https://user-images.githubusercontent.com/98517680/203959003-9049aa3a-0c3f-49da-b3a2-78aed28b9d1c.png)
이렇게 2차원 배열이 나오게 되고, 이를 하나의 배열로 만들어주기 위해서 사용한 것이다.

이렇게 해주게 되면, 아래와 같은 내가 원하던 결과를 얻을 수 있다.

![image](https://user-images.githubusercontent.com/98517680/203960269-50e8e1f9-a095-4fc5-86fb-3aaadf4228c3.png)

#### 여기서 한걸음 더 나가면,

제품 ID가 1인 제품에 대한 정보가 2개이고, ID가 2인 제품에 대한 정보가 2개이기 때문에, 조금 깔끔한 표현을 위해서는 중복되는 ID는 표에서 안 보여주면 어떨까 싶었다.

대략 이런 모습이다.

![image](https://user-images.githubusercontent.com/98517680/203961350-9cd5b166-b3a9-4879-93d8-aa507bd365c9.png)

이렇게 하기 위해서는 배열에서 index로 조회한 현재 값과 이전값을 비교하여 보여줄 지 말지 정하는 함수가 필요하다고 생각했다.

```typescript
const getIndex = (array: number[], index: number) => {
  if (index - 1 < 0) return array[index];
  else return array[index] === array[index - 1]? "" : array[index];
}
```

이렇게 함수를 만들고서는 제품 ID자리에서 실행한다면 원하는 값을 아래와 같이 얻을 수 있었다.

```typescript
import { columns, data } from './data';


function App() {

  const tableHeader = columns
  const productKeys = Object.keys(data)
  const productValue = Object.values(data).flat(2)
  const duplicateKeys = Object.entries(data).map(item => {
    const result : number[] = []
    for (let val of item[1]) {
      result.push(+item[0])
    }
    return result
  }).flat(2)

  return (
    <Box width={"100vw"} height={"100vh"} sx={{ display : 'flex', justifyContent : 'center'}}>
      <TableContainer sx={{ width : "50%", height: "50%",  display: 'flex', justifyContent : 'center', alignItems : 'center'}}>
        <Table>
          <TableHead>
            <TableRow>
              {tableHeader.map((item, idx) => (
                <TableCell key={idx}>{item}</TableCell>
              ))}
            </TableRow>
          </TableHead>
          
          <TableBody>
            {productValue.map((item, idx) => (
              <TableRow key={idx}>
                <TableCell>{getIndex(duplicateKeys, idx)}</TableCell>
                <TableCell>{item.storeId}</TableCell>
                <TableCell>{item.storePlace}</TableCell>
                <TableCell>{item.available? "가능" : "불가능"}</TableCell>
                <TableCell>{item.reason && item.reason}</TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </TableContainer>
    </Box>
  );
}

export default App;

const getIndex = (array: number[], index: number) => {
  if (index - 1 < 0) return array[index];
  else return array[index] === array[index - 1]? "" : array[index];
}
```

매번 배열로 table을 그려보다가 object로 그려보려고 하니, 좀 많이 해맸던 작업이었음. 나중에 보면 도움 많이 될듯.

-끝-