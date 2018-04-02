# Webpack 
자바스크립트 모듈 번들러. 모듈과 모듈이 사용하는 정적 자원(CSS,Image)들을 묶어서 하나 또는 몇 개의 파일로 번들링한다.
- 스크립트 태그를 이용해 여러 js를 로딩하는 것보다 상대적으로 초기 로딩 속도가 빠르다.
- css, image 같은 정적 파일들도 묶어서 배포가 가능하다.
- Babel 같은 트랜스파일 기능도 전처리가 가능하다.
- HMR(Hot Modulle Replacement)를 지원하므로, 코드가 수정되면 자동으로 번들링 후 페이지를 갱신한다.
- 다양 로더 및 플러그인 제공

1) Webpack 및 웹서버 설치
```
npm install -g webpack
npm install -g live-server
cd 프로젝트폴더
npm init
npm install --save-dev webpack
```
2) 프로젝트 초기화
- src/employees.js
```javascript
var employees = [ 
    { name : '홍길동', email:'gdhong@opensg.net', mobile:'010-2222-3331' },
    { name : '이몽룡', email:'mrlee@opensg.net', mobile:'010-2222-3332' },
    { name : '성춘향', email:'chsung@opensg.net', mobile:'010-2222-3333' },
    { name : '박문수', email:'mspark@opensg.net', mobile:'010-2222-3334' },
    { name : '변학도', email:'hdbyun@opensg.net', mobile:'010-2222-3335' }
];
module.exports = employees;
```
- src/app.js
```javascript
let employees = require('./employees');
var str = "";
str += "<h2>직원정보</h2><hr />"
for (var i=0; i < employees.length; i++) {
    str += '<div>' + employees[i].name + ' : ' + employees[i].email +
              ', ' + employees[i].mobile + '</div>';
}
document.getElementById('app').innerHTML = str;
```
- public/index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>웹팩 예제1</title>
</head>
<body>
  <div id="app"></div>
  <script src="bundle.js"></script>
</body>
</html>
```

2) Webpack 실행
```
webpack src/app.js public/bundle.js
```
webpack을 위처럼 Command Line에서 실행할 수도 있지만 구성 파일을 작성하는 것이 효과적
- webpack.config.js
- ``devtool: 'source-map'`` bundling 되기 전의 상태에서 디버깅 가능
- ``__dirname`` 현재 실행중인 스크립트가 포함된 디렉토리의 이름을 나타내는 node.js의 전역변수
```javascript
module.exports = { 
    devtool : 'source-map',
    entry: __dirname + '/src/app.js',  
    output: { 
        path: __dirname + '/public', 
        filename: 'bundle.js' 
    }
 };
```
파일명이 webpack.config.js 파일인 경우는 다음과 같이 입력하면 되고, 파일명이 다르면 ``webpack 파일명.js``
```
webpack
```
task runner를 npm으로 지정하여 webpack 실행하기
- package.json의 scripts 부분에 추가
```json
"scripts": {
    "build": "webpack",
  }
```
build task runner 실행 후 브라우저에서 확인
```
npm run build
live-server
```
##### webpack 개발 서버
- 로컬 개발을 위한 webpack 옵션
- node.js+express로 구성되어 있어 별도의 웹서버(ex. live-server) 없이 동작할 수 있게 함.

1) webpack 개발 서버 설치
```
// 전역 설치
npm install webpack-dev-server --g
// 개발 의존성 추가
npm install --save-dev webpack-dev-server 
```
- webpack.config.js에 devServer 옵션 추가
- contentBase : 프로젝트 루트가 아니라 다른경로를 contentBase로 지정할 때
- port : default는 8080
- inline : true는 페이지가 변경되면 새로고침 되도록 한다.
- historyApiFallback : true는 매핑되지 않은 개발 서버에 대한 요청 시 /index.html로 라우팅 됨.
```javascript
devServer : {
        contentBase : './public',
        inline:true, 
        historyApiFallback :true,
        port : 7777
}    
```
개발 환경과 운영 환경 분리를 위해 cross-env 추가
```
npm install --save-dev cross-env
```
- package.json의 scripts 부분에 추가
- --hot : HMR(Hot Module Replacement) 지원. 코드 수정 후 저장하면 즉시 화면에 반영됨
- --open : webpack 개발 서버 구동 후 브라우저 자동 열림
```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "cross-env  NODE_ENV=production  webpack",
    "start": "cross-env  NODE_ENV=development  webpack-dev-server  --hot  --open"      
  },
```
```
npm run start
```