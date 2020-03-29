```js
// 실습 skeleton

git clone https://github.com/react-nodejs-tutor/cp-structurized-layout.git

yarn

yarn start
```

지금까지 배운 jsx문법과 state를 활용해서 정말 간단한 코드작성을 해보았는데, 단일 App컴포넌트에 모든 로직을 몰아서 작성했다는 것이 아쉽습니다. 수업 첫 시간에 컴포넌트는 '재사용 가능한' UI라고 말씀드렸죠? 따라서 기능별로 컴포넌트를 분리해서 적재적소에 사용할줄 알아야 합니다. 이번에는 다음과 같이 컴포넌트를 분리해 볼게요.

컴포넌트 구조는 다음과 같습니다.

![](/assets/structure.png)

폼을 입력하게 해주는 Form컴포넌트, 입력한 항목들을 나열해주는 List, 그리고 개별 항목의 내용이 담긴 Item 컴포넌트가 있습니다.단일 App컴포넌트에 몰아서 작성할 때는 App컴포넌트 자기자신의 mutation을 관리하기 위한 state만 사용했다면 이제는 컴포넌트들끼리 소통을 해야하기 때문에 props를 사용해야 하고, 어떤 props가 어디서 어디로 내려가야 하는지에 고민해야 합니다.

input state는 당연히 Form에서 관리하면 됩니다. 그러면 List컴포넌트는 Form에서 입력한 내용들을 보여주는 컴포넌트이므로 텍스트를 담는 배열\(todos\) state는 List에 있어야 할까요?

우선 컴포넌트 간의 관계를 따져볼까요? Form컴포넌트는 \(영향을 주기만 하지\) 다른 컴포넌트에게서 영향을 받지 않습니다. 따라서 input이라는 state는 Form컴포넌트에 있으면 됩니다. 하지만 todos배열은 Form 컴포넌트로부터 입력된 내용을 보여주는 것이지요? 즉 영향을 받습니다. Form으로부터 입력받은 내용을 파라미터로 전달받아서 todos를 별도의 state로 관리해줘야 합니다.

그렇다면 바로 List에서 Form에서 전달해준 input을 받으면 되는가하면 또 그건 아닙니다. App\(최상단 부모컴포넌트\)를 제외한 다른 컴포넌트들끼리 props를 교환하면 나중에 컴포넌트 갯수가 많아졌을때 구조가 복잡해집니다. 따라서 컴포넌트간에 소통을 할 때는 부모를 거쳐서 내려받도록 합니다. \(물론 나중에 리덕스를 배우면 이러한 수고로움도 덜 수 있습니다\)

각 이벤트 핸들러 구현은 첫 시간에 구현한 내용과 별 다를 것이 없기 때문에, 각 이벤트 핸들러의 내용이 잘 이해가 되시지 않는다면 1주차 강의자료를 참고해주세요.

> **이번 컴포넌트 구조화에서 새롭게 추가된 내용은,  handleToggle 부분인데요, **
>
> **여기서는 ...이라는 스프레드 연산자가 사용이 되었습니다.**
>
> **리액트에서는 불변성 관리를 해주어야 하기때문에 객체 그자체를 변경하면 안되고 반드시 복사를 해줘야한다고 말씀드렸죠?**
>
> **객체 불변성 관리의 경우에는 스프레드 연산자를 사용하면 편합니다.**

스프레드 연산자 사용법은 다음과 같습니다.

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

// 이렇게 하면 기존 데이터를 수정하지 않으면서 새 값을 지니고있는 새 데이터를 만들 수 있습니다.
// data와 nextData는 서로 다른 주소값을 들고있어요. handleToggle도 스프레드 연산자를 이용하면 쉽게 구현할 수 있습니다.
```

_**복습을 하실 때는, "왜 컴포넌트를 이런식으로 구조화 했는가"를 고민해보세요.**_

_**어떤 컴포넌트에서 어떤 내용을 state로 관리하고, 어떤 내용을 props로 내려주는지, 그 관계에 주목을 하면서 복습하시기를 추천드립니다.**_

우선 Form에서 input을 관리하기 위해 input state를 만들고 관리해봅시다.

```js
// src/components/Form.js

import React, { Component } from 'react';
import './Form.css';

class Form extends Component {
    state = {
        input: ''
    };

    handleChange = e => {
        const { value } = e.target;

        this.setState({
            input: value
        });
    };

