리듀서는 순수해야 한다고 했습니다.

따라서 같은 인풋에 따라 다른 아웃풋이 나올 가능성이 있는 네트워크 요청과 같은 순수하지 못한 작업은 리덕스 미들웨어를 사용합니다.

유저가 어떤 컴포넌트에서 특정 액션을 날렸을 때\(스토어에 디스패치 했을 때\),

해당 액션이 순수하지 않은 작업을 요구하는 것이라면, 리덕스 미들웨어가 그 액션을 대신 처리해줍니다.

다음 그림을 볼까요?![](/assets/middlewares.png)위 그림처럼 어떤 액션이 날라왔는데, 미들웨어가 "해당 액션이 순수하지 않은 작업을 요청하는 것 같다"라고 판단하면,

미들웨어가 리듀서 대신 그 작업을 처리해주고 next\(\)를 호출하게 됩니다.

그러면 리듀서는 순수한 작업만 하게 되는 것입니다.

그렇다면 "해당 액션이 순수한 작업을 요청하는 것인지, 그렇지 않은지" 판단할 기준이 미들웨어에게 필요하겠죠?

이건 미들웨어마다 판단하는 방식이 다릅니다.

리덕스 미들웨어는 종류가 상당히 많은데요,

우리는 그 중에서, 현재까지는 가장 보편적으로 쓰이는 **redux-thunk**를 학습해보겠습니다.

redux thunk는 액션생성함수가 반환하는 액션이 함수일 경우 이를 자신이 대신 처리해줘야 하는 액션으로 판단합니다.

이는 redux thunk 라이브러리 코드를 살펴보시면 쉽게 확인하실 수 있습니다.

```
git clone https://github.com/react-nodejs-tutor/rx-middleware.git

yarn 

(yarn add redux-thunk)
```

우선 실습을 위해 위 깃 주소를 클론해주세요.

여기서는 간단하게 버튼을 누르면 1초뒤에 insert가 되는 비동기 insert를 만들어 보면서 thunk에 익숙해져 보겠습니다.

먼저 thunk middleware를 스토어에 적용해주세요.

미들웨어를 적용할 때는 applyMiddleware를 사용합니다.

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore, applyMiddleware } from 'redux';
import rootReducer from './store/modules';
import { Provider } from 'react-redux';
import middleware from './middleware';
import ReduxThunk from 'redux-thunk';

const store = createStore(rootReducer, applyMiddleware(middleware, ReduxThunk));

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
);

serviceWorker.unregister();
```

이번에는 모듈에  insertAsync thunk함수를 추가로 만들어주세요.

```js
// src/store/modules/form.js

import { createAction, handleActions } from 'redux-actions';

const CHANGE_INPUT = 'form/CHANGE_INPUT';
const INSERT = 'form/INSERT';

let id = 1;
export const changeInput = createAction(CHANGE_INPUT, text => text);
export const insert = createAction(INSERT, text => ({ text, id: id++ }));

// 원하는 비동기 작업을 thunk 함수안에서 실행 (여기서는 setTimeout이 해당)
export const insertAsync = input => dispatch => {
    setTimeout(() => {
        dispatch(insert(input));
    }, 1000);
};

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
                text: action.payload.text
            })
        })
    },
    initialState
);
```

그리고 App컴포넌트에서 기존 코드를 bindActionsCreators를 사용해서 기존코드를 바꾸고 insertAsync를 사용해보세요.

```js
// src/App.js

import React, { Component } from 'react';
import { connect } from 'react-redux';
import * as formActions from './store/modules/form';
import { bindActionCreators } from 'redux';

class App extends Component {

    handleChange = e => {
        const { value } = e.target;
        this.props.FormActions.changeInput(value);
    };

    handleSubmit = e => {
        e.preventDefault();

        const { FormActions, input } = this.props;

        FormActions.insertAsync(input);
        FormActions.changeInput('');
    };

    render() {
        const { input, list } = this.props;

        return (
            <div>
                <form action="">
                    <input value={input} onChange={this.handleChange} />
                    <button onClick={this.handleSubmit}>입력</button>
                </form>
                {list.map(item => {
                    return <div key={item.id}>{item.text}</div>;
                })}
            </div>
        );
    }
}

export default connect(
    ({ form: { input, list } }) => ({
        input,
        list
    }),
    dispatch => ({
        FormActions: bindActionCreators(formActions, dispatch)
    })
)(App);
```

1초뒤에 입력값이 잘 추가되나요?

이어서 리덕스 미들웨어를 사용해 axios call을 해보고 로그인, 로그아웃을 구현해 보겠습니다.

