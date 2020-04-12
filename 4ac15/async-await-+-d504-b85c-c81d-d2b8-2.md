**새로운 프로젝트를 시작하기 전에,**

지난시간에는 promise를 배웠는데요,

async-await를 이용하면 보다 가독성이 높은 코드를 작성하실 수 있습니다.

async-await는 promise와 별개의 것이 아니라 promise기반 위에서 작동하는 것이라고 말씀드렸습니다.

async-await는 다음과 같은 형식으로 이루어집니다.

```js
async function asyncFunc(){
    await 첫번째 promise
    await 두번째 promise
    await 세번째 promise
    await 네번째 promise
    ...
}
```

await 키워드를 사용하기 위해서는 async 키워드가 function 앞에 붙어야합니다.

또한 await뒤에는 항상 promise 인스턴스가 와야해요.

그렇게하면 해당 promise가 resolve값을 반환하기 전까지는 await가 그다음 진행을 막아줍니다.

다시 한번 볼까요?

```js
// 비동기작업

setTimeout(() => {
    console.log(1)
}, 1000)
console.log(2)

// 결과값: 2 1
```

```js
// promise를 이용해서 비동기를 동기적으로 바꾸면?

function promiseFunc(){
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log(1);
            resolve();
        }, 1000)
    })
}

promiseFunc().then(() => {
    console.log(2);
})

// 결과값: 1 2
```

```js
// async-await를 이용하면?

async function asyncFunc(){
    await promiseFunc();
    console.log(2);
}

// 결과값: 1 2
```

어떤가요? 가독성이 아주 좋죠?

async-await를 쓸때는 try~catch로 감싸면 try에서 비동기작업을 하다 에러가 날경우 catch로 잡아줄 수가 있습니다.

```js
async function asyncFunction(){
    try {
        // 네트워크 작업, 소켓요청 등 여러가지 비동기작업을 하다가 에러가 날경우 catch의 e로 에러인자가 넘어감
    } catch(e) {
        console.error(e)
    }
}
```

promise를 작성했으면 그걸 async-await로 바꾸는 연습을 해보세요!

```js
// 지난 시간 jsonplaceholder에 promise로 요청한 것을 async-await로 바꾸면?

import React, { Component } from 'react';
import axios from 'axios';

class App extends Component {
    state = {
        data: null
    };

    handleClick = async () => {
        const response = await axios.get(
            'https://jsonplaceholder.typicode.com/posts'
        );

        this.setState({
            data: response.data
        });
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

자 이제 새로운 프로젝트를 진행해볼까요?

혹시라도 컴포넌트 구조화에 대한 복습을 제대로 못하셨다면 꼭 뒤로 돌아가서 복습을 해주세요!

```js
// skeleton code를 git clone해주세요

git clone https://github.com/react-nodejs-tutor/football-api-prac-demo.git

// package.json에 있는 라이브러리들을 설치해주세요

yarn add

yarn start
```

우선 이번 프로젝트를 진행하기 위해서는 apifootball 사이트에 가입하고 apiKey를 발급받으셔야 합니다. 이건 수업시간에 진행했죠?

```js
https://apifootball.com에 들어가서 register하고 apiKey 발급받기
```

clone한 폴더 구조를 한번 살펴보세요.

이전에 진행한 프로젝트에 비해서 많이 복잡해보이죠?

하지만 그렇지 않습니다. 파일이 보이지 않게 폴더를 접고 폴더명만 살펴보세요.

관련있는 파일들끼리 폴더로 구조화해놓았기 때문에 이렇게 하면 눈에 쉽게 들어오실 것입니다.

이번 프로젝트 구조는 다음과 같습니다.

![](/assets/app.png)

우선 calendar와 league는 신경쓰지말고 유저가 url에 처음 접속했을때 영국 프리미어리그 2020년 1월 1일 ~ 2020년 2월 1일 경기목록 및 스코어를 보여주는 작업을 해볼까요?

api요청할때는 발급받은 개인 API KEY를 넣어주세요.

```js
// MatchList.js

import React, { Component } from 'react';
import axios from 'axios';

class MatchList extends Component {
    state = {
        data: null
    };

