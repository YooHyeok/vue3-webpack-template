
# 프로젝트 구성 및 환경 설정
<details>
<summary>접기/펼치기</summary>
<br>

## Webpack 기본 템플릿

__webpack__: 모듈(패키지) 번들러의 핵심 패키지<br>
__webpack-cli__: 터미널에서 Webpack 명령(CLI)을 사용할 수 있음<br>
__webpack-dev-server__: 개발용으로 Live Server를 실행(HMR)<br>

__html-webpack-plugin__: 최초 실행될 HTML 파일(템플릿)을 연결<br>
__copy-webpack-plugin__: 정적 파일(파비콘, 이미지 등)을 제품(`dist`) 폴더로 복사<br>

__sass-loader__: SCSS(Sass) 파일을 로드<br>
__postcss-loader__: PostCSS(Autoprefixer)로 스타일 파일을 처리<br>
__css-loader__: CSS 파일을 로드<br>
__style-loader__: 로드된 스타일(CSS)을 `<style>`로 `<head>`에 삽입<br>
__babel-loader__: JS 파일을 로드<br>

__@babel/core__: ES6 이상의 코드를 ES5 이하 버전으로 변환<br>
__@babel/preset-env__: Babel 지원 스펙을 지정<br>
__@babel/plugin-transform-runtime__: Async/Await 문법 지원<br>

__sass__: SCSS(Sass) 문법을 해석(스타일 전처리기)<br>
__postcss__: Autoprefixer 등의 다양한 스타일 후처리기 패키지<br>
__autoprefixer__: 스타일에 자동으로 공급 업체 접두사(Vendor prefix)를 적용하는 PostCSS의 플러그인<br> 

## 주의사항!

- `npm i -D webpack-dev-server@next`로 설치(webpack-cli 버전(@4^)과 일치)!<br>
- `package.json` 옵션으로 `browserslist` 추가!<br>
- `.postcssrc.js` 생성(PostCSS 구성 옵션)!<br>
- `.babelrc.js` 생성(Babel 구성 옵션)!<br>

<br>

## 프로젝트 clone `degit`
깃 리포지토리에서 파일만 복사해오는 도구인 degit 옵션을 사용한다.

#### npx 방식
설치 없이 실행 가능한 npm CLI 이다.
아래 명령을 통해 degit 패키지를 즉시 실행하여 GitHub 레포지토리를 다운로드한다.
- `npx degit YooHyeok/vue3-webpack-template#main <생성할 프로젝트(디렉토리)명>`

#### npm 방식
degit을 로컬 또는 전역에 설치한 뒤 degit 명령을 통해 GitHub 레포지토리를 다운로드한다.
- `npm install -g degit`
- `degit YooHyeok/vue3-webpack-template#main <생성할 프로젝트(디렉토리)명>`

#### 설치 및 기동
- `npm install`
- `npm run dev`

<br>

## npm run dev 기동 이슈
### error:0308010C:digital envelope routines::unsupported

<br>

<details>
<summary>접기/펼치기</summary>
<br>

```
> webpack-basic@1.0.0 dev
> webpack-dev-server --mode development

Browserslist: caniuse-lite is outdated. Please run:
npx browserslist@latest --update-db

Why you should do it regularly:
https://github.com/browserslist/browserslist#browsers-data-updating
<i> [webpack-dev-server] Project is running at http://[::1]:8080/
<i> [webpack-dev-server] Content not from webpack is served from {프로젝트 경로}
```
</details>
<br>

위 로그 이후 아래 에러 발생
<br>

<details>
<summary>접기/펼치기</summary>
<br>

```
node:internal/crypto/hash:68
  this[kHandle] = new _Hash(algorithm, xofLen);
                  ^

Error: error:0308010C:digital envelope routines::unsupported
    at new Hash (node:internal/crypto/hash:68:19)
    at Object.createHash (node:crypto:138:10)
    at BulkUpdateDecorator.hashFactory (C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack\lib\util\createHash.js:144:18)
    at BulkUpdateDecorator.update (C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack\lib\util\createHash.js:46:50)
    at RawSource.updateHash (C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack-sources\lib\RawSource.js:64:8)
    at NormalModule._initBuildHash (C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack\lib\NormalModule.js:829:17)
    at handleParseResult (C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack\lib\NormalModule.js:894:10)
    at C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack\lib\NormalModule.js:985:4
    at processResult (C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack\lib\NormalModule.js:716:11)
    at C:\Programming\workspace_vs\vue-cli-9din-sass-props-button\node_modules\webpack\lib\NormalModule.js:768:5 {
  opensslErrorStack: [ 'error:03000086:digital envelope routines::initialization error' ],
  library: 'digital envelope routines',
  reason: 'unsupported',
  code: 'ERR_OSSL_EVP_UNSUPPORTED'
}
```
</details>

<br>

 Node.js 17 이상에서 Webpack 4~5 버전 사용 시 자주 발생하는 OpenSSL 관련 이슈
 Node.js 17부터 OpenSSL 3이 기본이 되었고, 이전 방식으로 createHash()를 사용하는 라이브러리들이 충돌을 일으키기 때문에 발생.
```
Error: error:0308010C:digital envelope routines::unsupported
code: 'ERR_OSSL_EVP_UNSUPPORTED'
```
이는 webpack, webpack-dev-server, webpack-sources 내부에서 해시 생성(crypto.createHash) 시 발생하는 문제

### 해결방법 3가지
Webpack의 해시 함수가 OpenSSL 3과 충돌하는 문제를 우회하는 방식

- Windows (CMD)
  ```
  set NODE_OPTIONS=--openssl-legacy-provider
  ```

- Windows (PowerShell)
  ```
  $env:NODE_OPTIONS="--openssl-legacy-provider"
  ```

- macOS / Linux / WSL
  ```
  export NODE_OPTIONS=--openssl-legacy-provider
  ```

</details>
