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
- [ ] 11. Expose modules to dependencies with Webpack
- [ ] 12. Initialize a Webpack Project with Karma for Testing
- [ ] 13. Use Chai assertions for tests in a Karma project
- [ ] 14. Use Karma for Unit Testing with Webpack
- [ ] 15. Add Code Coverage to tests in a Webpack project
- [ ] 16. Ensure all source files are included in test coverage reports with Webpack

## 1. Intro to the Production Webpack Course

## 2. Validate your Webpack config with webpack-validator

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

```
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

```
new webpack.LoaderOptionsPlugin({ 
  minimize: true,
  debug: false,
})
```

> What this is doing is it's taking any instance of process.env.nodeenv and replacing it with a string production. That way when React or even in our own code, we say process.env.nodeenv is production. It's going to replace this with production.

> It's going to evaluate this as true. In any instance where that value actually ends up being false in an if statement, for example, it can go ahead and remove that code as dead code. That's generally done by the UglifyJS plug-in which we'll add next.

> This saves us a ton of space when using React in particular, but it can also save us some space with other libraries as well. I should note also that this isn't just a size optimization, but it's also a performance optimization in the case of React. For example, it will disable runtime checks for your prop types.

```
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

```
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
## 12. Initialize a Webpack Project with Karma for Testing
## 13. Use Chai assertions for tests in a Karma project
## 14. Use Karma for Unit Testing with Webpack
## 15. Add Code Coverage to tests in a Webpack project
## 16. Ensure all source files are included in test coverage reports with Webpack
