지난 시간에 숙제로 내어 드렸던 코드를 함께 완성해봅시다.

우선 우리가 만들려고 하는 것에 어떤 액션들이 필요한지를 알아야 합니다.

필요한 액션을 정했다면 리듀서를 위한 모듈파일을 작성하는 것은 쉽기 때문입니다.

그러면 어떤 액션이 필요한가요?

* 컬러를 입력하기 위해 인풋에 변화를 일으키는 액션, 
* 컬러를 추가하는 액션, 
* 추가된 액션을 클릭했을때 불투명도가 바뀌는 액션, 
* 그리고 제거 하는 액션

이렇게 네 가지 액션이 필요하겠네요.

이제 한 번 만들어볼까요?

```js
// src/store/modules/colorList.js

import { createAction, handleActions } from 'redux-actions';

const CHANGE_INPUT = 'colorList/CHANGE_INPUT';
const INSERT = 'colorList/INSERT';
const UPDATE = 'colorList/UPDATE';
const REMOVE = 'colorList/REMOVE';

let id = 1;

export const changeInput = createAction(CHANGE_INPUT, text => text);
export const insert = createAction(INSERT, color => ({ color, id: id++ }));
export const update = createAction(UPDATE, id => id);
export const remove = createAction(REMOVE, id => id);

const initialState = {
    input: '',
    list: []
};


export default handleActions(
    {
        [CHANGE_INPUT]: (state, action) => ({
            ...state,
            input: action.payload
        }),
        [INSERT]: (state, action) => ({
            ...state,
            list: state.list.concat({
                id: action.payload.id,
                color: action.payload.color,
                opacity: 1
            })
        }),
        [UPDATE]: (state, action) => ({
            ...state,
            list: state.list.map(item => {
                if (item.id === action.payload) {
                    return {
                        ...item,
                        opacity: item.opacity - 0.1
                    };
                }
                return item;
            })
        }),
        [REMOVE]: (state, action) => ({
            ...state,
            list: state.list.filter(item => item.id !== action.payload)
        })
    },
    initialState
);
```

위 코드에서 주목해야 할 점은, 리듀서는 반드시 순수해야 한다는 것입니다.

리듀서에서 INSERT부분을 보면 id를 action.payload.id로 받아왔음을 확인할 수 있습니다.

id값을 1씩 더해주는 작업은 액션생성함수에서 대신 해주었죠.

위와 같은 간단한 작업들은 액션생성함수에서 대신 해줄 수 있지만, 소켓통신이나 api호출 등과 같은 비동기 작업은 그렇게 할 수 없습니다.

리덕스에서 비동기작업을 처리할 때는 리덕스 미들웨어를 사용해야 한다고 말씀드렸죠?

미들웨어는 뒤에서 이어서 알아볼게요.

이제 이어서 combineReducers를 통해 리듀서 모듈을 합쳐주겠습니다.

```js
// src/store/modules/index.js

import { combineReducers } from 'redux';
import counter from './counter';
import colorList from './colorList';

export default combineReducers({
    counter,
    colorList
});
```

여기까지 했으면 스토어 안에 colorList와 관련한 상태 값, 그리고 상태변화 함수인 리듀서가 반영이 되었습니다.

\(이미 index.js에서 리덕스 스토어 설정을 해주었으니까요\)

이제는 컨테이너 컴포넌트를 작성하면 되겠죠?

```js
// src/containers/ColorListContainer.js

import React, { Component } from 'react';
import { connect } from 'react-redux';
import ColorList from '../components/ColorList';
import * as colorListActions from '../store/modules/colorList';
import * as counterActions from '../store/modules/counter';
import { bindActionCreators } from 'redux';

class ColorListContainer extends Component {
    render() {
        const { input, list, ColorListActions, CounterActions } = this.props;

        return (
            <ColorList
                input={input}
                list={list}
                ColorListActions={ColorListActions}
                CounterActions={CounterActions}
            />
        );
    }
}

const mapStateToProps = ({ colorList: { input, list } }) => ({
    input,
    list
});

const mapDispatchToProps = dispatch => ({
    ColorListActions: bindActionCreators(colorListActions, dispatch),
    CounterActions: bindActionCreators(counterActions, dispatch)
});

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(ColorListContainer);
```

이번에는 bindActionsCreators를 불러와서 mapDispatchToProps를 작성했습니다.

import해야할 액션생성함수 개수가 너무 많을 경우 import \* as ~ 이런식으로 액션생성함수를 한번에 불러와서

mapDispatchToProps를 작성할 때 bindActionCreators를 통해 이들을 한번에 묶어주면 편하게 사용할 수 있습니다.

