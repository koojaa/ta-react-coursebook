sass사용법을 알아봤자 실제 프로젝트를 할 때 어디서부터 어떻게 시작할지를 모른다면 무슨 소용이 있을까요?

따라서 단순한 sass사용법보다는\(사실 css와 별로 다를게 없고\), 실제 플젝에서 sass파일을 어떻게 관리할지를 아는 것이 더 중요하다고 생각합니다.

지금 당장은 그 필요성이 와닿지 않으실 수 있겠지만, 6주 모든 수업이 끝나고 실제 플젝을 하실 때 이 내용이 분명 도움이 되시리라고 생각합니다.

---

컴포넌트별로 sass파일을 어떻게 관리해야할지에 대해서는 정답이 없습니다. 사실은 개발자 개발하기 편한대로 관리하면 됩니다.

\(sass파일을 관리하는 폴더를 별도로 만들어서 해당 폴더에 몽땅 sass파일을 집어넣어도 됩니다. 수업시간에 보여드렸죠?\)

하지만 저는 관련있는 파일들끼리 분리해서 별도의 폴더에 만들어 관리하는 것을 선호합니다.

그렇게하면 해당 상대경로에서 불러오기도 편하고 폴더구조가 한눈에 들어오기 때문에 개발할 때 편하다고 느끼기 때문입니다.

프로젝트를 새로 생성하고 시작해볼까요?

우선 컴포넌트를 만들고 해당 컴포넌트를 꾸미기 전에 전체적인 css\(scss\)초기화를 해보겠습니다.

그냥 index.css에 하는것이 아니라 초기화를 위한 파일을 따로 만들겠습니다.

\*base폴더에는 프로젝트 전반에 걸쳐서 적용할 스타일이 작성된 파일들을 모아주도록 할게요.

```js
// src/styles/base/_reset.scss

* {
    margin: 0;
    padding: 0;
}
```

여기서 reset.scss가 아니라 \_reset.scss라고 표현을 했습니다.

\_는 불러오기 전용 파일을 의미한다고 말씀드렸죠?

따라서 다른 컴포넌트에서 바로 reset.scss를 import해서 사용하는 것이 아니라,

다른 scss파일에서 불러와서 합쳐줬으면 하는 의도로 작성된 파일입니다.

```js
// src/styles/base/_all.scss

@import 'reset';
```

\_all.scss는 말그대로 base와 관련된 모든 불러오기 전용 scss파일을 불러와서 한 곳에 합쳐주는 역할을 하는 scss파일입니다.

이 또한 불러오기 전용으로 작성을 했으므로 한번 더 불러와 줍니다.

```js
// src/styles/main.scss

@import 'base/all';
```

이제는 초기화를 진행할 준비가 되었습니다.

main.scss는 불러오기 전용파일이 아니므로 이를 index.js에서 불러와서 적용해줍니다.

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from 'components/App';
import 'styles/main.scss';

import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

serviceWorker.unregister();
```

스타일 초기화를 끝냈으니 이제부터 컴포넌트를 하나 만들고 해당 컴포넌트를 중심으로 스타일파일을 작성해보겠습니다.

아래와 같이 Button컴포넌트와 그에 해당하는 스타일파일을 만들고 이 둘을 한 폴더에 집어넣겠습니다.

```js
// src/components/Button/Button.js

import React from 'react';
import './Button.scss';

class Button extends Component {
    render(){
        return (
            <div className="button" type="submit">
                {this.props.text}
            </div>
        )
    }
}

export default Button;


// src/components/Button/Button.scss
// 스타일링 자체에는 너무 신경쓰지 마세요. 어차피 본인만의 프로젝트를 할 때 공들여서 하시게 되겠죠?

.button {
    width: 150px;
    height: 100px;
    font-size: 1.5em;
    text-align: center;
    margin: 20px;
    background-color: red;
}
```

이번에는 라이브러리를 사용해서 위 버튼 컴포넌트를 꾸며볼까요?

sass-material-colors라는 라이브러리를 사용하기 위해 우선 해당 라이브러리를 설치해주세요.

```js
yarn add sass-material-colors
```

그러면 터미널을 통해 경로를 확인해야겠죠?

경로를 확인했으면 라이브러리 파일을 위한 폴더와 파일을 만들고 import를 해주세요.

```js
// src/styles/lib/_all.scss

@import '~sass-material-colors/sass/sass-material-colors';
```

마찬가지로 불러오기 전용이죠?

main.scss는 css\(scss\)초기화 같은 프로젝트에서 전반적으로 사용할 스타일 값들을 관리하는 파일이라면,

sass-material-colors와 같은 라이브러리는 컴포넌트마다 사용할 수도 있고 그렇지 않을 수도 있기때문에 별도의 utils파일로 모아볼게요.

```js
// src/styles/utils.js

@import 'lib/all';
```

이제 버튼에 적용해볼까요?

```js
// src/components/Button/Button.scss

// 이미 절대경로 설정이 끝났으므로 utils로 불러오면 됩니다.
@import 'utils.scss';

.button {
    width: 150px;
    height: 100px;
    font-size: 1.5em;
    text-align: center;
    margin: 20px;
    background: material-color('blue-grey', '600');
}
```

비슷한 방식으로 커스텀 mixin도 적용해보도록 할게요.

```js
// src/styles/lib/mixin/_material-shadow.scss
// 출처 - codepen.io

@mixin material-shadow($z-depth: 1, $strength: 1, $color: black) {
    @if $z-depth == 1 {
        box-shadow: 0 1px 3px rgba($color, $strength * 0.14), 0 1px 2px rgba($color, $strength * 0.24);
    }
    @if $z-depth == 2 {
        box-shadow: 0 3px 6px rgba($color, $strength * 0.16), 0 3px 6px rgba($color, $strength * 0.23);
    }
    @if $z-depth == 3 {
        box-shadow: 0 10px 20px rgba($color, $strength * 0.19), 0 6px 6px rgba($color, $strength * 0.23);
    }
    @if $z-depth == 4 {
        box-shadow: 0 15px 30px rgba($color, $strength * 0.25),
            0 10px 10px rgba($color, $strength * 0.22);
    }
    @if $z-depth == 5 {
        box-shadow: 0 20px 40px rgba($color, $strength * 0.3),
            0 15px 12px rgba($color, $strength * 0.22);
    }
    @if ($z-depth < 1) or ($z-depth > 5) {
        @warn "$z-depth must be between 1 and 5";
    }
}
```

```js
// src/styles/lib/_all.scss

@import '~sass-material-colors/sass/sass-material-colors';
@import 'mixin/material_shadow';
```

이제 버튼에서 해당 mixin을 사용해보세요.

mixin을 사용할때는 include를 사용합니다.

```js
// src/components/Button/Button.scss

// 이미 절대경로 설정이 끝났으므로 utils로 불러오면 됩니다.
@import 'utils.scss';

.button {
    width: 150px;
    height: 100px;
    font-size: 1.5em;
    text-align: center;
    margin: 20px;
    background: material-color('blue-grey', '600');
    @include material-shadow(5, 5);
}
```

매년 열리는 리액트 컨퍼런스를 보고 fb 리액트 코어팀이 쓰는 방식으로 따라해보셔도 좋고,

github에서 stars수가 많은 레포를 뜯어보면서 다른 개발자들의 컨벤션을 따라해보시는 것도 많은 도움이 될 것입니다.

따라서 프로젝트 스타일링은 정해진 답이 없습니다.

나중에 이런 구조화 작업이 익숙해지신다면, 본인만의 노하우가 담긴 폴더구조화도 진행해보세요.

