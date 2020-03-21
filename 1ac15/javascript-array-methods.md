# 자바스크립트 배열 내장함수

**리액트에서는 불변성 유지를 위해 다음과 같은 배열 내장함수를 사용합니다.**

#### concat

concat 은 배열에 특정 값, 혹은 또다른 배열을 붙여주는 내장함수입니다. 그리고, _**기존의 배열은 건드리지 않습니다**_.

```js
const array = [0, 1, 2];
const after = array.concat(3); // 결과 [0, 1, 2, 3]
const stick = array.concat([4, 5, 6]); // 결과 [0, 1, 2, 4, 5, 6]
```

#### map

기존에 있는 배열을 가지고 특정 로직을 통하여 _**새로운 배열을**_ 만들어 낼 때 사용합니다.

```js
const array = [0, 1, 2, 3, 4, 5];
function square(number) {
  return number * number;
}

const squared = array.map(square);
// 결과: [0, 2, 4, 9, 16, 25]
```

map 내부에 바로 함수를 작성하셔도 됩니다.

```js
const array = [0, 1, 2, 3, 4, 5];
const squared = array.map(n => n * n);
```

for 반복문을 돌려서해도 되는데 왜 map을 사용할까요?

concat, map 모두 _**기존의 배열은 건들지 않고 새로운 값을 반환해준다는**_ 것이 핵심이기 때문입니다.

_\* 우리는 setState를 통해 상태관리를 할 때 새로운 객체를 생성해서 기존의 참조관계를 건들지 않고 이전값과 나중값을 비교해 최적화를 해줘야해요!_

#### map활용하기

#### 1\) 컴포넌트 배열 렌더링

마찬가지로 map함수를 사용해서 배열 JSX 의 배열로 변환 해봅시다.

```js
const ShowNumbers = () => {
  const array = [0, 1, 2, 3, 4, 5];
  const numberList = array.map((n, i) => <div key={i}>{n}</div>)
  return (
    <div>{numberList}</div>
  )
}
```

배열안의 값들을 효과적으로 렌더링 하기 위해서는 배열 요소 값 하나하나에 key값을 넣어줘야 한다고 말씀드렸죠?

**REMIND\) 그렇다면 key에 index를 넣는것은 좋은 습관일까요? key는 고유해야 한다고 말씀드렸죠? **

---- 오늘은 여기까지 배웠습니다. 밑에 나오는 내용은 다음시간에 이어서 진행하겠습니다. 미리 한번 살펴만 보세요 ---

#### 2\) 불변성을 지키면서 데이터 변환

```js
const data = [
  {
    id: 0,
    value: true
  },
  {
    id: 1,
    value: false
  }
];
const nextData = data.map(
  o => (o.id === 1)
    ? { ...o, value: !o.value }
    : o
);
```

이렇게 하면 기존 데이터를 수정하지 않으면서 새 값을 지니고있는 새 데이터를 만들 수 있습니다.

#### filter

filter 함수는 데이터 배열에서 특정 조건을 만족하는 원소들만 골라서 _**새 배열을 만들어줄 때 사용**_합니다.

```js
const numbers = [0, 1, 2, 3, 4, 5];
const gt = numbers.filter(n => n > 2);
// 결과: [3, 4, 5]
```

이것을 응용해서, 기존의 데이터는 그대로 두면서 특정 원소를 제거시킨 배열을 새로 만들어 낼 수도 있습니다.

예를들어 위 배열에서 3을 없애고 싶다면 이렇게 할 수 있습니다.

```js
const numbers = [0, 1, 2, 3, 4, 5];
const withoutThree = numbers.filter(n => n !== 3);
// 결과: [0, 1, 2, 4, 5]
```

마찬가지로 불변성을 지키며 데이터를 제거시켜야 할 때 유용하게 사용 할 수 있습니다.