또한 새롭게 추가한 color값을 counter에도 반영해야 하므로 CounterActions도 불러왔음을 볼 수 있습니다.

컨테이너를 작성했으니 이제는 컴포넌트에서 그대로 받아서 보여주는 작업을 해볼까요?

```js
// src/components/ColorList.js

import React, { Component } from 'react';
import './ColorList.css';

class ColorList extends Component {
    handleChange = e => {
        const { value } = e.target;
        const { ColorListActions } = this.props;
        ColorListActions.changeInput(value);
    };

    handleSubmit = e => {
        e.preventDefault();
        const { ColorListActions, CounterActions, input } = this.props;
        ColorListActions.insert(input);
        CounterActions.changeColor(input);
        ColorListActions.changeInput('');
    };

    handleUpdate = id => {
        const { ColorListActions } = this.props;
        ColorListActions.update(id);
    };

    handleRemove = id => {
        const { ColorListActions } = this.props;
        ColorListActions.remove(id);
    };

    render() {
        const { input, list } = this.props;

        return (
            <div>
                <form className="ColorList" onSubmit={this.handleSubmit}>
                    <input
                        placeholder="원하는 색을 입력하세요"
                        value={input}
                        onChange={this.handleChange}
                    />
                </form>
                <ul>
                    {list.map(item => {
                        return (
                            <div
                                key={item.id}
                                style={{
                                    backgroundColor: item.color,
                                    opacity: item.opacity,
                                    width: '50px',
                                    height: '50px',
                                    float: 'left'
                                }}
                                onClick={() => this.handleUpdate(item.id)}
                                onContextMenu={e => {
                                    e.preventDefault();
                                    this.handleRemove(item.id);
                                }}
                            />
                        );
                    })}
                </ul>
            </div>
        );
    }
}

export default ColorList;
```

사실 여기까지만 해도 잘 작동합니다.

insert한 색을 클릭하면 불투명도가 증가하고 우클릭을하면 사라지게 하죠.

하지만 우리는 .map처리를 통해 배열을 렌더했으므로 최적화에 대한 의심을 해주어야 합니다.

강의 초반에 todolist를 만들어볼 때 shouldComponentUpdate를 사용했던 것 기억나시나요?

마찬가지입니다. shouldComponentUpdate는 lifeCycle메소드이므로 div대신 따로 컴포넌트를 만들어줍시다.

```js
// src/components/ListItem.js

import React, { Component } from 'react';
class ListItem extends Component {
    shouldComponentUpdate(nextProps, nextState) {
        return this.props.item !== nextProps.item;
    }

    render() {
        const { style, onUpdate, onRemove, item } = this.props;
        return (
            <div
                style={style}
                onClick={() => onUpdate(item.id)}
                onContextMenu={e => {
                    e.preventDefault();
                    onRemove(item.id);
                }}
            />
        );
    }
}
export default ListItem;
```

당연히 ColorList 컴포넌트에서 props를 알맞게 내려 주어야겠죠?

```js
// src/components/ColorList.js

import React, { Component } from 'react';
import './ColorList.css';
import ListItem from './ListItem';

class ColorList extends Component {
    handleChange = e => {
        const { value } = e.target;
        const { ColorListActions } = this.props;
        ColorListActions.changeInput(value);
    };

    handleSubmit = e => {
        e.preventDefault();
        const { ColorListActions, CounterActions, input } = this.props;
        ColorListActions.insert(input);
        CounterActions.changeColor(input);
        ColorListActions.changeInput('');
    };

    handleUpdate = id => {
        const { ColorListActions } = this.props;
        ColorListActions.update(id);
    };

    handleRemove = id => {
        const { ColorListActions } = this.props;
        ColorListActions.remove(id);
    };

    render() {
        const { input, list } = this.props;

        return (
            <div>
                <form className="ColorList" onSubmit={this.handleSubmit}>
                    <input
                        placeholder="원하는 색을 입력하세요"
                        value={input}
                        onChange={this.handleChange}
                    />
                </form>
                <ul>
                    {list.map(item => {
                        return (
                            <ListItem
                                key={item.id}
                                item={item}
                                style={{
                                    backgroundColor: item.color,
                                    opacity: item.opacity,
                                    width: '50px',
                                    height: '50px',
                                    float: 'left'
                                }}
                                onUpdate={this.handleUpdate}
                                onRemove={this.handleRemove}
                            />
                        );
                    })}
                </ul>
            </div>
        );
    }
}

export default ColorList;
```

이와같이 map처리되는 컴포넌트들은 shouldComponentUpdate와 같은 최적화는 기본으로 해주시는 것이 좋습니다!

