[https://velog.io/@velopert/react-component-styling](https://velog.io/@velopert/react-component-styling)

위 김민준님 포스트에서 기본적인 sass사용법, 절대경로로 .scss파일 import하는 webpack 설정법, 라이브러리 사용법을 배우실 수 있습니다.

---

**보충설명**

\* CRA\(create-react-app\) 를 통해 만든 리액트 프로젝트에서는 node-sass설치만으로도 sass를 쉽게 사용할 수 있습니다.

이미 내부적으로 webpack의 sass-loader가 있기 때문에 sass-loader가 .scss파일을 불러오고,

우리가 설치한 node-sass와 연동해서 .scss파일을 .css파일로 변환시켜주고 변환된 css파일은 webpack 내장 css-loader가 처리해주게 됩니다.

---

\* 나중에 프로젝트 규모가 커지고 컴포넌트 규모가 복잡해질 경우를 대비해서

utils.js와 같은 유틸관련 파일을 상대경로가 아닌 절대경로로 관리할 필요가 있습니다.

그러기 위해서 우리는 webpack.config.js를 건드려야했죠.

이미 CRA가 설정해 놓은 webpack.config.js를 바꾸기 위해서는 yarn eject명령어를 통해 이를 밖으로 꺼내주어야 합니다.

한번 밖으로 꺼내면 다시 안으로 못 집어넣기 때문에 반드시 커밋을 하고 yarn eject - Y를 실행해야 합니다.

또한 yarn eject를 하면 \(최근 업데이트\) 간혹 npm pacakage들 중

**@babel/plugin-transform-react-jsx**

**@babel/plugin-transform-react-jsx-self**

**@babel/plugin-transform-react-jsx-source**

@babel 네임스페이스를 가진 위 세가지 패키지가 uninstall되는 경우가 있습니다. 

\(이 글을 쓰는 시점인 2020년 4월 5일에는 굳이 uninstall하지 않아도 잘 작동합니다\)

따라서 만약 yarn eject후 yarn start를 했을때 오류메세지가 보여질 경우

위 세가지를 yarn add 명령어로 각각 설치해주거나, 더 쉬운 방법은 node\_modules/ 폴더를 지우고 yarn add를 하면 정상적으로 실행이 됩니다.

---

\* 라이브러리를 사용할 때는 yarn add &lt;라이브러리명&gt; 설치 이후,

라이브러리에서 제공하는. scss파일이 어디에 있는지 확인하기 위해서 직접 node\_modules/ 폴더안을 들여다 봐야 하는데,

이 작업은 터미널로 확인하면 훨씬 편하게 하실 수 있습니다. 

라이브러리들 마다 경로가 다릅니다. 반드시 경로를 확인하세요.

---

\* 시간이 지나 링크에 나온 webpack.config.js 수정 코드가 바뀌었습니다.

```js
// webpack.config.js

...
	const getStyleLoaders = (cssOptions, preProcessor) => {
		const loaders = [
			isEnvDevelopment && require.resolve('style-loader'),
			isEnvProduction && {
				loader: MiniCssExtractPlugin.loader,
				// css is located in `static/css`, use '../../' to locate index.html folder
				// in production `paths.publicUrlOrPath` can be a relative path
				options: paths.publicUrlOrPath.startsWith('.') ? { publicPath: '../../' } : {},
			},
			{
				loader: require.resolve('css-loader'),
				options: cssOptions,
			},
		].filter(Boolean);
		if (preProcessor) {
			loaders.push(
				{
					loader: require.resolve('resolve-url-loader'),
					options: {
						sourceMap: isEnvProduction && shouldUseSourceMap,
					},
				},
				{
					loader: require.resolve(preProcessor),
					options: {
						sourceMap: true,
						sassOptions: {
							includePaths: [paths.appSrc + '/styles'],
						},
					},
				}
			);
		}
		return loaders;
	};

...
```

webpack설정파일의 getStylesLoaders함수를 위 코드로 바꾸어주세요.

