**참고\)**

**LifeCycle API는 개념이 다소 추상적이기 때문에 초반에는 완벽한 이해가 어려운 것이 정상입니다.**

_**이러한 여러가지 LifeCycle API는 수업시간내내 프로젝트를 다루면서 사용해 볼 것입니다.**_** **

_**당장 완벽하게 이해가 되지 않더라도 너무 걱정하지마세요!**_

### 컴포넌트 라이프사이클 단계

라이프 사이클은 크게 세 단계로 나뉜다고 말씀드렸어요.

_마운트 - 업데이트 - 언마운트 _인데요, 도표를 보시면 다음과 같습니다.![](/assets/lc.png)이 중 자주쓰이는 것들은

constructor, render, componentDidMount, shouldComponentUpdate, componentDidUpdate, componentWillUnmount입니다.

나머지 값들은 사용하실일이 드물어요. 실제 프로덕션에서도 이 여섯가지 위주로 life cycle을 관리합니다.

### **마운트 단계**

#### constructor

```js
constructor(props) {
  super(props);
}
```

constructor는 컴포넌트가 브라우저에 나타나기전에 호출됩니다. 다시 말하면 render\(\)전에 호출이되는 라이프사이클 함수입니다.

마운트단계 초기에 실행이되는 메소드입니다.

리액트 구버전에서 state를 constructor안에 선언했다고 말씀드렸죠?

당연히 render가 되고 난 이후에는 state를 자유롭게 사용할 수 있어야하니까 state와 같은 값은 render전에 만들어져야합니다.

다만 constructor를 직접 쓸일은 그렇게 많지 않기 때문에 지금은 알아두고만 계셔도 충분합니다.

#### componentDidMount

```js
componentDidMount() {
  // 외부 api호출, DOM 에 관련된 작업 등
}
```

cdm\(componentDidMount\)는 컴포넌트가 브라우저에 반영이되고 나서 호출이됩니다.

dom관련 작업도 일단 브라우저에 dom이 모두 반영이되야 불러서 쓸 수 있겠죠?

따라서 dom에 관여하는 작업들은 render직후 마운트 또는 업데이트 시점에서 처리해줍니다.

또한 해당 컴포넌트에서 필요로 하는 데이터를 요청하기 위해 axios, fetch등을 이용하여 외부 api를 호출하는 작업도 주로 여기서 합니다.

axios, fetch를 통한 api호출은 3주차에 진행할 예정입니다.

### 업데이트 단계

#### shouldComponentUpdate

```js
shouldComponentUpdate(nextProps, nextState) {
  // return false 하면 업데이트를 안함
  return true;
  // 기본값으로는 true를 반환
}
```

shouldComponentUpdate는 컴포넌트 최적화를 할 때 많이 사용됩니다.

리액트에서는 변화가 발생하는 부분만 업데이트를 해줘서 성능이 꽤 잘 나온다고 했었죠?

하지만, 변화가 발생한 부분만 감지해내기 위해서는 Virtual DOM에 먼저 그립니다.

이 작업은 그렇게 부하가 많은 작업은 아니지만, 컴포넌트가 무수히 많이 렌더링된다면 얘기가 조금 달라집니다.

버츄얼돔도 CPU를 어느정도 사용하고 있는것은 사실이니까요.

쓸데없이 낭비되고 있는 이 CPU 처리량을 줄여주기 위해서 우리는 Virtual DOM 에 그려주는 것마저 줄여줘야 합니다.

이를 위해  shouldComponentUpdate를 작성합니다.

이 함수는 기본값으로 true 를 반환하는데,

우리가 따로 조건을 작성하여 경우에 따라 false 를 반환하면, 그 시점에는 render 함수를 호출하지 않습니다.

그리고 그만큼 렌더를 덜 해줌으로써 최적화를 해줄 수 있습니다.

shouldComponentUpdate는 당장 2주차에 사용해 볼 것입니다.

#### componentDidUpdate

```js
componentDidUpdate(prevProps, prevState, snapshot) {

}
```

이 API는 컴포넌트에서 업데이트 시점에서 render\(\) 를 호출하고난 다음에 발생하게 됩니다.

이 시점에선 this.props 와 this.state 가 원래 값에서 다른 값으로 업데이트 된 시점입니다.

prevProps는 이전 props값, this.props는 현재 부모로부터 받은 props입니다

prevState는 이전 state값, 그리고 this.state는 현재 state값 입니다.

이렇게 비교할 수 있는 이유는요? 당연히 앞서 배운 불변성 덕분이겠죠?

#### 언마운트 단계

#### componentWillUnmount

```js
componentWillUnmount() {
  // 이벤트 및 연동된 외부 라이브러리 제거
}
```

언마운트 시점은 브라우저에 렌더된 컴포넌트가 사라지는 시점입니다.

WillUnmount는 해당 컴포넌트가 사라지기 직전에 호출되는데요,

여기서는 주로 등록했었던 이벤트 및 연관 외부 라이브러리 호출등을 제거합니다.

그렇지 않으면 나중에 메모리 누수의 위험이 있어요!

_**우리는 이러한 여러가지 LifeCycle API를 수업시간내내 프로젝트를 다루면서 사용해 볼 것입니다.**_

_**당장 완벽하게 이해가 되지 않더라도 너무 걱정하지마세요!**_

