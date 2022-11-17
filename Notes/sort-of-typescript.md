# 유용한 타입스크립트 Advance.ver

_(계속 채워나갈 예정임.)_

## 1. Omit

- 미리 정해놓은 타입에서 특정 타입만 제거한 타입을 재정의할 때 주로 사용한다.
- MUI 같은 경우, 받아온 Props 타입에서 내가 재정의 하고 싶은 부분을 제외하여 정의할 때도 자주 사용
- 보통 가장 큰 범위의 interface를 작성한 후 그 안에서 Omit을 사용하여 부분 사용을 가능케 할 수 있다.

#### 예시

```typescript
interface ProductType {
  id: number;
  name: string;
  label: string;
  price: number;
  stock: number;
}

type SelectedProduct = Omit<ProductType, "stock">;
type LeftProduct = Omit<ProductType, "price">

const mySelectedProductList: SelectedProduct[] = [
  {
    id: 1,
    name: "blue Jean,
    label: "청바지",
    price: 25000,
  },
  {
    id: 2,
    name: "white Jean,
    label: "하얀 바지",
    price: 28000,
  },
];

const nowLeftProductList : LeftProduct[] = [
  {
    id: 1,
    name: "blue Jean,
    label: "청바지",
    stock: 20,
  },
  {
    id: 2,
    name: "white Jean,
    label: "하얀 바지",
    stock: 10,
  },
]
```

## 2. Partial & Required

- 정해진 타입을 동일하게 유지한 채로 모든 프로퍼티들을 Optional하게 변경해준다.
- 반대로 required는 정해진 타입의 프로퍼티 내용은 유지한 채 모든 프로퍼티들을 required로 변경해준다.
- 사실 하나만 쓰는건 큰 활용도는 없다고 생각하지만 둘을 동시에 쓴다면 써볼만 하다.
  ~~(물론 optional과 required를 하나의 타입아네 쓰는 것이 훨씬 간편하고 좋지만, 상황에 따라 두개의 타입을 optional과 required로 합쳐야 할 경우 유용하게 쓸 수 있을것이라 본다.)~~

#### Partial 예시

```typescript
interface Todo {
  id: number;
  text: string;
  isDone: boolean;
}

type OptionalTodoType = partial<Todo>;

interface OptionalTodoInterface {
  id?: number;
  text?: string;
  isDone?: boolean;
}

// OptionalTodoType과 OptionalTodoInterface는 완전 동잃한 타입이다.
```

#### Required 예시

```typescript
interface Todo {
  id: number;
  text?: string;
  isDone: boolean;
}

type OptionalTodoType = required<Todo>;

interface OptionalTodoInterface {
  id: number;
  text: string;
  isDone: boolean;
}

// OptionalTodoType과 OptionalTodoInterface는 완전 동잃한 타입이다.
```

#### 둘을 같이 사용한다면,

```typescript
// User의 Information 타입을 정의한다면 아래와 같이 할 수 있지만, 
interface UserInformationOne {
  id: string;
  uid: string;
  name: string;
  age?: number;
  profile?: string;
  phone?: string;
}

// 이를 둘로 난눈다면,
interface RequiredUserInformation {
  id: string;
  uid: string;
  name: string;
}

interface OptionalUserInformation {
  age: number;
  profile: string;
  phone: string;
}

type UserInformationTwo = RequiredUserInformation & Partial<OptionalUserInformation>;

// UserInformationOne와 UserInformationTwo는 동일한 타입이다.
```
