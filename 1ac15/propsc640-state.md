리액트에서 다루는 데이터는 크게 props와 state 두가지가 있습니다.

props는 부모 컴포넌트가 자식 컴포넌트에게 내려주는 값이라면, state는 컴포넌트 자기자신이 선언하는 값입니다.

그럼 props부터 살펴볼까요?

## Props

#### Props의 기본

props는 부모가 자식에게 내려주는 속성입니다.

부모컴포넌트에서 자식컴포넌트에게 props를 집어넣으면 자식컴포넌트는 this.props로 해당값을 사용할 수 있습니다.

```js
// App.js (부모 컴포넌트)

import React, { Component } from 'react';
import Name from './Name';

class App extends Component {
  render() {
    return (
      <Name name="홍길동" />
    );
  }
}

export default App;
```

```js
// Name.js(자식컴포넌트)

import React, { Component } from 'react';

class Name extends Component {
  render() {
    return (
      <div>
        hello <b>{this.props.name}!</b>
      </div>
    );
  }
}

export default Name;
```

정말 쉽죠? props는 이게 거의 전부입니다.

리액트 문법은 props와 같이 정말 단순합니다. 다만 언제, 어떤 방식으로 사용할지 결정하는 것이 어려워요.

이런 활용법은 6주 수업내내 함께 코드를 작성하면서 익숙해질테니 너무 걱정하지 마세요!

#### children props

```js
//App.js

import React, { Component } from 'react';
import Wrapper from './Wrapper';
import Name from './Name';

class App extends Component {
  render() {
    return (
      <Wrapper>
        <Name />
      </Wrapper>
    );
  }
}

export default App;
```

이렇게하면 위에서 작성한 Name컴포넌트가 Wrapper컴포넌트의 children props로 들어갑니다.

children props는 부모에서 내려주지않아도 컴포넌트 자체적으로 가지고있는 props에요.

따라서 모든 클래스 컴포넌트에서는 this.props.children을 사용할 수가 있습니다.

```js
// Wrapper.js

import React, {Component} from 'react';

class Wrapper extends Component {

  const {children} = this.props;
  render(){
    return (
      <div>{children}</div>
    )
  }
}

export default Wrapper;
```

children은 const {children} = props와 같은 비구조화 할당을 해서 작성해 봤습니다.

아래 코드는 위코드와 동일합니다.

```js
// Wrapper.js

import React, {Component} from 'react';

class Wrapper extends Component {

  render(){
    return (
      <div>{this.props.children}</div>
    )
  }
}

export default Wrapper;
```

_비구조화할당 예시_

```js
const object = {a:10, b:20, c:30}

const {a,b,c} = object;

console.log(b);
// 20
```

# State

#### State의 기본

props처럼 부모 컴포넌트가 내려주는 것 말고, 컴포넌트 자신이 어떤 값을 관리하고싶다면 그때는 state를 사용합니다.

리액트는 제이쿼리와 달리 dom을 직접 가져와서 조작하는 것이 아니기 때문에,

유동적으로 변하는 값\(mutation\)은 모두 state로 관리해 주신다고 생각하시면 됩니다.

```js
import React, { Component } from 'react';

class Counter extends Component {
  state = {
    number: 0
  }

  handleIncrease = () => {
    this.setState({
      number: this.state.number + 1
    });
  }

  render() {
    return (
      <div>
        <h1>counter example</h1>
        <div>number : {this.state.number}</div>
        <br />
        <button onClick={this.handleIncrease}>+</button>
      </div>
    );
  }
}

export default Counter;
```

props와 마찬가지로 state도 this키워드를 통해서 접근합니다.

state를 변화시킬때는 직접 새로운 값을 대입해 주는 것이 아니라 반드시 setState 함수를 사용한다고 했었죠?

이유는 불변성 관리를 해줘야 하기 때문입니다. \(그냥 대입을 해주면 불변성 관리가 되질 않아요!\)

나중에 리액트에서 shouldComponentUpdate를 통해 최적화를 하거나 componentDidUpdate등의 LifeCycle API를 사용할때 이전값과 현재값을 비교해야 하는 작업이 있는데, 불변성을 지키지 않으면 리액트가 그 둘을 비교할 방법이 없기 때문입니다.

오늘은 setState, 그리고 배열내장함수인 concat, map 등을 배워보면서 간단하게 불변성이란 무엇인지에 대해 설명을 드렸는데요,

다음주에는 구조화된 todolist실습을 하면서 더욱 자세하게 알아볼게요.

```js
  render() {
    return (
      <div>
        <h1>counter example</h1>
        <div>number : {this.state.number}</div>
        <br />
        <button onClick={this.handleIncrease}>+</button>
      </div>
    );
  }
```

자, 이 부분을 한 번 다시 볼까요?

onClick이벤트를 onclick이아니라 camelCase로 작성했습니다. 자바스크립트와 차이가 나는 부분입니다.

또한 this.handleIncrease를 this.handleIncrease\(\)이런식으로 호출해주지않고 함수명만 넣어주었죠?

만약 this.handleIncrease\(\)로 바꿔버리면 number 상태값이 계속해서 바뀌므로 무한루프에 빠지게됩니다.

따라서 반드시 함수명만 넣어주셔야 합니다.

```js
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      number: 0
    }
  }
  ...

}
```

마지막, 원래 state는 이런식으로 constructor안에 작성해야 했습니다.\(리액트 초창기 버전\)

하지만 이제는 class field문법을 통해 constructor없이도 바로 state나 handler함수를 작성함으로써 더욱 편하게 사용할 수 있습니다.

공식문서나 medium같은 블로그에는 아직도 constructor를 이용해 state와 각종 함수를 작성하는 부분이 있기 때문에 참고만 하시면 될 것 같습니다.

