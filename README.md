_**본 문서는 탈잉에서 진행하는 &lt;실무자에게 배우는 리액트의 모든 것&gt; 수강생분들을 위한 리액트 코스북입니다. **_

**무단 전재, 복제 및 배포를 삼가해주세요.**

_수업 시작전 아래 항목들을 미리 설치해 오실것을 권장드립니다._

## 설치할 항목

* **Nodejs** : 리액트 프로젝트를 준비하기 위해 필요한 webpack, babel 등의 도구들을 실행하는데에 사용됩니다.
* **Yarn** : 자바스크립트 패키지를 관리하기 위해서 사용됩니다. Node.js 를 설치하면 npm 이 설치되어서 npm 으로 해도 되긴 하지만, yarn을 사용하면 훨씬 빠릅니다.
* **Code Editor\(VS Code\)** : 코드 에디터는 여러분이 좋아하는걸 사용하시면 됩니다. 저는 VS Code 를 사용하여 강의를 진행하겠습니다.

## Windows

#### **Nodejs**

[https://nodejs.org/ko/](https://nodejs.org/ko/)에 들어가셔서 좌측 LTS 버전을 설치하면 됩니다

#### **Yarn**

[https://yarnpkg.com/en/docs/install\#windows-tab](https://yarnpkg.com/en/docs/install#windows-tab)에 들어가셔서 인스톨러를 사용하여 설치하세요.

#### **Git Bash**

일반 CMD 대신에 Bash 시뮬레이터를 사용하는것이 좋습니다. 수업을 진행하게 되면서, 터미널에서 bash 명령어를 사용하게 됩니다. bash 명령어를 모르시더라도 걱정하지마세요. 수업 때 정말 기본적인것들은 짚고 넘어갑니다. [https://git-scm.com/download/win](https://git-scm.com/download/win)에 들어가셔서 본인의 운영체제 \(32 / 64\) 에 맞는 인스톨러를 사용하여 설치하세요.

**VS Code**

[https://code.visualstudio.com/](https://code.visualstudio.com/)에 들어가셔서 설치하세요

## MAC OS

#### NVM

nvm 은 노드 버전 관리 매니저입니다. 이 도구를 통해 설치하는것이 편합니다

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```

그 다음,`source ~/.bash_profile`명령어를 사용하거나 터미널을 재시작하여 새 .bash\_profile 을 적용하세요.

`nvm --version`을 실행해서 제대로 설치됐는지 확인하세요.

만약에 nvm 이 실행되지 않을 경우, vim 으로`.bash_profile`파일을 열어서 다음 라인들을 추가해주세요

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
```

그 다음 터미널을 다시 열거나`source ~/.bash_profile`을 하시면 됩니다.

이 명령어를 실행하면 최신 LTS 버전이 설치됩니다.

```
nvm install --lts
nvm list
nvm alias default [현재버전]
# 예: nvm alis default v8.11.3
```

#### **Yarn**

brew 를 사용하여 yarn 을 설치합니다. 만약에 Homebrew 가 설치되어있지 않다면 다음 명령어를 통해 설치하세요.

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

yarn 을 설치합니다

```
brew update
brew install yarn
```

그리고 환경변수에 yarn 의 global path 를 추가하세요.

```
echo 'export PATH="$(yarn global bin):$PATH"' >> ~/.bash_profile
```

#### VS Code

[https://code.visualstudio.com/](https://code.visualstudio.com/)을 통하여 설치하면 됩니다.

**\* 맥 카탈리나 사용자는 사용자 환경에 따라 설치가 충돌나는 경우가 있습니다. 설치가 원활하게 이루어지지 않을 경우 개인적으로 알려주세요.**

