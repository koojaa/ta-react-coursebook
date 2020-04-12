SPA\(Single Page Application\)가 단순히 페이지가 하나가 아니라는 점을 말씀드렸죠?

싱글페이지라고 부르는 이유는 유저가 처음 접속했을때 구체적인 data를 제외한 정적파일을 모두 불러오기 때문입니다.

이제 리액트 라우터로 페이지를 나누어 유저가 접속하는 url마다 다른 data를 보여줄 수 있게 해볼게요.

먼저 리액트 라우터를 설치해주세요. \(프로젝트를 새로 만들어주세요\)

```js
yarn add react-router
```

설치한 라우터를 적용하기 위해서 index.js에서 BrowserRouter를 이용해 App을 감싸줍니다.

```js
// index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);

serviceWorker.unregister();
```

이제는 각 라우터에 구현할 페이지를 구현해 볼게요. state나 life cycle을 사용하지않으므로 함수형 컴포넌트로 구현하겠습니다.

main컴포넌트와 sub컴포넌트를 만들고 각각 다른 url에 연결해줍시다.

```js
// src/Main.js
import React from 'react';

const Main = () => {
    return (
        <div>
            <h1>메인화면</h1>
        </div>
    );
};

export default Main;
```

```js
// src/Sub.js
import React from 'react';

const Sub = () => {
    return (
        <div>
            <h1>서브화면</h1>
        </div>
    );
};

export default Sub;
```

**본격적으로 라우트를 사용해볼텐데요, 사용자가 요청하는 주소에 따라 다른 컴포넌트를 보여주겠습니다.**

이 작업을 할때는 Route컴포넌트를 사용하고 알맞은 props를 넣어줍니다.

```js
<Route path="주소" component={보여주고싶은 컴포넌트}>
```

기존 App.js에 있었던 코드를 지워주고 다음과 같이 작성해줄게요.

```js
// src/App.js

import React from 'react';
import { Route } from 'react-router-dom';
import Main from './Main';
import Sub from './Sub';

const App = () => {
  return (
    <div>
      <Route path="/" component={Main} />
      <Route path="/sub" component={Sub} />
    </div>
  );
};

export default App;
```

여기서 /sub으로 들어가면 메인 컴포넌트 내용도 보이는 것을 확인할 수 있습니다.

이는 /sub경로가 /경로와 중복되기 때문입니다. 따라서 exact라는 props를 넣어줍니다.

```js
// src/App.js

import React from 'react';
import { Route } from 'react-router-dom';
import Main from './Main';
import Sub from './Sub';

const App = () => {
  return (
    <div>
      <Route exact path="/" component={Main} />
      <Route path="/sub" component={Sub} />
    </div>
  );
};

export default App;
```

이렇게 하면 경로가 완벽히 똑같을때만 컴포넌트를 보여주게 됩니다.

**이번에는 링크를 만들어서 클릭하면 다른 주소로 이동할 수 있게 해줄게요.**

이때는 Link컴포넌트를 사용합니다.

Link컴포넌트는 클릭시 다른 주소로 이동시키는 컴포넌트입니다.

리액트 라우터사용시 일반 a태그를 사용하시면 e.preventDefault\(\)를 해주어야하고 자바스크립트로 주소를 변환해주어야합니다.

그렇지않으면 새로고침을 하게되고 그러면 상태초기화, 컴포넌트 언마운트 후 리렌더링을 하게됩니다.

따라서 Link컴포넌트를 사용하며, 이는 HTML5 History API를 사용하기 때문에 브라우저의 주소만 바꿀뿐 페이지를 새로 불러오지는 않습니다.

```js
// App.js

import React, { Component } from 'react';
import Main from './Main';
import Sub from './Sub';
import { Route, Link } from 'react-router-dom';

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">메인화면으로</Link>
        </li>
        <li>
          <Link to="/sub">서브화면으로</Link>
        </li>
      </ul>
      <hr />
      <Route exact path="/" component={Main} />
      <Route path="/sub" component={Sub} />
    </div>
  );
};

export default App;
```

**이번엔 파라미터, 그리고 쿼리를 받을 수 있도록 해볼게요.**

파라미터, 쿼리를 언제 사용해야하느냐에 대한 정해진 규칙은 없지만 통상적으로 파라미터는 특정 id나 이름을 가지고 조회를 할 때,

쿼리는 어떤 키워드를 검색하거나 요청에 필요한 옵션을 전달할 때 사용합니다.

```js
// src/Profile.js

import React from 'react';

const profileData = {
  kim: {
    name: '김oo',
    description:'프로그래머'
  },
  lee: {
    name: '이oo',
    description: '가정주부'
  }
};

const Profile = ({ match }) => {
  // 파라미터일때는 match사용
  const { username } = match.params;
  const profile = profileData[username];
  if (!profile) {
    return <div>존재하지 않는 사람입니다.</div>;
  }
  return (
    <div>
      <h1>{profile.name}</h1>
      <p>{profile.description}</p>
    </div>
  );
};

export default Profile;
```

```js
// src/App.js

import React, { Component } from 'react';
import Main from './Main';
import Sub from './Sub';
import Profile from './Profile';
import { Route, Link } from 'react-router-dom';

class App extends Component {
    render() {
        return (
            <div>
                <ul>
                    <li>
                        <Link to="/">메인화면으로</Link>
                    </li>
                    <li>
                        <Link to="/sub">서브화면으로</Link>
                    </li>
                </ul>
                <Route path="/" exact component={Main} />
                <Route path="/sub" component={Sub} />
                <Route
                    path="/profiles/:username"
                    component={Profile}
                />
            </div>
        );
    }
}

export default App;
```

localhost:3000/profiles/kim

localhost:3000/profiles/lee

localhost:3000/profiles/asdf

각각 접속을 해보세요. 잘 구현이 되어있나요?

숙제는 react-router로 프로젝트2를 리팩토링 하는 걸 내려고했는데, 아쉽게도 라우터를 다 배우지 못해서 아직은 좀 어려워보입니다.

다음시간에는 리덕스를 나갈예정이니, 이전에 학습하신 내용들을 완벽하게 숙지해주세요

