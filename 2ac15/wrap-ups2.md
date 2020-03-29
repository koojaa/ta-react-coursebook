지난 시간에는 배열에 값을 추가하고 보여주는 작업까지 했는데, 단순히 버튼을 만들기만 하고 onClick을 연결하면 유저가 '엔터를 눌렀을때'라는 이벤트를 따로 만들어줘야 해서 불편합니다. 따라서 연관된 input과 button을 form으로 묶어주고 form에 onSubmit이라는 이벤트를 달아봅시다.

```js
<form onSubmit={this.handleInsert}>
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
    <button type="submit">추가</button>
</form>
```

```js
handleInsert = e => {
    e.preventDefault();
    const { username, password, list } = this.state;
    this.setState({
        username: '',
        password: '',
        list: list.concat({ id: this.id, username, password })
    });
    this.id += 1;
};
```

form에는 원래 내용을 담아서 서버로 POST http request를 날리기 때문에, 페이지가 새로고침 됩니다. 따라서 form을 사용한다면 handleInsert에서는 e.preventDefault를 해줘야합니다. form안의 onSubmit이벤트가 기본적으로 가지고있는 새로고침 현상을 막아줌으로써 state가 초기화되는 것을 막아줍니다.

이제 폼을 작성하고 엔터를 눌러보세요. 개발자도구를 열어보면 배열에 성공적으로 값이 담긴것을 확인하실 수 있을거에요. 다만 폼을 작성하고 password input에 포커스가 남아있습니다. username input으로 포커스를 옮겨주려면 reference라는 것을 사용해야 합니다. ref를 사용하는 방식은 크게 두가지가 있는데요, 우선 첫번째 방식은 다음과 같습니다.

```js
import React, { Component } from 'react';

class App extends Component {
  state = {
    username: '',
    password: '',
    list: []
  };
  id = 1;
  usernameInputRef = null;
  
  ...

  handleInsert = e => {
    e.preventDefault();
    const { username, password, list } = this.state;
    this.setState({
      username: '',
      password: '',
      list: list.concat({ id: this.id, username, password })
    });
    this.id += 1;

    this.usernameInputRef.focus();
  };

  render() {
    const { username, password, list } = this.state;
    return (
      <div>
        <form onSubmit={this.handleInsert}>
          <input
            name="username"
            value={username}
            onChange={this.handleChange}
            ref={ref => {
              this.usernameInputRef = ref;
            }}

        ...
      </div>
    );
  }
}

export default App;
```

리액트는 버추얼돔을 사용하고 state를 통해 변화값을 관리하기 때문에, 사실 ref를 사용해서 직접 dom에 접근하는 것을 권장하지는 않습니다.다만 input에 focus를 주거나, 스크롤 위치를 가져오는 것등과 같은 특수한 케이스에는 ref를 사용해서 dom을 조작하면 됩니다.

위 코드처럼 컴포넌트가 렌더된 이후에 username input을 usernameInputRef에 넣어주고, handleInsert를 할 때 해당 dom element에 focus를 주는 방식으로 코드를 작성하면 됩니다. 

두번째는 리액트에서 제공하는 createRef를 사용하는 방식입니다.주목할 점은 단순히 focus가 아닌 current.focus를 사용해야 한다는 것입니다.

```js
usernameInputRef = React.createRef();
```

```js
<input
    name="username"
    placeholder="유저명"
    value={username}
    onChange={this.handleChange}
    ref={this.usernameInputRef}
/>
```

```js
handleInsert = e => {
    e.preventDefault();
    const { username, password, list } = this.state;
    this.setState({
        username: '',
        password: '',
        list: list.concat({ id: this.id, username, password})
    });
    this.id += 1;``
    
    this.usernameInputRef.current.focus();
};
```

이어서 삭제작업을 진행해볼까요? 배열에 insert를 할 때는 배열내장함수에서 push가 아닌 concat을 사용했는데, 이는 불변성을 지키기 위함이라고 말씀드렸습니다. 삭제 작업도 마찬가지입니다. 이 때는 filter를 사용하면 됩니다. handleRemove함수를 한번 작성해볼까요?

```js
handleRemove = id => {
    const {list} = this.state;
    this.setState({
        list: list.filter(item => item.id !== id)
    })
};
```

filter도 map함수와 마찬가지로 콜백함수를 인자로 받는데요, 모든 원소를 한번씩 돌리면서 콜백함수의 몸체부분에서 true를 반환하는 원소들만 걸러줍니다. handleRemove의 경우에는 인자로 받은 id를 가지고있는 원소를 제외한 모든 원소들이 걸러지겠지요?

이제 handleRemove 이벤트핸들러를 버튼에 연결해보겠습니다.

```js
<button onClick={() => this.handleRemove(item.id)}>
    삭제하기
</button>
```

여기를 보면 this.handleRemove\(item.id\)가 아닌 \(\) =&gt; this.handleRemove\(item.id\)로 표현했습니다.jsx에서는 이벤트핸들러를 등록할때 함수이름만 넣고 호출하지 않는다고 했죠? 카운터 예제에서 onClick={this.handleIncrease} 이렇게 했지 onClick={this.handleIncrease\(\)}이렇게 하지 않았습니다.

그 이유는 무엇이었나요? this.handleIncrease\(\)이렇게 호출해버리면 컴포넌트가 렌더됨과 동시에 해당 이벤트핸들러가 호출되고 바로 state로 관리하는 number값이 0에서 1로 증가하기 때문에 즉시 해당 컴포넌트가 리렌더되고, 계속해서 그 작업이 반복되기 때문에 무한루프에 빠진다고 했습니다.

handleRemove의 경우도 마찬가지 입니다. this.handleRemove에 item.id라는 인자를 넣긴 넣어서 전달해주어야 하는데, this.handleRemove\(item.id\)이렇게 해버리면 즉시 호출되어 버리겠죠? 따라서 this.handleRemove\(item.id\)를 한번 더 감싸주는 것입니다. 

\(\) =&gt; this.handleRemove\(item.id\) 이렇게요. 이렇게하면 버튼이 클릭되었을때 \(\(\) =&gt; this.handleRemove\(item.id\)\)\(\)가 되면서 this.handleRemove함수에 id가 잘 담겨서 호출됩니다.

