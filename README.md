# ES2015 환경 설정

## 들어가며
npm, webpack, babel 사용 해서 ES2015로 개발 할 수 있는 환경을 구축해보자.


### npm 설치
아래 명령어로 설치 여부 확인 후 없으면 node를 설치합니다.

```
npm -v
```

### webpack 설정
아래 명령어로 프로젝트 폴더 생성합니다.

```
mkdir es2015-examples
cd es2015-examples
```

아래 명령어로 package.json 파일 생성합니다.

```
npm init -y
```

webpack 모듈과 cli를 추가합니다.

```
npm install webpack webpack-cli --save-dev
```

테스트를 위해 `src/index.js` 파일을 생성하고 아래처럼 작성합니다.

```js
alert('hello');
```

`packages.json` 파일에 webpack을 실행할 스크립트를 추가합니다.

```
  "scripts": {
    "build": "webpack"
  },
```

아래 명령어로 webpack을 실행합니다.

```
npm run build
```

기본 설정 경로에 번들링 된 `dist/main.js` 파일이 생성 됐음을 확인 할 수 있습니다.
`dist/index.html` 파일 생성하고 아래처럼 작성합니다.

```html
<!doctype html>
<html>
  <head>
    <title>es2015 example</title>
  </head>
  <body>
    <script src="main.js"></script>
  </body>
</html>
```

### webpack-dev-server 설정

로컬 환경에서 쉽게 디버깅 할 수 있도록 webpack-dev-server를 설치합니다.

```
npm install webpack-dev-server --save-dev
```

`package.json` 파일에 스크립트를 추가합니다.
- watch: 스크립트는 파일이 변경됐을때 재빌드 되도록 webpack에 watch 옵션을 설정합니다.
- start : 스크립트는 webpack server를 띄우는 명령어로 파일 변경 됐을때 자동 갱신 되도록 hot 옵션과 브라우져가 자동으로 실행되게 open 옵션을 추가합니다.

```
  "scripts": {
    "build": "webpack",
    "watch": "webpack --watch",
    "start": "webpack-dev-server --hot --open"
  },
```

watch와 start를 실행합니다.

```
npm run watch
npm run start
```

브라우져에서 `dist` 폴더로 이동해서 alert 동작하는지 확인합니다.
정상 동작하면 `src/index.js` 파일을 아래처럼 변경했을때 정상 동작하는지도 확인합니다.

```js
const a = () => console.log('hello');
a();
```

webpack server가 실행 할때 `dist/index.html` 자동으로 찾도록 바꿔봅시다.

webpack의 설정을 기술하는 `webpack.config.js` 파일을 생성하고 아래 설정을 추가합니다.

```js
const path = require('path');

module.exports = {
    entry: './src/index.js',
    devtool: 'inline-source-map',
    devServer: {
        contentBase: 'dist'
    },
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist'),
    },
};
```

참고 : https://webpack.js.org/guides/development/#using-webpack-dev-server

webpack server를 다시 실행해서 정상 동작하는지 확인해보자.

### babel 설정
브라우져에서 `dist/main.js` 파일을 확인해보면 ES6가 하위 브라우져에 호환 가능한 버전(ECMA-262 5판)으로 변환되지 않는 것을 확인 할 수 있습니다.

babel로 변환하기 위해서 babel 핵심 모듈을 설치합니다.(-D 옵션은 --save-dev와 동일)

```
npm install -D babel-loader @babel/core @babel/preset-env
```

`webpack.config.js` 파일에 webpack 빌드 할때 babel로 변환하는 설정을 추가합니다.

```js
  module: {
    rules: [
      {
        test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  },
```
참고 : https://webpack.js.org/loaders/babel-loader/#root

설정이 변경되었으니 watch, start를 중단 후 다시 실행합니다.
`dist/main.js` 변경되었는지 확인합니다.

## 나가며
이로써 ES6 프로젝트를 시작 할 수 있는 기본적인 환경을 구축해봤습니다. 한번 설정 놓고 약간만 변형하면 또 다른 프로젝트를 만들 때 사용 할 수 있습니다. (product 빌드 버젼은 더 많은 설정이 필요합니다.)
그래서 리액트나 앵귤러처럼 많은 프로젝트에서 사용하는 모듈은 쉽게 설정 할수 있도록 CLI를 제공합니다.