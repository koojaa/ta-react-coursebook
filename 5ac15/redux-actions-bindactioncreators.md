package.json을 확인해보면 redux-actions라는게 있습니다.

redux-actions를 사용하면 리듀서 모듈파일을 좀 더 쉽게 작성할 수 있습니다.

```js
import {createAction} from 'redux-actions'
```

redux-actions의 createAction을 사용하면 액션생성함수를 다음과 같이 생성할 수 있습니다.

```js
// action creators

export const increment = createAction(INCREMENT);
export const decrement = createAction(DECREMENT);
export const changeColor = createAction(CHANGE_COLOR);
```

이렇게하면 모든 액션의 diff값을\(number, color등등\) payload라는 이름으로 통일해서 받아올 수 있게 됩니다.

이 때 payload안에 무엇이 들었는지 명시해주면 나중에 코드를 볼 때 더 수월하겠죠?

따라서 changeColor는 다음과 같이 작성해 보겠습니다.

```js
export const changeColor = createAction(CHANGE_COLOR, color => color);
```

이렇게 하면 리듀서에서는 action.color가 아닌 action.payload로 받아올 수 있게됩니다.

```js
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

        case CHANGE_COLOR:
            return {
                ...state,
                color: action.payload
            };

        default:
            return state;
    }
}
```

action.number인지, action.color인지 매번 고민할 필요가 없으니 리듀서를 작성할 때도 더 편하겠죠?

```js
import {createAction, handleActions} from 'redux-actions'
```

이번에는 handleActions 차례인데요, 이걸 사용하면 리듀서를 switch~case문을 사용하지 않고 작성할 수 있습니다.

```js
export default handleActions(
    {
        [INCREMENT]: (state, action) => ({
            ...state,
            number: state.number + 1
        }),
        [DECREMENT]: (state, action) => ({
            ...state,
            number: state.number - 1
        }),
        [CHANGE_COLOR]: (state, action) => ({
            ...state,
            color: action.payload
        })
    },
    initialState
);
```

위처럼 switch~case대신 handleActions를 사용하면 깔끔할뿐더러 각 액션case에서 const키워드로 동일한 변수명을 사용할 수도 있습니다.

아무래도 관련있는 액션들끼리 비슷한 변수 네이밍을 사용할 확률이 높겠죠?

이어서 container 컴포넌트를 작성할때 mapDispatchToProps를 좀 더 쉽게 작성해볼게요.

```js
// CounterContainer.js에서의 mapDispatchToProps
// 아래 두개는 같습니다.

const mapDispatchToProps = dispatch => ({
    increment: () => dispatch(increment()),
    decrement: () => dispatch(decrement())
});

const mapDispatchToProps = {
    increment,
    decrement
}
```

이 둘이 같은 이유는 connect가 bindActionCreators를 해주기 때문인데요,

참고로 bindActionsCreators를 하면 액션생성함수를 한번에 묶을 수 있습니다.

```js
// src/containers/CounterContainer.js

import React, { Component } from 'react';
import Counter from '../components/Counter';
// counterActions라는 이름으로 counter모듈에서 export해준 액션생성함수들을 한번에 받아옵니다.
import * as counterActions from '../store/modules/counter';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';

class CounterContainer extends Component {
    render() {
        const { number, CounterActions, color } = this.props;
        return (
            <Counter
                number={number}
                onIncrement={CounterActions.increment}
                onDecrement={CounterActions.decrement}
                color={color}
            />
        );
    }
}

const mapStateToProps = state => ({
    number: state.counter.number,
    color: state.counter.color
});

const mapDispatchToProps = dispatch => ({
    CounterActions: bindActionCreators(counterActions, dispatch)
});

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(CounterContainer);
```

따라서.. 다음 세 개는 같습니다.

```js
// 사용할때는 CounterActions.increment, CounterActions.decrement
const mapDispatchToProps = dispatch => ({
    CounterActions: bindActionCreators(counterActions, dispatch)
});

// 아래 두개는 그냥 평소처럼 똑같이 사용
// 1.
const mapDispatchToProps = dispatch => ({
    increment: () => dispatch(increment()),
    decrement: () => dispatch(decrement())
});

// 2.
const mapDispatchToProps = {
    increment,
    decrement
}
```

이번주에 진행하지 못한 부분, ColorList를 위한 모듈 파일을 작성하고 컨테이너 컴포넌트, 프레젠테이셔널 컴포넌트와 연결해보기를 꼭 해보세요.

코드를 작성하시면 저나 조교님에게 보내주시면 코드 리뷰를 해드리겠습니다.

