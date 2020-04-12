앞서 말씀드렸듯이 setState안에는 updater function을 집어넣어서 사용할 수 있습니다.

불변성관리를 쉽게할 수 있도록 도와주는 immer는 이러한 updater function형식을 알고 있으면 아주 쉽게 사용할 수 있습니다.

우선 immer를 사용하기 위해서는 immer를 설치해야합니다. 그리고 사용할때는 \(통상적으로\) produce라는 이름으로 불러와 사용합니다.

```js
// immer 설치

yarn add immer

// 불러오기

import produce from 'immer'
```

immer는 우리가 관리하려고 하는 state의 객체구조가 nesting되면서 복잡해질 경우 불변성 유지를 쉽게 하기 위해 사용한다고 했죠?

객체구조가 복잡할 때 직접 하나하나 불변성 유지를 하면 힘들다는 것은 어떤 의미였나요?

만약 어떤 객체가 있는데, 그 객체안에는 또 다른 객체가있고 그 객체안의 어느 한 값만 바꾸고 싶다고 가정해볼게요.

```js
state = {
  something: {
    inside: {
      here: {
        there: {
          changeMe: 0,
          keepMe: 1
        },
        where: 'unknown'
      },
      hello: 'world'
    },
    outside: 'what?'
  }
}
```

위 객체안에서 changeMe부분만 바꾸고 싶다면 어떻게 해야 할까요?

```js
this.setState({
  something: {
    ...this.state.something,
    here: {
      ...this.state.something.here,
      there: {
        ...this.state.something.here.there,
        changeMe: 30
      }
    }
  }
})
```

맞나요? 저도 모르겠습니다. 맞을 수도 있고 틀릴 수도 있습니다.

제가 전달하고 싶은 메세지는 객체구조가 복잡해지면 불변성관리가 그만큼 복잡하므로 불변성관리 라이브러리를 쓰면 아주 편하다는 것입니다.

immer를 사용하면 다음과 같이 바꿀 수 있습니다.

```js
this.setState(produce(draft => {
    draft.something.here.there.changeMe = 30;
}))
```

정말.. 편하죠?

물론 실제로는 id나 특정한 item을 파라미터로 받아서 그에 해당하는 내용을 찾아서 해당값을 바꾸겠지만, 그 역시 마찬가지입니다.

그냥 그 조건에 맞는 것을 찾아서 바꿔주기만 하면되니까요.

immer를 사용할 때는 setState안에 updater function을 넣어서 사용할때처럼 사용하되, produce로 해당 updater function을 한번 감싸주기만 하면 됩니다.

다만 차이점은 더이상 불변성관리를 안해줘도 된다는 것이죠.

오늘 진행한 코드를 볼까요?

```js
import React, { Component } from 'react';
import produce from 'immer';
import './styles.css';

class App extends Component {
  id = 2;
  state = {
    input: '',
    todos: [
      {
        id: 1,
        text: '제목1',
        done: true
      },
      {
        id: 2,
        text: '제목2',
        done: true
      }
    ]
  };

  handleChange = e => {
    this.setState({
      input: e.target.value
    });
  };

  handleInsert = () => {
    this.setState(
      produce(draft => {
        draft.todos.push({
          id: ++this.id,
          text: this.state.input,
          done: false
        });
        draft.input = '';
      })
    );
  };

  handleToggle = id => {
    this.setState(
      produce(draft => {
        const todo = draft.todos.find(todo => todo.id === id);
        todo.done = !todo.done;
      })
    );
  };

  handleRemove = id => {
    this.setState(
      produce(draft => {
        const index = draft.todos.findIndex(todo => todo.id === id);
        draft.todos.splice(index, 1);
      })
    );
  };

  render() {
    return (
      <div>
        <div>
          <input value={this.state.input} onChange={this.handleChange} />
          <button onClick={this.handleInsert}>추가</button>
        </div>
        <ul>
          {this.state.todos.map(todo => (
            <li
              key={todo.id}
              style={{
                textDecoration: todo.done ? 'line-through' : 'none'
              }}
              onClick={() => this.handleToggle(todo.id)}
              onContextMenu={e => {
                e.preventDefault();
                this.handleRemove(todo.id);
              }}
            >
              {todo.text}
            </li>
          ))}
        </ul>
      </div>
    );
  }
}

export default App;
```

setState안에 updater function을 집어넣으면 다음과 같은 형태를 가지죠?

```js
this.setState(state => {})
```

이걸 produce로 감싸기만 하면 됩니다.

```js
this.setState(produce(state => {}))
```

produce를 사용하면 기존의 state를 사용하는 것이아니라 '불변성을 유지해주지 않아도 되는 state'를 사용하게 됩니다.

따라서 state가 아닌 draft로 표현해서 사용합니다.

```js
this.setState(produce(draft => {}))
```

이렇게요. draft는 더이상 불변성을 유지해주지 않아도 되기 때문에, 바꾸고 싶은 값을 찾아서 그냥 대입해주기만 하면 됩니다.

```js
handleToggle = id => {
    this.setState(
        produce(draft => {
            const todo = draft.todos.find(todo => todo.id === id);
            todo.done = !todo.done;
        })
    );
};

// immer를 사용하지 않았다면?
handleToggle = id => {
    this.setState({
        todos: this.state.todos.map(todo => {
            if(todo.id === id){
                return {...todo, done: !todo.done}
            } else return todo
        })
    })
}

// 보다 간결하게 쓰면? 하지만 여전히 ...을 써야해서 nesting된 객체라면 불편합니다.
handleToggle = id => {
    this.setState({
        todos: this.state.todos.map(todo => todo.id === id ? {...todo, done: !todo.done} : todo)
    })
}
```

비교가 한 눈에 들어오시나요?

참고로 setState를 사용하면 shallow copy, 즉 스프레드 연산자를 사용한 얕은 복사를 자동으로 해주기때문에 지금까지 우리가 setState를 사용하면서 복사를 직접 해줄 필요가 없었습니다.

나중에 배우실 리덕스의 리듀서함수나 hooks에서는 복사를 자동으로 해주지 않기 때문에 직접 ...을 통해 복사를 해주어야 합니다.

이런 상황에서 객체가 nesting된 구조라면 불변성을 유지하기가 더욱 어렵겠죠?

바로 그 때 immer를 사용하시면 편합니다.

