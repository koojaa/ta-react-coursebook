이어서 색상도 관리해볼게요.

ducks로 작성한 counter 모듈 파일을 열어서 색상관리를 위한 액션, 액션생성함수, 리듀서 내용을 추가해주겠습니다.

_여기서부터는 가급적이면 먼저 해보시고 막힐 경우에 코드를 봐주세요. _

```js
// src/store/modules/counter.js

// action
const INCREMENT = 'counter/INCREMENT';
const DECREMENT = 'counter/DECREMENT';
const CHANGE_COLOR = 'counter/CHANGE_COLOR';

// action creator
export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });
export const changeColor = color => ({ type: CHANGE_COLOR, color });

const initialState = {
    number: 0,
    color: '#bfcd7e'
};

// counter reducer
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
                color: action.color
            };

        default:
            return state;
    }
}
```

우리는 이미 combineReducers로 리듀서를 합친 후 스토어에 등록했고, 해당 스토어를 리액트 프로젝트에 연결시켜 놓은 상황입니다.

따라서 가져오기만 하면 됩니다. 마찬가지로 컨테이너 컴포넌트를 만들고 connect로 스토어와 연결해서 가져오면 되겠죠?

```js
// src/containers/ColorSquareContainer.js

import React, { Component } from 'react';
import { connect } from 'react-redux';
import { changeColor } from '../store/modules/counter';
import ColorSquare from '../components/ColorSquare';

class ColorSquareContainer extends Component {
    render() {
        const { number, color, changeColor } = this.props;

        return (
            <ColorSquare selected={color} number={number} onSelect={changeColor} />
        );
    }
}

const mapStateToProps = state => ({
    color: state.counter.color,
    number: state.counter.number
});

const mapDispatchToProps = dispatch => ({
    changeColor: color => dispatch(changeColor(color))
});

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(ColorSquareContainer);
```

그리고 프레젠테이셔널 컴포넌트에서 그대로 받아와서 사용해주세요.

```js
// src/components/ColorSquare.js

import React, { Component } from 'react';
import './ColorSquare.css';

const colors = ['#bfcd7e', '#7E57C2', '#EA80FC', '#00BCD4'];

class Color extends Component {
    render() {
        const { color, active, onSelect } = this.props;

        const style = {
            backgroundColor: color
        };

        return (
            <div
                className={`Color ${active ? 'active' : ''}`}
                style={style}
                onClick={() => onSelect(color)}
            />
        );
    }
}

class ColorSquare extends Component {
    render() {
        const { number, selected, onSelect } = this.props;

        const style = {
            width: 200 + 10 * number,
            height: 200 + 10 * number
        };

        return (
            <div className="ColorSquare" style={style}>
                {colors.map(color => {
                    return (
                        <Color
                            key={color}
                            color={color}
                            active={selected}
                            onSelect={onSelect}
                        />
                    );
                })}
            </div>
        );
    }
}

export default ColorSquare;
```

이제 각 색상의 정사각형을 클릭하면 리덕스 스토어에 정상적으로 반영이 되나요?

더 명확하게 확인하고 싶으시면 ColorSquare.css에서 .Color의 opacity를 0.5로 줄여보세요.

색상이 리덕스 스토어에 잘 반영되고 있다면 이제는 해당 색상을 카운터에도 적용을 해줄게요.

```js
// src/containers/CounterContainer.js

import React, { Component } from 'react';
import Counter from '../components/Counter';
import { increment, decrement } from '../store/modules/counter';
import { connect } from 'react-redux';

class CounterContainer extends Component {
    render() {
        const { number, increment, decrement, color } = this.props;
        return (
            <Counter
                number={number}
                onIncrement={increment}
                onDecrement={decrement}
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
    increment: () => dispatch(increment()),
    decrement: () => dispatch(decrement())
});

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(CounterContainer);
```

마찬가지로 프레젠테이셔널 컴포넌트에서 props로 받아와서 렌더해줍니다.

```js
import React, { Component } from 'react';
import './Counter.css';

class Counter extends Component {
    render() {
        const { number, onIncrement, onDecrement, color } = this.props;

        const style = {
            color
        };

        return (
            <div className="Counter">
                <h1 style={style}>{number}</h1>
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



