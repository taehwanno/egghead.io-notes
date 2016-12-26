# Using Webpack for Production JavaScript Applications

- [x] 1. Intro to the Production Webpack Course
- [x] 2. Validate your Webpack config with webpack-validator
- [x] 3. Tree shaking with Webpack 2
- [x] 4. Polyfill Promises for Webpack 2
- [x] 5. Maintain sane file sizes with webpack code splitting
- [x] 6. Hashing with Webpack for long term caching
- [x] 7. Grouping vendor files with the Webpack CommonsChunkPlugin
- [x] 8. Optimize React size and performance with Webpack production plugins
- [x] 9. Chunking common modules from multiple apps with the Webpack CommonsChunkPlugin
- [x] 10. Import a non-ES6 module with Webpack
- [x] 11. Expose modules to dependencies with Webpack
- [x] 12. Initialize a Webpack Project with Karma for Testing
- [x] 13. Use Chai assertions for tests in a Karma project
- [x] 14. Use Karma for Unit Testing with Webpack
- [x] 15. Add Code Coverage to tests in a Webpack project
- [x] 16. Ensure all source files are included in test coverage reports with Webpack

## 1. Intro to the Production Webpack Course

> There are several lessons that will build on top of this project. It is a fairly standard, small webpack bundled project. In this lesson we’ll explore the project a bit so you’re familiar with how things are set up for future lessons.

## 2. Validate your Webpack config with webpack-validator