    render() {
        const { input } = this.state;
        return (
            <div className="Form">
                <form className="form_container">
                    <input
                        value={input}
                        onChange={this.handleChange}
                    />
                    <button>추가</button>
                </form>
            </div>
        );
    }
}

export default Form;
```

개발자도구를 열고 확인해보세요. 잘 반영이 되었으면, 그다음으로 넘어갑시다.

이번에는 버튼을 눌렀을때 배열에 추가되도록 해야겠지요? 위에서 말씀드린 것처럼 App에 todos라는 빈 배열을 만들고, todos에 값을 추가하는 핸들러 함수를 만들어 주겠습니다. 또한 Form에서 버튼을 클릭했을때 값이 들어가야 하므로, 핸들러함수를 Form컴포넌트로 내려줘봅시다.

```js
// src/App.js

import React, { Component } from 'react';
import './App.css';

import Form from './components/Form';
import List from './components/List';

class App extends Component {
    id = 1;

    state = {
        todos: []
    };

    handleInsert = text => {
        const { todos } = this.state;
        this.setState({
            todos: todos.concat({
                id: this.id++,
                text,
                done: false
            })
        });
    };

    render() {
        return (
            <div className="App">
                <h3>TODO LIST</h3>
                <Form onInsert={this.handleInsert} />
                <List />
            </div>
        );
    }
}

export default App;
```

```js
// src/components/Form.js

import React, { Component } from 'react';
import './Form.css';

class Form extends Component {
    state = {
        input: ''
    };

    handleChange = e => {
        const { value } = e.target;

        this.setState({
            input: value
        });
    };

    handleSubmit = e => {
        e.preventDefault();
        this.props.onInsert(this.state.input);
        this.setState({
            input: ''
        });
    };

    render() {
        const { input } = this.state;
        return (
            <div className="Form">
                <form className="form_container" onSubmit={this.handleSubmit}>
                    <input
                        value={input}
                        onChange={this.handleChange}
                    />
                    <button type="submit">추가</button>
                </form>
            </div>
        );
    }
}

export default Form;
```

개발자도구를 열고 확인해보세요. 배열에 값이 잘 추가되고 있나요?

배열에 값이 추가되었으면 그걸 보여주는 컴포넌트는 List입니다. 따라서 List에는 todos를 내려줍니다.

```js
// src/App.js

import React, { Component } from 'react';
import './App.css';

import Form from './components/Form';
import List from './components/List';

class App extends Component {
    id = 1;

    state = {
        todos: []
    };

    handleInsert = text => {
        const { todos } = this.state;
        this.setState({
            todos: todos.concat({
                id: this.id++,
                text,
                done: false
            })
        });
    };

    render() {
        return (
            <div className="App">
                <h3>TODO LIST</h3>
                <Form onInsert={this.handleInsert} />
                <List todos={this.state.todos}/>
            </div>
        );
    }
}

export default App;
```

실제로 내용을 보여주는 컴포넌트는 Item이므로 List에서는 개별 아이템 하나하나를 Item에게 다시 내려줘야겠죠?

```js
// src/components/List.js

import React, { Component } from 'react';
import './List.css';
import Item from './Item';

class List extends Component {
    render() {
        const { todos } = this.props;

        return (
            <div className="List">
                {todos.map(todo => {
                    return (
                        <Item
                            todo={todo}
                        />
                    );
                })}
            </div>
        );
    }
}

export default List;
```

```js
// src/components/Item.js

import React, { Component } from 'react';
import './Item.css';

class Item extends Component {
    render() {
        const { todo } = this.props;
        return (
            <div className='Item'>
                <div className="check">&#10004;</div>
                <div className="remove">[지우기]</div>
                <div className="text">{todo.text}</div>
            </div>
        );
    }
}

export default Item;
```

이제 확인해보세요. 입력하고 버튼을 누르면 값이 잘 반영되고 있나요?

이제 같은 작업을 반복하면 됩니다. 배열 각 원소의 done값을 반전시키는 핸들러함수, 배열 각 원소를 제거하는 핸들러함수를 App에서 만들어주고, 필요한 컴포넌트로 내려주면 끝입니다. 아래 코드를 살펴보고 이해가 되지않는다면 위로 올라가서 컴포넌트 간의 관계를 따져보세요.

새로 나오는 내용은 es6 template literal과 e.stopPropagation\(\)입니다. 템플릿 리터럴은 문자열안에서 자바스크립트 값을 쓸 수 있도록 해주는 문법이고, e.stopPropagation\(\)은 이벤트가 부모 엘리먼트로 전파되는 것을 막아주는 메서드입니다.

```js
// src/App.js