    getData = async () => {
        try {
            const response = await axios.get(
                'https://apiv2.apifootball.com/?action=get_events&from=2020-01-01&to=2020-02-01&league_id=148&APIkey=<본인API_KEY>'
            );

            console.log(response.data);
            this.setState({
                data: response.data
            });
        } catch (e) {
            console.error(e);
        }
    };

    componentDidMount() {
        this.getData();
    }

    render() {
        return <div></div>;
    }
}

export default MatchList;
```

여기까지 하고 console을 한번 보세요.

cdm에서 데이터를 잘가져오고 있나요?

그러면 값을 확인하고 어떤값을 key로 써야할지도 파악하셨겠죠?

이번엔 불러온 데이터들을 보여줍시다.

```js
// MatchList.js

import React, { Component } from 'react';
import axios from 'axios';

class MatchList extends Component {
    state = {
        data: null
    };

    getData = async () => {
        try {
            const response = await axios.get(
                'https://apiv2.apifootball.com/?action=get_events&from=2020-01-01&to=2020-02-01&league_id=148&APIkey=<본인API_KEY>'
            );

            console.log(response.data);
            this.setState({
                data: response.data
            });
        } catch (e) {
            console.error(e);
        }
    };

    componentDidMount() {
        this.getData();
    }

    render() {
        const { data } = this.state;
        return <div>{data && data.map(d => <Match key={d.match_id} data={d} />)}</div>;
    }
}

export default MatchList;
```

인터넷이 느린 사용자는 처음에 들어왔을때 수 ms동안 아무내용도 표시되지 않기때문에 페이지를 이탈할 가능성이 있습니다.

따라서 오류가 난 것이 아니라고 말해주기 위해 '데이터를 불러오는 중이다'라는 메세지를 띄워줍시다.

```js
// MatchList.js

import React, { Component } from 'react';
import axios from 'axios';

class MatchList extends Component {
    state = {
        loading: false
        data: null
    };

    getData = async () => {
        try {
            this.setState({
                loading: true
            })

            const response = await axios.get(
                'https://apiv2.apifootball.com/?action=get_events&from=2020-01-01&to=2020-02-01&league_id=148&APIkey=<본인API_KEY>'
            );

            console.log(response.data);
            this.setState({
                data: response.data
            });
        } catch (e) {
            console.error(e);
        }

        this.setState({
            loading: false
        })
    };

    componentDidMount() {
        this.getData();
    }

    render() {
        const { loading, data } = this.state;
        return (
            <div>
                {loading && <h3 style={{ textAlign: 'center' }}>데이터를 불러오는중입니다...</h3>}
                {!loading && data && data.map(d => <Match key={d.match_id} data={d} />)}
            </div>
        );
    }
}

export default MatchList;
```

이제 유저가 날짜를 선택하면 해당 날짜 범위에 있는 경기 리스트 및 스코어를 불러오게 하겠습니다.

제가 작성한 스켈레톤 코드에서는 이미 calendar컴포넌트가 자체적으로 state를 관리하고 있습니다.

그렇다면 날짜가 바뀌면 바뀐대로 새로운 주소가 api call을 날려줘야겠죠?

따라서 프로젝트1에서 했던 것처럼 최상단 app에서 해당 날짜를 별도의 state로 받아서 다시 MatchList로 내려주도록 하겠습니다.

```js
// App.js

import React, { Component } from 'react';
import MatchTemplate from './MatchTemplate/MatchTemplate';
import MatchFinder from './MatchFinder';
import Match from './Match';
import dateFormatter from '../utils/dateFormatter';

class App extends Component {
    state = {
        range: {
            startDate: '2020-01-01',
            endDate: '2020-02-01'
        }
    };

    handleRange = range => {
        const startDate = dateFormatter(range[0]);
        const endDate = dateFormatter(range[1]);

        this.setState({
            range: {
                startDate,
                endDate
            }
        });
    };

    render() {
        return (
            <div>
                <MatchTemplate header={<MatchFinder setRange={this.handleRange}/>}>
                    <Match />
                </MatchTemplate>
            </div>
        );
    }
}