> It’s quite common to make a mistake while developing your webpack configuration. A simple typo can cost you hours of development time. With [webpack-validator](https://www.npmjs.com/package/webpack-validator), you can save yourself a ton of time by validating that your webpack configuration is free of common mistakes.

webpack 설정시 에러가 났을 때 원인을 찾기 힘들다. 특히 오타일 경우는 loader, plugin 설정을 다시 찾아볼 가능성이 크다. [webpack-validator](https://www.npmjs.com/package/webpack-validator)을 써서 webpack configuration 설정시 도움을 받자. 개발, 배포용 webpack.*.config.js 각각 npm script를 작성해서 활용하도록 하자.

## 3. Tree Shaking with Webpack 2

> The concept of tree shaking basically says that if you’re not using some piece of code, then exclude it from the final bundle, even when that piece of code is exported from a module. 

> One of the aspects of ES6 modules is that they're statically analyzable. This means tools like Webpack can predict exactly which exports are used and which are not.

> This will automatically refresh, and now, we'll see that the helpers file is a little bit different with regards to how these ES6 module exports are transpiled. Before it was pretty straightforward common JS. Now, it's a little bit different because Webpack is going the transpilation for us.

`babel-preset-es2015-webpack`을 설치하고 완전성을 위해 `babel-preset-es2015`을 `package.json`에서 삭제한다. 그리고 `.babelrc` 파일에서 `preset`을 `["es2015", "stage-2"]`에서 `["es2015-webpack", "stage-2"]`으로 수정한다. webpack이 트랜스파일 과정에 참여하기 때문에 이전의 코드와는 달라지게 된다.

> Because Webpack has an understanding of all the modules throughout our system, it realizes addClass is not being used anywhere in our application, and so, it adds this comment, "Unused harmony export addClass," and it excludes it from the list of things that are being added to our common JS export.

> You'll notice though, that the addClass function is still being added in here, but because it's not being referenced, when we go ahead and uglify our code using the uglify.js plug-in, this function will be automatically removed.

"Unused harmony export addClass"라는 코멘트가 달리고 common JS 형식으로 export되지 않는다. `addClass`에 대한 코드는 남아있지만 참조되지 않으며 해당 코드는 `uglify` 과정에서 자동으로 사라지게 된다.

## 4. Polyfill Promises for Webpack 2

> If you're going to use code splitting with Webpack 2, you'll need to make sure the browser has support for the ES6 Promise API. This means that if you are required to support an old browser, you need to provide a polyfill. Polyfilling is relatively trivial to do with Webpack, but because Webpack itself depends on this particular polyfill, you have to come up with another way of doing so. In this lesson, we'll see how we can use the polyfill.io service to automatically polyfill this API (and others) for us.

code splitting을 사용하려면 `Promise`가 사용자 브라우저에서 지원되야하며 [`polyfill.io`](https://qa.polyfill.io/v2/docs/)를 통해 userAgent를 분석해서 on-demand로 해당 브라우저에 필요한 polyfill 코드만 보내준다.

## 5. Maintain sane file sizes with webpack code splitting

> As a Single Page Application grows in size, the size of the payload can become a real problem for performance. In this lesson, learn how to leverage code splitting to easily implement lazy loading for your application to load only the code necessary for a particular feature or functionality.

> we're going to use the ES6 system api to import this module.

> This(`System.import(...)`) will return us a promise.

> That is the module that we just imported using the system api. Webpack has a runtime for us, so we don't have to worry about loading that ourselves.

> Webpack is intelligent and will create a bundle for us for every possible combination or dynamic import like so.

[systemjs](https://github.com/systemjs/systemjs)를 활용하여 code splitting을 통해 on-demand로 원하는 `.js` 파일을 브라우저에서 동적으로 불러올 수 있다. 생성하고 싶은 스크립트에 대해 동적으로 생성가능하며 SPA 환경에서 `router`와 맞물려 사용될 수 있다. lazy loading을 통해 처음 내려받게 되는 파일의 크기를 줄일 수 있다.

## 6. Hashing with Webpack for long term caching

> Leveraging the browser cache is an important part of page load performance. A great way to utilize this cache is by versioning your resources. In this lesson, learn how to use Webpack’s hashing feature so you can take advantage of long term caching of your assets.

스크립트 파일이 로드될 때 long-term 캐싱 전략을 활용하는 방법에 대한 기초 설명 강좌. 핵심은 webpack의 `chunkhash`를 활용하여 URL을 통해 캐싱을 하며 내용이 바뀔 경우 해당 해쉬값을 바꿔 다시 받게 한다.

## 7. Grouping vendor files with the Webpack CommonsChunkPlugin

> Often, you have dependencies which you rarely change. In these cases, you can leverage the CommonsChunkPlugin to automatically put these modules in a separate bundled file so they can be cached and loaded separately from the rest of your code (leveraging the browser cache much more effectively).

자주 변화하는 비지니스 로직이 아닌 라이브러리 벤더 코드는 webpack built-in 플러그인 `CommonsChunkPlugin`을 활용해서 따로 앱으로부터 분리할 수 있다. 이 때 multiple entry를 가지게 되며 각각의 entry는 고유한 output file을 가져야하며 다수의 entry에서 output file이 겹칠 경우 에러가 발생하게 된다.

```js
plugins: [
  new webpack.optimize.CommonsChunkPlugin({
    name: 'vendor',
  })
]
```

테스트 환경에서는 따로 분리할 필요가 없으므로 `process.env.NODE_ENV`를 활용해서 `plugins` 배열 추가 유무를 결정하자

## 8. Optimize React size and performance with Webpack production plugins

> You can fine tune several webpack plugins to make your bundle as small as it can be for your specific application. However there are a few things you can do for pretty much every application to make it smaller and run faster. In this lesson we’ll combine several webpack plugins to optimize things for a React application (this is also applicable for non-React applications as well). 

> This will be "New Webpack.loader options plugin." This one is actually included by default with that -p argument, but it only specifies minimize is true. It will also specify debug is false just to speed up our build times.

> The loader options plug-in essentially tells all of our loaders that we're in minimized mode, and they can optimize according to whatever optimizations they can do, or the code that they're loading. I should also note that the loader options plug-in is only available in Webpack 2.

```js
new webpack.LoaderOptionsPlugin({ 
  minimize: true,
  debug: false,
})
```

> What this is doing is it's taking any instance of process.env.nodeenv and replacing it with a string production. That way when React or even in our own code, we say process.env.nodeenv is production. It's going to replace this with production.

> It's going to evaluate this as true. In any instance where that value actually ends up being false in an if statement, for example, it can go ahead and remove that code as dead code. That's generally done by the UglifyJS plug-in which we'll add next.

> This saves us a ton of space when using React in particular, but it can also save us some space with other libraries as well. I should note also that this isn't just a size optimization, but it's also a performance optimization in the case of React. For example, it will disable runtime checks for your prop types.

```js
new webpack.DefinePlugin({
  'process.env': {
    NODE_ENV: '"production"',
  },
})
```

## 9. Chunking common modules from multiple apps with the Webpack CommonsChunkPlugin

> If you have a multi-page application (as opposed to a single page app), you’re likely sharing modules between these pages. By chunking these common modules into a single common bundle, you can leverage the browser cache much more powerfully. In this lesson we’ll use webpack’s CommonsChunkPlugin to easily share common modules between apps.

> You'll likely be required to explicitly list the entries to use in the CommonsChunk, though. You can do this by adding a chunks property with an array of the entry names to have this plugin apply to.

## 10. Import a non-ES6 module with Webpack

> When you have a dependency that does not export itself properly, you can use the exports-loader to force it to export the pieces of the file that you need.

```js
import leftPad from 'exports?leftPad!./non_node_modules/left-pad';
```

ES6 module을 통해 `export`되지 않은 파일은 `exports-loader`를 이용해서 처리가능하다.

```js
import leftPad from 'imports?window=>{}!exports?leftPad!./non_node_modules/left-pad';
```

window 전역객체를 오염시키고 있는 모듈의 경우 `imports-loader`를 통해 처리가능하다.

```js
module: {
  loaders: [
    {
      test: require.resolve('./src/js/non_node_modules/left-pad'),
      loader: ['imports?window=>{}', 'exports?leftPad']
    }
  ]
}
```

해당 파일 (`leftPad`)를 `import` 할 때 마다 loader의 쿼리 스트링을 작성하기엔 번거로우므로 `module`의 `loader`에서 `require.resolve`를 활용해서 처리한다.

## 11. Expose modules to dependencies with Webpack

> When you have a dependency that has dependencies on global variables (like jQuery or lodash) or assumes that `this` is bound to `window`, you can use the imports-loader to provide those dependencies explicitly. 

내가 직접 작성한 코드가 아닌 의존성으로 가지는 모듈에서 `window` 전역변수를 통해 외부 라이브러리를 참조하는 경우 `imports-loader`를 통해 ES6 module을 활용할 수 있다.

## 12. Initialize a Webpack Project with Karma for Testing

> There are several steps involved with setting up `Karma` to work on your webpack project. In this first step we'll initialize our karma configuration and setup our `package.json` scripts.

```
npm install --save-dev karma karma-chrome-launcher karma-mocha mocha
./node_modules/.bin/karma init
```

```js
module.exports = function setKarmaConfig(config) {
  config.set({
    basePath: '',
    frameworks: ['mocha'],
    files: [
      'src/js/**/*.test.js'
    ],
    reporters: ['progress'],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: false,
    browsers: ['Chrome'],
    singleRun: true,
    concurrency: Infinity
  })
}
```

```json
"config": {
  "ghooks": {
    "pre-commit": "opt --in pre-commit --exec \"npm run validate\""
  }
},
"scripts": {
  "validate": "npm-run-all --parallel validate-webpack:* lint test",
  "validate-webpack:dev": "webpack-validator webpack.config.js --env.dev",
  "validate-webpack:prod": "webpack-validator webpack.config.js --env.prod",
  "test": "karma start",
  "test:watch": "npm test -- --auto-watch --no-single-run"
}
```

## 13. Use Chai assertions for tests in a Karma project

> [Chai](https://npmjs.com/package/chai) assertions work great with the [Mocha](https://npmjs.com/package/mocha) testing framework. In this lesson we'll walk through out to install Chai and [karma-chai](https://www.npmjs.com/package/karma-chai) so you can use the expect assertions in your tests.

``` 
npm -i -D chai karma-chai
```

```js
module.exports = function setKarmaConfig(config) {
  config.set({
    ...
    frameworks: ['mocha', 'chai'],
    ...
  })
}
```

## 14. Use Karma for Unit Testing with Webpack

> When writing tests run by Karma for an application that’s bundled with webpack, it’s easiest to integrate webpack and Karma directly together. In this lesson we’ll see how to utilize the karma-webpack plugin and reuse our existing webpack configuration to preprocess our test files with webpack.

> What we need to do is tell Karma to pre-process all of our test files with Webpack, so that these import statements are resolved, and the bundle for that test file is delivered to the browser to rerun for our test.

> For Karma, Webpack is what's called a pre-processor. We basically want to pipe all of our test files through Webpack, so Webpack can bundle these and prepare them for delivery to the browser. Then, the browser will take this bundle and run it, which will run our test.

> The way that we tell Webpack what configuration to use is by using a Webpack property. Here, it will take an object with our configuration. We don't want to duplicate our configuration both in our Karma config and our Webpack config.

테스트 파일 내부에서 `import`, `require`을 사용해서 파일을 들고오게 되면 브라우저에서 인식을 하지 못한다. 그러므로 `karma-webpack`을 통해 해결한다.

```
npm i -D karma-webpack
```

```js
const webpackEnv = { test: true };
const webpackConfig = require('./webpack.config')(webpackEnv);
const fileGlob = 'src/js/**/*.test.js';

module.exports = function setKarmaConfig(config) {
  config.set({
    ...
    files: [fileGlob],
    preprocessors: {
      [fileGlob]: ['webpack'],
    },
    webpack: webpackConfig,
    webpackMiddleware: { noInfo: true },
    ...
  });
};
```

## 15. Add Code Coverage to tests in a Webpack project

> How much of your code runs during unit testing is an extremely valuable metric to track. Utilizing code the `karma-coverage` plugin and `babel-plugin-__coverage__` plugin, we can get an accurate measure of how well we’re covering the files that we are testing.

```
npm i -D karma-coverage
```

```js
module.exports = function setKarmaConfig(config) {
  config.set({
    reporters: ['progress', 'coverage'],
    coverageReporter: {
      reporters: [
        { type: 'lcov', dir: 'coverage/', subdir: '.' },
        { type: 'json', dir: 'coverage/', subdir: '.' },
        { type: 'text-summary' },
      ]
    }
  });
}
```

```
npm i -D babel-plugin-__coverage__
```

```json
// .babelrc

{
  "presets": ["es2015-webpack", "stage-2"],
  "env": {
    "test": {
      "plugins": [
        "__coverage__"
      ]
    }
  }
}
```

```js
// karma.conf.js
...
process.env.BABEL_ENV = 'test';
...
```

babel을 통해 트랜스파일되어 변환된 코드의 커버리지가 아닌 변환되기 전의 코드에 대해 커버리지를 측정해야함으로 `babel-plugin-__coverage__`를 활용한다. 테스트 환경에서만 `babel-plugin-__coverage__`를 사용하기 위해 `.babelrc` 파일에 `env` 속성을 추가한다.

```js
// .babelrc

{
  "presets": ...,
  "env": {
    "test": {
      "plugins": [
        ["__coverage__", { "ignore": "*.+(test|stub).*" } ]
      ]
    }
  }
}
```

테스트 파일에 대한 coverage는 필요없으므로 `.babelrc`에서 `__coverage__` 플러그인에 대한 `ignore` 속성을 추가한다.

## 16. Ensure all source files are included in test coverage reports with Webpack

> If you’re only instrumenting the files in your project that are under test then your code coverage report will be misleading and it will be difficult for you to track or enforce improvements to application coverage over time. In this lesson we’ll learn how to ensure all source files are included in coverage reports and how to enforce a specific threshold so you can work toward improving application code coverage.

`*.test.js` 형식의 파일이 존재하는 `*.js` 파일만이 커버리지에 포함이 되므로 테스트 파일이 없는 모든 파일에 대해 커버리지를 측정하도록 설정한다.

```js
...
const testGlob = 'src/js/**/*.test.js';
const srcGlob = 'src/js/**/*!(test|stub).js'

module.exports = function setKarmaConfig(config) {
  config.set({
    ...
    files: [testGlob, srcGlob],
    preprocessors: {
      [testGlob]: ['webpack'],
      [srcGlob]: ['webpack'],
    },
    ...
  });
};
```

`istanbul`을 활용해서 각 파일의 커버리지에 대해 thresold를 설정해서 해당 기준을 충족시키지 못하는 파일이 존재하는 경우 에러를 발생시키도록 할 수 있다.

```
npm install -D istanbul
```

```js
// package.json

"scripts": {
  ...
  "check-coverage": "istanbul check-coverage --statements 23 --branches 5 --functions 9 --lines 24",
  "validate": "npm-run-all --parallel validate-webpack:* lint test --serial check-coverage",
  "validate-webpack:dev": "webpack-validator webpack.config.js --env.dev",
  "validate-webpack:prod": "webpack-validator webpack.config.js --env.prod",
  ...
},
```

> However, we can't run this in parallel with the test. This check coverage script has to run after the tests have run and the coverage report has been collected. But with MPM run all, we can add this serial flag here and we'll put check coverage right there.