import React, { Component } from 'react';
import './App.css';

import Form from './components/Form';
import List from './components/List';

class App extends Component {
    id = 1;

    state = {
        todos: []
    };

    handleInsert = text => {
        const { todos } = this.state;
        this.setState({
            todos: todos.concat({
                id: this.id++,
                text,
                done: false
            })
        });
    };


    handleToggle = id => {
        const { todos } = this.state;
        this.setState({
            todos: todos.map(todo => {
                if (todo.id === id) {
                    return {
                        ...todo,
                        done: !todo.done
                    };
                }
                return todo;
            })
        });
    };

    handleRemove = id => {
        const { todos } = this.state;
        this.setState({
            todos: todos.filter(todo => todo.id !== id)
        });
    };

    render() {
        return (
            <div className="App">
                <h3>TODO LIST</h3>
                <Form onInsert={this.handleInsert} />
                <List
                    todos={this.state.todos}
                    onToggle={this.handleToggle}
                    onRemove={this.handleRemove}
                />
            </div>
        );
    }
}

export default App;
```

```js
// src/components/List.js

import React, { Component } from 'react';
import './List.css';
import Item from './Item';

class List extends Component {
    render() {
        const { todos, onToggle, onRemove } = this.props;

        return (
            <div className="List">
                {todos.map(todo => {
                    return (
                        <Item
                            todo={todo}
                            onToggle={onToggle}
                            onRemove={onRemove}
                        />
                    );
                })}
            </div>
        );
    }
}

export default List;
```

```js
// src/components/Item.js

import React, { Component } from 'react';
import './Item.css';

class Item extends Component {
    render() {
        const { todo, onToggle, onRemove } = this.props;
        return (
            <div
                className={`Item ${todo.done && 'active'}`}
                onClick={() => onToggle(todo.id)}
            >
                <div className="check">&#10004;</div>
                <div
                    className="remove"
                    onClick={e => {
                        e.stopPropagation();
                        onRemove(todo.id);
                    }}
                >
                    [지우기]
                </div>
                <div className="text">{todo.text}</div>
            </div>
        );
    }
}

export default Item;
```

여기까지 하면 모두 잘 작동하는 것을 확인하실 수 있습니다. 이제 마지막으로 최적화 작업을 해보겠습니다.

리액트에서는 부모컴포넌트가 리렌더링되면 자식컴포넌트도 리렌더링 된다고 말씀드렸죠?

만약 자식컴포넌트의 내용은 바뀐 것이 전혀 없는데 부모가 리렌더링 되었다는 이유만으로 자식컴포넌트도 리렌더링 된다면 정말 비효율적일 것입니다.

현재 App컴포넌트가 todos 관리하고 자식들이 그 내용을 보여주고 있습니다.

따라서 todos 배열의 여러 요소들중 한 요소의 done만 바뀌었으면 그 요소만 리렌더링 해주면 됩니다.

바로 이런 경우에 shouldComponentUpdate를 통해 최적화해 줄 수 있습니다.

```js
// src/components/Item.js

import React, { Component } from 'react';
import './Item.css';

class Item extends Component {
    shouldComponentUpdate(nextProps, nextState) {
        if (this.props.todo !== nextProps.todo) {
            return true;
        } else {
            return false;
        }
    }

    render() {
        const { todo, onToggle, onRemove } = this.props;
        return (
            <div
                className={`Item ${todo.done && 'active'}`}
                onClick={() => onToggle(todo.id)}
            >
                <div className="check">&#10004;</div>
                <div
                    className="remove"
                    onClick={e => {
                        e.stopPropagation();
                        onRemove(todo.id);
                    }}
                >
                    [지우기]
                </div>
                <div className="text">{todo.text}</div>
            </div>
        );
    }
}

export default Item;
```

성능을 보다 정확하게 해보고 싶으시면 개발자도구를 열어 performance탭의 record기능 혹은 리액트 개발자도구의 profiler를 통해 비교해보세요.

간단하게 렌더가 되었는지만 확인을 하고 싶으시면, Item컴포넌트에 로깅을 찍어봄으로써 해당 컴포넌트가 리렌더되고있는지 아닌지 알 수 있습니다.