export default App;
```

여기서 dateFormatter는 왜 사용했을까요?

calendar에서 관리되고 있는 state를 보면 배열로 관리되고 있고 날짜도 yyyy-mm-dd형식이 아닙니다.

따라서 api문서에서 원하는 형식으로 데이터를 파싱해줘야 합니다.

데이터 파싱은 제가 미리 준비한 dateFormatter를 통해서 해주세요.

MatchFinder에서는 다시 캘린더로 props를 내려주세요.

```js
import React, { Component } from 'react';
import Calendar from './Calendar';
import League from './League';

class MatchFinder extends Component {
    render() {
        const { setRange } = this.props;

        return (
            <div>
                <League />
                <Calendar setRange={setRange} />
            </div>
        );
    }
}

export default MatchFinder;
```

이렇게 했으면 Calendar.js에서는 button에 onClick이벤트를 등록하고 handleSubmit 이벤트핸들러를 작성해줍니다.

```js
// Calendar.js

import React, { Component } from 'react';
import ReactCalendar from 'react-calendar';
import './Calendar.scss';

class Calendar extends Component {
    state = {
        date: null
    };

    handleChange = date => {
        this.setState({
            date
        });
    };

    handleSubmit = e => {
        e.preventDefault();

        this.props.setRange(this.state.date);
    };

    render() {
        return (
            <div className="calendar">
                <ReactCalendar
                    className="react-calendar"
                    onChange={this.handleChange}
                    selectRange={true}
                />
                <button className="calender-button" onClick={this.handleSubmit}>
                    검색
                </button>
            </div>
        );
    }
}

export default Calendar;
```

리액트 개발자도구를 열고 확인해보세요.

달력에서 날짜를 선택하고 버튼을 누르면 App에 올바른 state값이 잘 담기나요?

이제 App에서 MatchList에 변화된 range값을 내려줄게요.

```js
// App.js

import React, { Component } from 'react';
import MatchTemplate from './MatchTemplate/MatchTemplate';
import MatchFinder from './MatchFinder';
import Match from './Match';
import dateFormatter from '../utils/dateFormatter';

class App extends Component {
    state = {
        range: {
            startDate: '2020-01-01',
            endDate: '2020-02-01'
        }
    };

    handleRange = range => {
        const startDate = dateFormatter(range[0]);
        const endDate = dateFormatter(range[1]);

        this.setState({
            range: {
                startDate,
                endDate
            }
        });
    };

    render() {
        const {range} = this.state;

        return (
            <div>
                <MatchTemplate header={<MatchFinder setRange={this.handleRange}/>}>
                    <Match range={range}/>
                </MatchTemplate>
            </div>
        );
    }
}

export default App;
```

그렇게하면 MatchList에서는 변한 range값에 따른 api요청을 다시 해줘야겠죠?

아까는 cdm으로 관리했다면 이번에는 cdu를 사용해볼게요.

```js
// MatchList.js

import React, { Component } from 'react';
import Match from './Match';
import axios from 'axios';

class MatchList extends Component {
    state = {
        loading: false,
        data: null
    };

    getData = async () => {
        this.setState({
            loading: true
        });

        try {
            // 동적으로 쿼리생성
            const { startDate, endDate } = this.props.range;
            const extraQuery = `&from=${startDate}&to=${endDate}`;

            // 템플릿 리터럴 사용
            const response = await axios.get(
                `https://apiv2.apifootball.com/?action=get_events${extraQuery}&league_id=148&APIkey=<본인API_KEY>`
            );

            console.log(response.data);
            this.setState({
                data: response.data
            });
        } catch (e) {
            console.error(e);
        }

        this.setState({
            loading: false
        });
    };

    componentDidMount() {
        this.getData();
    }

    // cdu등록
    componentDidUpdate(prevProps, prevState) {
        if (this.props.range !== prevProps.range) {
            this.getData();
        }
    }

    render() {
        const { loading, data } = this.state;
        // !data.error 조건 추가
        return (
            <div>
                {loading && <h3 style={{ textAlign: 'center' }}>데이터를 불러오는중입니다...</h3>}
                {!loading && data && !data.error &&
                    data.map(d => <Match key={d.match_id} data={d} />)}
            </div>
        );
    }
}

