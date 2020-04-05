우리는 이번시간에 불변성관리를 쉽게 할 수 있도록 도와주는 immer라는 라이브러리를 배워볼 것입니다.

하지만 immer를 배우기 전에는 setState에 대해 조금 더 알아봐야 합니다.

수업시간에 보여드린 것과 같이 setState는 두가지 방식으로 사용될 수 있다고 말씀드렸죠?

첫번째는 우리가 지금까지 해오던 객체를 집어넣는 방식\(state change\)입니다.

```jsx
this.setState({
    counter: this.state.counter + 1
})
```

두번째는 setState안에 함수를 집어넣는 방식\(updater function\)입니다.

```jsx
this.setState((state) => {
    return {
        number: this.state.number + 1
    }
})
```

첫번째와 두번째 방식에는 간단한 차이가 존재합니다.

첫번째 방식은 this.setState를 아무리 많이해도 한번에 묶여서 작동하게 됩니다.

공식문서에서는 다음과 같이 말합니다.

"This form of`setState()`is also asynchronous, and multiple calls during the same cycle may be **batched** together.

Subsequent calls will override values from previous calls in the same cycle, so the quantity will only be incremented once."

즉 number을 1씩 증가시키는 setState를 열번 하더라도 batch형태로 작동하기 때문에 number값은 1만 증가된다는 것이지요.

```js
handleClick = () => {
    this.setState({
        counter: this.state.counter + 1
    })

    this.setState({
        counter: this.state.counter + 1
    })
}

<button onClick={this.handleClick}>

// 버튼을 클릭하면 1증가
```

만약에 object를 집어넣어서 state change를 하고싶은데 batch형태가 아니라 동기적인 흐름으로 작업하고 싶다면 어떻게할까요?

그럴때는 setState의 두번째 인자로 콜백을 쓰시면 됩니다.

```js
handleClick = () => {
    this.setState({
        counter: this.state.counter + 1
    }, () => {
        this.setState({
            counter: this.state.counter + 1
        })
    })
}

<button onClick={this.handleClick}>

// 버튼을 클릭하면 2증가
```

그런데 콜백이 들어가면 모양새가 좀 지저분하죠? 

위 코드를 setState안에 updater function을 집어넣은 형식으로 바꾸어봅시다

```js
handleClick = () => {
    this.setState((state) => {
        return {
            number: this.state.number + 1
        }
    })
    
    this.setState((state) => {
        return {
            number: this.state.number + 1
        }
    })
}

<button onClick={this.handleClick}>

// 버튼을 클릭하면 2증가
```

위 코드를 잘 기억하신다면 immer를 사용하실때에 큰 무리가 없을 것입니다.

