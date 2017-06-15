# Jest with [@kentcdodds](https://github.com/kentcdodds)

- [x] 1. Test Javascript with Jest
- [x] 2. Add Babel Integration with Jest
- [x] 3. Track project code coverage with Jest
- [x] 4. Use Jest's Interactive Watch Mode
- [x] 5. Use Jest's Snapshot Testing Feature

**Lecture reference github repository**

- [kentcdodds/jest-tutorial](https://github.com/kentcdodds/jest-tutorial)

# [1. Test Javascript with Jest](https://egghead.io/lessons/javascript-test-javascript-with-jest)

> Let's learn how to unit test your JavaScript with [Jest](http://facebook.github.io/jest), a JavaScript unit testing framework from Facebook. We'll install and optimize Jest for this project and see how quick and easy it is to get things going with Jest.

If you don't need to simulate browser, set up jest's configuration testEnvironment: "node" in `package.json`

```json
"jest": {
  "testEnvironment": "node"
}
```

# [2. Add Babel Integration with Jest](https://egghead.io/lessons/javascript-add-babel-integration-with-jest)

> Jest will default to utilize babel when your `node_modules` contains `babel-jest`. Depending on your environment this may already be the case! In this quick lesson, find out why that is and how to ensure that continues to work.

```bash
$ yarn add --dev babel-jest
```

# [3. Track project code coverage with Jest](https://egghead.io/lessons/javascript-track-project-code-coverage-with-jest)

> Jest comes pre-packaged with the ability to track code coverage for the modules you're testing, but it takes a little extra work to make it track untested files as well. Let's look at what Jest can do for you, what you have to do yourself, and how to setup code coverage thresholds so you can work to improving the code coverage numbers for your projects.

```json
"jest": {
  "collectCoverageFrom": [
    "src/*.js"
  ],
  "coverageThreshold": {
    "global": {
      "branches": 100,
      "functions": 100,
      "lines": 100,
      "statements": 100
    }
  }
}
```

# [4. Use Jest's Interactive Watch Mode](https://egghead.io/lessons/javascript-use-jest-s-interactive-watch-mode)

> Jest ships with a pretty amazing interactive watch mode which you can initiate with `jest --watch`. Let's learn about the bits of interactivity and how this can really improve your workflow.

```bash
$ jest --watch --lastCommit
```

# [5. Use Jest's Snapshot Testing Feature](https://egghead.io/lessons/javascript-use-jest-s-snapshot-testing-feature)

> Often when testing, you use the actual result to create your assertion and have to manually update it as you make changes to the feature. With Jest snapshot testing, you can let Jest do this part for you and write more tests and features faster and with more confidence. Let's learn about how you can use Jest snapshot testing to improve your own workflow.

> In review, to use snapshot testing with Jest, you simply pass your serializable variable into expect and invoke toMatch(snapshot) on it. Then you develop normally, and update snapshots as needed. When you're ready to push your changes, make sure to include the updated snapshots in your pull requests, so reviewers can see how your changes impact the output.

Snapshot can improve pull reqeust review process