export default MatchList;
```

여기까지하고 이번엔 league종류에따라서 api call을 다시 하게 해봅시다.

마찬가지로 무슨 작업을 할 지, state를 어디서 관리하고 props를 어디에서 어디로 내려줄지 생각해보세요.

초반에 이런 작업이 어려우시다면 코드를 작성하기전에 먼저 종이에 코딩해보세요.

우리가 할 작업은 두가지입니다.

바뀐 리그종류에 따라 api call을 다시하기, 그리고 클릭한 메뉴의 스타일을 바꿔주는 것. 이 두가지 입니다.

```js
// App.js

import React, { Component } from 'react';
import MatchTemplate from './MatchTemplate/MatchTemplate';
import MatchFinder from './MatchFinder';
import Match from './Match';
import dateFormatter from '../utils/dateFormatter';

class App extends Component {
    state = {
        //추가
        leagueId: 148,
        range: {
            startDate: '2020-01-01',
            endDate: '2020-02-01'
        }
    };

    handleRange = range => {
        const startDate = dateFormatter(range[0]);
        const endDate = dateFormatter(range[1]);

        this.setState({
            range: {
                startDate,
                endDate
            }
        });
    };

    // 추가
    handleLeagueId = leagueId => {
        this.setState({
            leagueId
        });
    };

    render() {
        const { leagueId, range } = this.state;

        return (
            <div>
                // leagueId는 둘다한테 내려줌
                <MatchTemplate
                    header={
                        <MatchFinder
                            setRange={this.handleRange}
                            setLeagueId={this.handleLeagueId}
                            leagueId={leagueId}
                        />
                    }
                >
                    <Match range={range} leagueId={leagueId} />
                </MatchTemplate>
            </div>
        );
    }
}

export default App;
```

이제 Matchfinder에서 리그로 props를 내려주세요.

```js
// MatchFinder.js

import React, { Component } from 'react';
import Calendar from './Calendar';
import League from './League';

class MatchFinder extends Component {
    render() {
        const { setRange, setLeagueId, leagueId } = this.props;

        return (
            <div>
                <League setLeagueId={setLeagueId} leagueId={leagueId} />
                <Calendar setRange={setRange} />
            </div>
        );
    }
}

export default MatchFinder;
```

```js
// LeagueList.js
class LeagueList extends Component {
    render() {
        const { setLeagueId, leagueId } = this.props;
        return (
            <div>
                {
                    <ul className="leagueList-wrapper">
                        {leagueTypes.map(league => {
                            return (
                                <li key={league.league_id} className="leagueList">
                                    <LeagueItem
                                        league_name={league.league_name}
                                        league_id={league.league_id}
                                        setLeagueId={setLeagueId}
                                        selected={leagueId === league.league_id ? 'true' : false}
                                    />
                                </li>
                            );
                        })}
                    </ul>
                }
            </div>
        );
    }
}

export default LeagueList;
```

```js
// LeagueItem.js

import React, { Component, Fragment } from 'react';
import './LeagueItem.scss';

class LeagueItem extends Component {
    render() {
        const { league_name, league_id, setLeagueId, selected } = this.props;

        return (
            <Fragment>
                <span
                    className={`league ${selected && 'selected'}`}
                    onClick={() => setLeagueId(league_id)}
                >
                    {league_name}
                </span>
            </Fragment>
        );
    }
}

export default LeagueItem;
```

여기까지하고 개발자도구로 확인해보세요. 새로운 리그를 선택하면 leagueId가 App에서 잘 담기나요?

마찬가지로 바뀐 리그에 따른 api호출을 해주면 끝나겠죠?

```js
// MatchList.js

...            
            const { startDate, endDate } = this.props.range;
            const { leagueId } = this.props;
            const extraQuery = `&from=${startDate}&to=${endDate}&league_id=${leagueId}`
...            

    componentDidUpdate(prevProps, prevState) {
        if (
            this.props.range !== prevProps.range ||
            this.props.leagueId !== prevProps.leagueId
        ) {
            this.getData();
        }
    }
```

축하드립니다. 이제는 좀 무언가를 만든다는 느낌이 드시나요?

skeleton코드를 띄우고 몇번씩 반복해보세요.

복습하실때는 절대 눈으로만 보시지 마시고, 코드를 여러번 반복해서 작성해보세요.

