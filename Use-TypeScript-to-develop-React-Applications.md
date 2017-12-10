# [Use TypeScript to develop React Applications](https://egghead.io/courses/use-typescript-to-develop-react-applications)

- [x] 1. Why use TypeScript with React
- [x] 2. Bootstrap a TypeScript + React project
- [x] 3. Create stateless React components using TypeScript
- [x] 4. Create stateful React Components using TypeScript
- [x] 5. Publish a React component with TypeScript

## [1. Why use TypeScript with React](https://egghead.io/lessons/egghead-why-use-typescript-with-react)

> You get excellent developer experience when using TypeScript with React. This is better when compared to many other frameworks that work off of simple template strings because JSX is embedded within TypeScript.

> In this lesson we give a demonstration of the some of the powerful refactorings and type checks enabled by using TypeScript with React.

### Pros

- Autocomplete
- Type Checking
- Refactoring
  - Rename component name, props
- Understanding Code

## [2. Bootstrap a TypeScript + React project](https://egghead.io/lessons/egghead-bootstrap-a-typescript-react-project)

> Learn how to setup a TypeScript + React project from scratch. Understand the reason behind every line involved in the configuration allowing you to customize it at will in the future.

```bash
$ npm install react react-dom
$ npm install --save-dev webpack webpack-dev-server typescript @types/react @types/react-dom ts-loader
```

```json
{
  "scripts": {
    "build": "webpack ./webpack.config.js",
    "start": "webpack-dev-server ./webpack.config.js --content-base ./public"
  }
}
```

```js
module.exports = {
  devtool: 'inline-source-map',
  entry: './src/app.tsx',
  output: {
    path: __dirname + './public',
    filename: 'build/app.js',
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js']
  },
  module: {
    rules: [
      { test: /\.tsx?$/, loader: 'ts-loader' }
    ]
  }
};
```

```json
{
  "compilerOptions": {
    "sourceMap": true,
    "module": "commonjs",
    "target": "es5",
    "jsx": "react"
  },
  "include": [
    "src"
  ],
  "compileOnSave": false
}
```

## [3. Create stateless React components using TypeScript](https://egghead.io/lessons/react-create-stateless-react-components-using-typescript)

> You can create a stateless React component in TypeScript as easily as creating a function.

> But if you want to create high quality idiomatic React + TypeScript components we cover some improved patterns in this lesson.

```tsx
import * as React from 'react';
import * as ReactDOM from 'react-dom';

type AppProps = { message: string };
const App: React.SFC<AppProps> = ({ message }) => <div>{message}</div>;

ReactDOM.render(
  <App message="Hello world again" />,
  document.getElementById('root')
);
```

## [4. Create stateful React Components using TypeScript](https://egghead.io/lessons/egghead-create-stateful-react-components-using-typescript)

> You can create stateful React Components in TypeScript by extending from the React.Component class. This parent class takes two generic parameters, Props and State.

> This lessons shows these generic parameters and React.Component in action.

```tsx
import * as React from 'react';

type AppProps = {
  message: string;
}
type AppState = {
  count: number;
}

class App extends React.Component<AppProps, AppState> {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
    this.increment = this.increment.bind(this);
  }

  increment() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div onClick={this.increment}>
        {this.props.message} {this.state.count}
      </div>
    );
  }
}
```

## [5. Publish a React component with TypeScript](https://egghead.io/lessons/egghead-publish-a-react-component-with-typescript)

> In this lesson we look at how to create a React Component package using TypeScript.

> Creating a React Package is very similar to creating any other TypeScript Node package and we demonstrate that here.
