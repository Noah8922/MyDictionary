# useInfiniteQuery를 이용한 무한 호출

## 1. useInfiniteQuery란?

useInfiniteQuery는 React Query에서 사용할 수 있는 API중 하나로, 파라미터만 변경하여 동일한 useQuery를 무한히 호출하여 서버로부터 데이터를 받아 올 때 사용한다. 대표적인 사용 예로는 "Infinite Scroll"가 있다.

## 2. 사용법

기본적인 사용법은 useQuery와 동일하다. uesQuery가 key와 Function을 정의하여 data를 받아오는 형태라면, useInfiniteQuery는 파라미터로 pageParam을 받는데 그 파라미터를 바꿔주는 함수가 같이 있는 셈이다.

> < 주의 >
> useQuery가 누구에게나 동일하게 적용될 수 없는 이유는 각자가 API Request를 보내는 url주소가 다르기 때문이다.~~(내가 계속 해맸던 이유가 서버로 보내는 요청 URL이 다르기 때문에 코드 적용이 일괄적으로 되지 않는데 있었다.)~~

#### 2-1. 서버에 Request API로 호출

예를 들어, 많은 양의 상품 데이터를 서버로부터 받아오는 경우가 있다고 하자. 보통 아래와 같은 코드를 작성 할 것이다.

```typescript
const fetchProductList = async () => {
  return axios.get(`http://loaclhost:4000/products/list`).data;
};
```

이렇게 request를 보내게 되면 default로 정해진 page와 size값으로 서버에서 data를 조회하게 된다.

대신 여기서 한번에 5개의 데이터만 받아오려고 한다면, 아래와 같이 변경하게 된다.

```typescript
const fetchProductList = async () => {
  return;
  axios.get(`http://loaclhost:1234/products/list?page=0&size=5`).data;
};
```

useInfinityQuery를 사용하게 되면, pageParam을 request API의 파라미터로 받기 때문에, 아래와 같이 사용 할 수 있게 된다.

```typescript
const fetchProductList = async (pageParam: number) => {
  return;
  axios.get(`http://loaclhost:1234/products/list?page=${pageParam}&size=5`)
    .data;
};
```

이렇게 하게 되었을 때 총 상품의 개수가 10개라면 2페이지, 20개라면 4페이지, 30개라면 6페이지가 될 것이다. 그랬을 때 저 pageParam은 총 페이지 중에서 조회하고 싶은 page가 되는 것이다.

여기까지 하면 서버로 요청하는 Request API는 준비된 것이다.

#### 2-2. 사용할 useInfiniteQuery 만들기

앞서 말했던 것 처럼 useInfiniteQuery는 useQuery와 동일하게 key와 Function을 가지고, parameter를 변경해주는 함수가 들어가 있다.

```typescript
const callProductListByInfinity = () => {
  const result = useInfiniteQuery(["infinity"], fetchProductList, {
    getNextPageParam: (lastPage, allPage) => {
      if (allPage.length < 5) {
        return lastPage.number + 1;
      } else {
        return undefined;
      }
    },
  });

  if (result.isSuccess) {
    return result;
  } else {
    throw Error;
  }
};
```
getNextPageParam은 이름에서도 알 수 있듯이 다음 조회하고 싶은 pageParam을 만드는 함수 되시겠다. 인자로 받고 있는 두 가지 중, lastpage는 호출된 가장 마지막에 있는 페이지 데이터를 의미하고, allpage는 호출된 모든 페이지 데이터를 의미한다.

그렇게 호출된 페이지 정보가 배열로 담겨서 오는데, 그 배열의 길이를 통해서 언제까지 페이지를 늘릴지 결정하게 된다.

#### 2-3. 만들어 놓은 useInfiniteQuery 사용하기

이렇게 작성하게 되면 이제 실제 코드에서 아래와 같이 사용 할 수 있게 되는 것이다.

```typescript
const { data, fetchNextPage } = callProductListByInfinity();

if (data) {
  return (
    <Box>
      {data.pages.map((page) => {
        const product: IProduct = page.data;

        return (
          <Box key={product.id}>
            <Typography>{product.id}.</Typography>
            <Typography>{product.name}</Typography>
          <Box>
        );
      })}
      {/* 클릭 시 다음 페이지 호출 */}
      <Button onClick={() => fetchNextPage()}>
        Next
      <Button>
    </Box>
  );
}
```

정의한 함수를 실행하고 나면 return값으로 오는 useInfiniteQuery의 여러가지 API들을 사용할 수 있게 된다.

그중에 많이 사용하게 되는 것은 결과값을 가진 data, 변경된 pageParams를 통해 Query를 호출 할 수 있게 하는 fetchNextPage 함수가 있다.

이러한 내용으로 작동하게 되면 data안에는 pageParam을 바꿔서 보낸 request의 값이 차곡차곡 쌓이게 되고 그걸로 Clinet에게 데이터를 보여줄 수 있게 된다.

-끝-
