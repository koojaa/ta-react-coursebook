## **JSX**

JSX는 리액트 컴포넌트를 작성하면서 return문에 사용하는 문법입니다.

얼핏보면 HTML같지만, 실제로는 자바스크립트입니다.

JSX를 작성하면 바벨이 transpiling을 해줍니다.

```js
return (
    <div className="App">
        <h1>Hello React</h1>
    </div>
);
```

이렇게 컴포넌트에서 JSX를 작성하면 바벨 트렌스파일러가 자동으로 자바스크립트로 변환을 해줍니다.

babel사이트에 들어가서 어떻게 변환되는지 살펴보는것도 좋을 것 같네요!

또한 JSX는 그냥 단순한 HTML이 아니라고 말씀드렸죠?

따라서 지켜줘야 할 여러 규칙들이 있습니다.

규칙은 크게 일곱가지인데, 하나씩 살펴보도록 할게요.

#### 1. 태그는 반드시 닫아줘야 한다.

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>
        <p>hello react!</p>
      </div>
    );
  }
}

export default App;
```

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>
        <input type="text" />
      </div>
    );
  }
}

export default App;
```

주목할 점은 h1, div, p같은 태그 외에 input 같은 태그도 반드시 닫아주어야 한다는 것입니다.

JSX는 html이 아닙니다!

#### 2. 최상단에서는 반드시 div로 감싸주어야 한다.

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>
        <div>
          Hello
        </div>
        <div>
          Bye
        </div>
      </div>
    );
  }
}

export default App;
```

태그들은 반드시 밖에서 최종적으로 한번 더 감싸주도록 합니다.

아래코드처럼 div를 밖에서 감싸지 않고 div를 두번 연달아 쓰시면 에러가 납니다 !

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
        <div>
          Hello
        </div>
        <div>
          Bye
        </div>
    );
  }
}

export default App;
```

또한 div말고 Fragment를 사용해도된다고 했었죠?

하지만 Fragment를 권장하지는 않습니다. 일반 div보다는 무겁기 때문이에요.

하지만 시멘틱 마크업\(의미단위 마크업\)을 중요하게 생각하신다면 여전히 생각해볼만한 옵션입니다!

#### 3. JSX안에서 자바스크립트 값을 사용하고 싶을때는 {}를 사용한다.

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    const name = 'react'
    return (
      <div>
        hello {name}!
      </div>
    );
  }
}

export default App;
```

컴포넌트는 단순 UI를 보여주는 것 외에도 여러가지 기능을 제공한다고 말씀드렸죠?

위 코드처럼 {}를 사용해서 컴포넌트 안에서 자바스크립트값을 넣어줄 수도 있습니다.

JSX에서 자바스크립트 값을 사용할때는 위처럼 {}를 사용합니다.

#### 4. 조건부 렌더링을 하고싶으면 &&연산자나 삼항연산자를 사용한다.

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>
        {
          true
            ? console.log('correct')
            : console.log('incorrect')
        }
      </div>
    );
  }
}

export default App;
```

바벨은 if~else를 transpile하지 못합니다.

수업시간에 바벨에서 if else를 transpile하지 못하는걸 보여드렸죠?

따라서 if else문을 사용하려면 즉시호출함수 패턴을 사용해야합니다.

사실 즉시호출함수를 사용할일은 정말 드물기 때문에, 3항연산자 또는 && operator\(and연산자\)를 사용하면 대부분의 경우를 처리할 수 있습빈다.

#### 5. 인라인 스타일링은 항상 객체형식으로 작성한다.

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    const style = {
      backgroundColor: 'red',
    };

    return <div style={style}>react</div>;
  }
}

export default App;
```

스타일은 항상 객체형식으로 작성을 합니다.

따라서 인라인 스타일로 스타일을 매긴다면 중괄호가 두번 들어가겠죠?

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return <div style={{backgroundColor: 'red'}}>react</div>;
  }
}

export default App;
```

#### 6. 별도의 스타일파일을 만들었으면 class대신 className을 사용한다.

```js
import React, { Component } from 'react';
import './App.css'

class App extends Component {
  render() {
    return (
      <div className="App">
        react
      </div>
    );
  }
}

export default App;
```

스타일을 적용할때는 class가 아닌 className을 써주세요.

사실 class로 해도되지만 그렇게하면 warning이 발생합니다.

class키워드는 이미 자바스크립트에서 사용중이기때문에 가급적 피하도록 합니다.

#### 7. 주석은 {/\* \*/}을 사용해 작성한다.

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>
        {/* 주석은 이렇게 */}
        <h1 // 태그안에서>
           react
        </h1>
      </div>
    );
  }
}

export default App;
```

주석은 {/\* \*/}형식을 사용하셔야 합니다!

여기까지 총 일곱가지 JSX규칙을 배워봤어요. 이 규칙들을 꼭 지켜주셔야 한다는것을 잊지마세요!

