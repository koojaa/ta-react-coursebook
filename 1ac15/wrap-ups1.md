지금까지 배운 내용들이 react문법의 절반이라고 해도 과언이 아닙니다.

리액트 문법 자체는 쉽다고 말씀드렸죠? 문제는 "어떻게 활용하는가" 입니다.

지금까지 배운 내용들로 간단한 코드를 작성해 보겠습니다.

### input 관리

```js
import React, { Component } from 'react';

class App extends Component {
  state = {
    input: ''
  };
  handleChange = e => {
    this.setState({
      input: e.target.value
    });
  };
  render() {
    return (
      <div>
        <input value={this.state.input} onChange={this.handleChange} />
        <p>현재 input 값: {this.state.input}</p>
      </div>
    );
  }
}

export default App;
```

handleChange함수를 주목해 주세요.

```js
handleChange = e => {
    this.setState({
        input: e.target.value
    });
};
```

handleChange함수는 e값을 파라미터로 받아왔는데요,

e.target을 콘솔로 찍어보면 onChange이벤트가 발생한 input 엘리먼트를 받아올 수 있습니다.

따라서 e.target의 value값을 우리가 관리해주고 있는 input값에 넣어줌으로써 새롭게 상태관리를 해줄 수 있습니다.

#### 여러개의 input관리

```js
import React, { Component } from 'react';

class App extends Component {
  state = {
    username: '',
    password: ''
  };
  handleChange = e => {
    const { value, name } = e.target;
    this.setState({
      [name]: value
    });
  };
  render() {
    // username과 password를 비구조화 할당해서 꺼내옴
    const { username, password } = this.state;
    return (
      <div>
        <input
          name="username"
          value={username}
          onChange={this.handleChange}
        />
        <input
          name="password"
          value={password}
          onChange={this.handleChange}
        />
        <p>
          {username} 의 패스워드는 {password}입니다.
        </p>
      </div>
    );
  }
}

export default App;
```

인풋이 여러개일땐 어떻게 해줘야 할까요?

인풋이 여러개면 handleChange함수 입장에서 어떤 인풋에 변화가 일어난 것인지 모르기 때문에 각 input에 name값을 집어넣어 줘야 합니다.

그렇게 하면 handleChange를 다음과 같이 간단하게 작성할 수 있습니다.

```js
handleChange = e => {
    const { value, name } = e.target;
    this.setState({
        [name]: value
    });
};
```

위 코드를 보면

```js
this.setState({
    [name]: value
});
```

이렇게 name값을 대괄호로 감싸주었는데요, 이는 es6의 computed property names라는 문법입니다.

이렇게 name변수 안에 들어있는 값을 유동적으로 받아오기 위해서는 반드시 대괄호로 감싸주어야 한답니다.

대괄호로 감싸지 않고 다음과 같이 작성하면,

```js
this.setState({
    name: value
})
```

말그대로 name을 key로 가지는 객체를 의미하게 됩니다.

#### 배열에값을 추가하고 보여주기

```js
import React, { Component } from 'react';

class App extends Component {
  id = 1;
  state = {
    username: '',
    password: '',
    list: []
  };
  handleChange = e => {
    const { value, name } = e.target;
    this.setState({
      [name]: value
    });
  };
  handleInsert = () => {
    const { username, password, list } = this.state;
    this.setState({
      username: '',
      password: '',
      list: list.concat({ id: this.id, username, password })
    });
    this.id += 1; // 새 데이터 만들때마다 +1
  };
  render() {
    const { username, password, list } = this.state;
    return (
      <div>
        <input
          name="username"
          value={username}
          onChange={this.handleChange}
        />
        <input
          name="password"
          value={password}
          onChange={this.handleChange}
        />
        <button onClick={this.handleInsert}>추가하기</button>
        <p>
          {username} 의 패스워드는 {password}입니다.
        </p>
        <ul>
          {list.map((item) => (
            <li key={item.id}>
              {item.username}({item.password})
            </li>
          ))}
        </ul>
      </div>
    );
  }
}

export default App;
```

이번에는 button을 만들고 추가를 누르면 빈 배열에 값이 추가되고, 추가된 값들을 보여주는 코드를 작성해봤습니다.

배열에 있는 값을 보여줄때는 자바스크립트 내장함수인 map함수를 사용한다고 말씀드렸죠?

여기서 중요한 점은 map을 통해 배열안에 있는 값들을 보여줄때는 key값을 반드시 달아줘야 한다고 했습니다.

단순히 배열 요소값의 index를 key값을 사용하는 것은 warning메세지를 피하기위한 임시방편에 불과하기 때문에,

우리는 id를 만들어서 고유한 값을 달아주도록 처리했습니다.

오늘은 여기까지만 수업을 했고 filter, 그리고 spread연산자를 이용한 삭제구현이나 업데이트는 하지 않았습니다.

이는 다음시간에 이어서 다뤄볼 예정입니다.

다음시간에는 코드량이 두세배 이상으로 증가하므로, 복습을 꼭 많이 해주세요!

