그동안 두차례 컴포넌트 구조화를 경험했습니다.

컴포넌트 수가 많지도 않음에도 불구하고 컴포넌트간에 부모를 거쳐서 props를 전달해주는 것이 마냥 쉽지만은 않았죠?

만약 컴포넌트가 엄청나게 많아진다면 어떤 컴포넌트에 어떤 state가 있어야하고 서로 어떤방식으로 props를 내려줘야 할지가 더 어려워질 것입니다.

만약 외부에 글로벌한 state 저장소가 있고 컴포넌트는 특정 state가 필요할때만 그 저장소에서 해당 값을 꺼내쓴다면 얼마나 편할까요?

그걸 구현한 것이 바로 redux입니다.

리액트에서 사용하는 redux에는 다음과 같은 개념들이 있어요.

1. 스토어
2. 액션, 액션생성함수
3. 디스패치
4. 리듀서

스토어는 말그대로 state를 관리하는 저장소입니다.

특정 컴포넌트는 특정 state가 필요할때 store에서 꺼내와서 사용하기만 하면 됩니다.

스토어안에는 상태값과 리듀서가 들어있는데요, 그렇다면 리듀서는 무엇일까요?

리듀서는 컴포넌트가 보내는 액션을 받아서 그에 알맞는 상태변화를 가져다 줍니다.

참고로 액션은 다음과 같이 생겼어요.

```jsx
const CLICK = "CLICK"
```

리듀서는 항상 type값을 보기때문에 보통 액션 생성함수라는 것을 만들어서 사용함으로써 매번 type에 action을 만드는 것을 대신해줍니다.

액션생성함수는 다음과 같이 생겼습니다.

```jsx
const click = () => ({type: CLICK})
```

따라서 click이라는 함수를 실행하면 다음과 같은 결과가 반환되는 것이죠

```jsx
// 클릭을 호출하면
click()

// 다음과 같이 반환됩니다
{type: "CLICK"}
```

스토어안의 리듀서는 액션을 받아서 타입을 확인하고 해당 타입에 해당되는 로직에 따라 스토어의 상태값을 변화 시켜줍니다.

그렇다면 컴포넌트는 특정액션을 어떻게 스토어에 보낼까요? 그건 디스패치라는 함수를 통해서 가능합니다.

```jsx
store.dispatch(click())
```

이렇게하면 스토어안에 있는 리듀서는`{type: "CLICK"}`이라는 값을 받게되는 것이죠.

아마 리덕스가 처음이시라 새로 쏟아지는 용어들 때문에 여러모로 헷갈리실텐데요, 아래 그림을 보면서 다시 이해해봅시다.

![](/assets/redux-process.png)

> **"**_리덕스 스토어에는 state와 reducer가 들어가고 state를 바꾸기 위해서는 reducer를 작동시켜야 했지"_
>
> _"reducer안에는 state를 어떤식으로 변화시킬지에 대한 여러가지 로직이 들어있는데, 그러면 reducer안의 특정 로직을 어떻게 작동시켰지?"_
>
> _"아 맞다 action을 dispatch해주면 reducer가 해당 action의 type을 찾아서 그에 알맞게 state를 변화시켜 주었지"_
>
> ...
>
> 등등 처럼 위 그림 각각의 구성요소가 무엇인지 먼저 이해해보세요.

자, 이제 개념을 잡으셨다면 제가 미리 준비한 템플릿을 clone해주세요.

```js
git clone https://github.com/react-nodejs-tutor/redux-template.git
```

리덕스를 사용하려면 우선 스토어를 하나 만들어야겠죠?

하지만 처음부터 index.js에 store를 만들지 않고 리듀서파일부터 작성을 해줍니다.

store를 만들때에는 리듀서를 함꼐 넣어줘야 오류가 나지 않기 때문입니다.

리덕스 공식문서에서는 액션, 액션생성함수와 리듀서를 각각 다른 파일에 작성하곤 하는데,

우리는 ducks패턴이라는 것을 사용할 것이기 때문에 액션, 액션생성함수, 그리고 리듀서를 한 파일\(모듈\)에 묶어서 작성을 하겠습니다.

yarn start를 통해 개발서버를 실행시키면 가장 위에 먼저 보이는 카운터부터 완성을 해보겠습니다.

```js
// src/store/modules/counter.js

// 액션
// 모듈이름을 앞에 붙여줌으로써 액션명 중복방지
const INCREMENT = 'counter/INCREMENT';
const DECREMENT = 'counter/DECREMENT';

// 액션생성함수
export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });

// 초기 상태값
const initialState = {
    number: 0
};

// 리듀서
export default function counter(state = initialState, action) {
    switch (action.type) {
        case INCREMENT:
            return {
                ...state,
                number: state.number + 1
            };

        case DECREMENT:
            return {
                ...state,
                number: state.number - 1
            };

        default:
            return state;
    }
}
```

