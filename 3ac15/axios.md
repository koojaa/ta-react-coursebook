api요청은 fake data를 무료로 제공해주는 jsonplaceholder를 이용했습니다.

[https://jsonplaceholder.typicode.com/](https://jsonplaceholder.typicode.com/)

axios 라이브러리 관련 더 자세한 정보는 공식문서에서 확인하실 수 있습니다.

[https://github.com/axios/axios](https://github.com/axios/axios)

axios를 사용하기 위해 우선 설치합니다.

```jsx
yarn add axios
```

**아래** **코드를 보시면서...**

1.

axios는 promise기반으로 작동한다고 말씀드렸죠? 따라서 .then으로 응답값을 받아올 수 있습니다.

즉 axios가 요청을 한 후 성공적으로 값을 받아오면 resolve에 넣어서 해당 값을 반환해 주는 것이죠.

또한 promise기반이기 때문에 async-await으로 작성을 해도 됩니다. \(async-await은 다음시간에 배워보도록 할게요\)

2.

await axios.get으로 요청을 해서 값을 response에 담았습니다.

그런데 this.setState안에서 response.data를 넣어주고 있죠?

api요청을 해서 반환받은 값은 항상 열어서 확인을 해줘야합니다.

요청, 응답과 관련한 헤더들도 들어있기 때문에 정말로 우리가 원하는 데이터는 어디에 있나 확인을 해봐야해요.

3.

jsx부분에서 바로 data.map을 해주지않고 data가 있을때~ 라는 조건을 달아주었죠?

map은 배열내장함수이기 때문에 api를 불러오기전인 null에 쓰면 안됩니다. 그러면 어플리케이션이 crash날 확률이 높습니다.

따라서 조건 처리를 반드시 해주어야 합니다.

```jsx
// src/App.js

import React, { Component } from 'react';
import axios from 'axios';

class App extends Component {
    state = {
        data: null
    };

    handleClick = () => {
        axios.get('https://jsonplaceholder.typicode.com/posts')
            .then(response => {
                this.setState({
                    data: response.data
                });
            })
    };

    render() {
        const { data } = this.state;
        return (
            <div>
                <button onClick={this.handleClick}>
                    api불러오기
                </button>
                <ul>
                    {data &&
                        data.map(item => {
                            return <li key={item.id}>{item.title}</li>;
                        })}
                </ul>
            </div>
        );
    }
}

export default App;
```

지금까지 내운 배용들을 잘 복습해주세요.