리듀서를 작성할때는 ...state로 직접 불변성을 유지해줘야 한다는 것을 잊지마세요!

setState\(\) 함수에서는 불변성 관리를 자동으로 해주었기 때문에 우리가 직접 ... spread를 사용할 필요가 없었어요.

하지만 리듀서에서는 꼭! 해주어야 합니다. 그래야 리덕스 개발자도구를 통해서 되돌아가기 기능을 사용하면서 상태변화를 볼 수가 있어요.

위와 같이 counter모듈을 작성했다면 combineReducers를 통해서 묶어줍니다.

지금은 모듈이 한개지만 나중에는 여러개가 될 수 있으니까요.

따라서 index.js에서 스토어를 만들기전에, 여러 리듀서들을 한번에 합쳐주는 작업을 해줄게요.

```js
// src/store/modules/index.js

import { combineReducers } from 'redux';
import counter from './counter';

export default combineReducers({
    counter
});
```

이렇게 여러 모듈을 하나로 묶으면 스토어에 한번에 집어넣을 수 있게 됩니다.

스토어를 만들고 리듀서를 집어넣어 볼까요?

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore } from 'redux';
import rootReducer from './store/modules';

const store = createStore(rootReducer);
//상태값을 콘솔에 찍어볼 수도 있음
//console.log(store.getState());

ReactDOM.render(<App />, document.getElementById('root'));

serviceWorker.unregister();
```

이렇게 하면 스토어가 만들어집니다. 여기서 store.getState\(\)를 콘솔에 찍어보면 스토어안의 값을 확인할 수가 있어요.

여기까지는 아직 우리 리액트 프로젝트에 스토어를 적용한 것이 아닙니다.

또한 우리에게는 리덕스 개발자도구라는 것이 있기 때문에 더 쉽게 확인할 수가 있습니다.

우리 프로젝트에 리덕스를 적용해보고 개발자도구까지 달아볼까요?

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { createStore } from 'redux';
import rootReducer from './store/modules';
import { Provider } from 'react-redux';

const devTools =
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__();
const store = createStore(rootReducer, devTools);

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
);

serviceWorker.unregister();
```

이제는 우리 프로젝트에 연결된 리덕스 스토어에서 값을 빼와서 사용해보겠습니다.

여기서는 프레젠테이셔널 컴포넌트와 컨테이너 컴포넌트로 구분할 것인데요,

컨테이너 컴포넌트는 리덕스 스토어와 소통하는 smart한 컴포넌트고,

프레젠테이셔널 컴포넌트는 컨테이너 컴포넌트가 내려주는 값만을 사용하는 dumb한 컴포넌트입니다.

우선 리덕스안의 상태값을 가져올 수 있는 컨테이너 컴포넌트부터 만들어봅시다.

```js
// src/containers/CounterContainer.js

import React, { Component } from 'react';
import Counter from '../components/Counter';
import { increment, decrement } from '../store/modules/counter';
import { connect } from 'react-redux';

class CounterContainer extends Component {
    render() {
        const { number, increment, decrement } = this.props;
        return (
            <Counter
                number={number}
                onIncrement={increment}
                onDecrement={decrement}
            />
        );
    }
}

const mapStateToProps = state => ({
    number: state.counter.number
});

const mapDispatchToProps = dispatch => ({
    increment: () => dispatch(increment()),
    decrement: () => dispatch(decrement())
});

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(CounterContainer);
```

여기서 중요한건 connect함수입니다. react-redux 라이브러리가 제공해주는 connect함수는 스토어와 컨테이너 컴포넌트를 연결해줍니다.

스토어안에는 여러가지 값이 있을 수 있는데 그중에 필요한 값을 mapStateToProps를 통해서 받아올 수 있고,

스토어에 액션을 디스패치해서 스토어가 관리하는 상태값을 변화시킬 수 있는 함수를 mapDispatchToProps를 통해서 받아올 수 있습니다.

그리고 CounterContainer는 this.props를 통해서 그 값들을 받아오게 되는 것이죠.

마지막으로 Counter.js에서는 컨테이너 컴포넌트가 내려준 값들을 사용하기만 하면 됩니다.

```js
// src/components/Counter.js

import React, { Component } from 'react';
import './Counter.css';

class Counter extends Component {
    render() {
        const { number, onIncrement, onDecrement } = this.props;

        return (
            <div className="Counter">
                <h1>{number}</h1>
                <div className="btn-wrapper">
                    <button onClick={onIncrement}>+</button>
                    <button onClick={onDecrement}>-</button>
                </div>
            </div>
        );
    }
}

export default Counter;
```

자 이제 개발서버를 열고 카운터를 작동 시켜보세요.

App.js에서 프레젠테이셔널 컴포넌트를 렌더해주고 있으니 잘 작동할 수가 없겠죠?

컨테이너 컴포넌트를 렌더해주는 것을 절대 잊지마세요!

