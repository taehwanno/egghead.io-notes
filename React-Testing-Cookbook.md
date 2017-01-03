# [React Testing Cookbook](https://egghead.io/courses/react-testing-cookbook)

- [x] 1. Setting up dependencies
- [x] 2. Running tests
- [x] 3. Utility modules
- [x] 4. Intro to Shallow Rendering
- [x] 5. JSX error diffs
- [x] 6. Element types with Shallow Rendering
- [x] 7. className with Shallow Rendering
- [x] 8. Conditional ClassName with Shallow Rendering
- [x] 9. Reusing test boilerplate
- [x] 10. Children with Shallow Rendering
- [x] 11. The Redux Store - Multiple Actions
- [x] 12. The Redux Store - Initial State
- [x] 13. Redux Reducers

## 1. Setting up dependencies

> To write tests for our React code, we need to first install some libraries for running tests and writing assertions. In this lesson we walk through setting up Mocha as our test runner and expect as our assertion library. We will also set up some React and JSX specific test tools (React Test Utils) to make writing our tests easier.

mocha, expect, react-addons-test-utils를 사용한다.

## 2. Running tests

> In this lesson, we walk through how to setup our tests and run them. We write a quick empty first test and assertion, so we can run the tests. Using Mocha, we can do this manually each time with the Mocha CLI. We can also automate this using task runner features from tools like Grunt, Gulp, Webpack, or npm scripts. In this course, we will use the common npm test script setup to run our tests. We will also use the Babel compiler to write our tests with modern JavaScript syntax.

## 3. Utility modules

> When writing tests for our React code, it is helpful to pull out any functionality that doesn't have to do with our UI components into separate modules, so that they can be tested separately. In this lesson, we will take a look at a React component and how we can pull out some of its generic utility logic into a separate module. We will then write some tests for that module.

UI 컴포넌트와 관련이 없는 기능을 분리할 경우 테스트를 나눠 진행할 수 있다.

```js
// src/utilities/createId.js

export default function(uniqueNumber, description) {
  const shortKebabDescription = description
    .toLowerCase()
    .split(' ')
    .slice(0, 2)
    .join('-');
  return `${uniqueNumber}-${shortKebabDescription}`;
}
```

```js
// src/utilities/createdId.spec.js

import expect from 'expect';
import createId from './createId';

describe('createId', () => {
  it('sholud convert a description into a unique id', () => {
    const actual = createId(123, 'Cool example description');
    const expected = '123-cool-example';
    expect(actual).toEqual(expected);
  });
});
```

## 4. Intro to Shallow Rendering

> In this lesson, we walk through how to use one of React's Test Utilities (from the react-addons-test-utils package) called "Shallow Rendering". This lets us render our React component one level deep - without a DOM - so that we can write tests for it. It works kind of like ReactDOM.render, where the shallow renderer is a temporary place to "hold" your rendered component so that you can assert things about its output. Tests written using the shallow renderer are great for stateless or "dumb" components that simply have their props passed to them from a parent container or "smart" component. These shallow renderer tests work especially well [with stateless function components](https://egghead.io/lessons/react-building-stateless-function-components-new-in-react-0-14). They also work well for "unit" tests where you want to make sure your code works in isolation.

> NOTE: The React team has recommended composing the majority of your apps using these stateless "dumb" components, so the majority of lessons in this course will focus on writing simple unit tests for these stateless components using Shallow Rendering. If you also want to write tests for the stateful components that are tied to different components and state and can't be tested in isolation, you may want to look at using a DOM (with something like Karma or jsdom) and React's other test utilities like renderIntoDocument and Simulate. However, I've found that it is helpful to try to compose most of your project with simple, isolated, stateless or "pure" components that can be unit tested with Shallow Rendering, and then wrap these components with a few stateful or "impure" components that you can either not worry about testing (what I do most of the time because it is difficult to test stateful components), or write separate integration and functional tests for them using different tools.

```js
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import expect from 'expect';

const CoolComponent = ({greeting}) => (
  <div>
    <h1>Greeting</h1>
    <div>{greeting}</div>
  </div>
);

describe('CoolComponent', () => {

  it('should...', () => {
    const renderer = TestUtils.createRenderer();
    renderer.render(<CoolComponent greeting='hello world' />);
    const output = renderer.getRenderOuptut();
    console.log(output);
  });
});
```

> our renderer variable has become a temporary place where we can store the output of our component. With the shallow-renderer these components are only rendered one level deep so you don't need a DOM, it just gives you the object structure that you can assert things about.

shallow render의 경우 DOM 트리 구조 위계에서 가장 상위의 요소만 렌더링한다.

## 5. JSX error diffs

> When writing React component tests, it can be hard to decipher the error diffs of broken tests, since they are just the final objects that React uses under the hood. There are some nice libraries that let you extend your assertion library to show JSX diffs; in this lesson we will wire up one of these libraries to show how to debug JSX error diffs from your React tests. We will also show how narrowing down what you are testing helps to make the test error diffs easier to deal with.

> NOTE: This lesson uses the expect-jsx package, but there are other options available for both expect and other assertion libraries.

JSX가 원하는 결과대로 렌더링되지 않았을 때 발생한 에러를 보고 디버깅하기는 어렵다. `expectJSX`를 활용하여 디버깅 환경을 개선시키자.

```js
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import expect from 'expect';
import expectJSX from 'expect-jsx';

expect.extend(expectJSX);

const CoolComponent = ({greeting}) => (
  <div>
    <h1>Greeting</h1>
    <div>{greeting}!</div>
  </div>
);

describe('CoolComponent', () => {

  it('should render the greeting', () => {
    const renderer = TestUtils.createRenderer();
    renderer.render(<CoolComponent greeting='hello world' />);
    const actual = renderer.getRenderOutput();
    const expected = <div>hello world</div>;
    expect(actual).toIncludeJSX(expected);
  });
});
```

```
  1) CoolComponent should render the greeting:
     Error: Expected '<div> <h1> Greeting </h1> <div> hello world! </div> </div>' to include '<div> hello world </div>'
      at assert (node_modules/expect/lib/assert.js:29:9)
      at Expectation.toInclude (node_modules/expect/lib/Expectation.js:215:28)
      at Expectation.toIncludeJSX (node_modules/expect-jsx/index-dist.js:33:131)
      at Context.<anonymous> (src/home/quote/components/CoolComponent.spec.js:22:20)
```

위의 에러는 `expectJSX`를 적용했을 경우의 에러 메세지이다.

## 6. Element types with Shallow Rendering

> When you render a component with the Shallow Renderer, you have access to the underlying object. We can write lots of useful tests to check that our components are working as expected. In this lesson, we will use the type property on the shallow rendered component to make sure that the root element is what we expect it to be.

```js
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import expect from 'expect';
import LikeCounter from './LikeCounter';

describe('LikeCounter', () => {

  it('should render the greeting', () => {
    const renderer = TestUtils.createRenderer();
    renderer.render(<LikeCounter count={5} />);
    const actual = renderer.getRenderOutput().type;
    const expected = 'a';
    expect(actual).toEqual(expected);
  });
});
```

쉘로우 렌더링을 통해 그린 컴포넌트의 `type`을 알 수 있다.

## 7. className with Shallow Rendering

> The React Shallow Renderer test utility lets us inspect the output of a component one level deep. In this lesson, we will examine the rendered output of props, specifically the className prop. We will then use the ES2015 String.includes() method to check that our rendered className includes what we expect.

```js
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import expect from 'expect';
import Icon from './Icon';

describe('Icon', () => {

  it('should render the icon', () => {
    const renderer = TestUtils.createRenderer();
    renderer.render(<Icon name="facebook" />);
    const actual = renderer.getRenderOutput().props.className.includes('facebook');
    const expected = true;
    expect(actual).toEqual(expected);
  });
});
```

## 8. Conditional className with Shallow Rendering

> Often our components have output that shows differently depending on the props it is given; in this lesson, we go over how to compare the className prop element tree output based on conditional input.

```js
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import expect from 'expect';
import LikeCounter from './LikeCounter';

describe('LikeCounter', () => {

  it('should show the like count as active', () => {
    const renderer = TestUtils.createRenderer();
    renderer.render(<LikeCounter count={5} isActive={true} />);
    const actual = renderer.getRenderOutput().props.className
      .includes('LikeCounter--active');
    const expected = true;
    expect(actual).toEqual(expected);
  });

  it('should show the like count as inactive', () => {
    const renderer = TestUtils.createRenderer();
    renderer.render(<LikeCounter count={5} isActive={false} />);
    const actual = renderer.getRenderOutput().props.className
      .includes('LikeCounter--active');
    const expected = false;
    expect(actual).toEqual(expected);
  });
});
```

## 9. Reusing test boilerplate

> Setting up a shallow renderer for each test can be redundant, especially when trying to write similar tests that have slight tweaks. In this lesson, we go over how you can reduce some of the overlapping code so that each test only contains the unique pieces of the test.

```js
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import expect from 'expect';
import LikeCounter from './LikeCounter';

describe('LikeCounter', () => {

  describe('isActive', () => {
    function renderLikeCounter(isActive) {
      const renderer = TestUtils.createRenderer();
      renderer.render(<LikeCounter count={5} isActive={isActive} />);
      return renderer.getRenderOutput().props.className
        .includes('LikeCounter--active');
    }

    it('should show the like count as active', () => {
      expect(renderLikeCounter(true)).toEqual(true);
    });

    it('should show the like count as inactive', () => {
      expect(renderLikeCounter(false)).toEqual(false);
    });
  });
});
```

> The main idea, the principle behind it, is to take pieces that are repetitive in your test and to pull them out into reusable parts.

보일러플레이트 코드를 줄일 수 있다.

## 10. Children with Shallow Rendering

> When testing React components, we often want to make sure the rendered output of the component matches what we expect. With the React Shallow Renderer, we can check the entire rendered output of a component, the children prop, or a subset of the children prop. We can also use 3rd party libraries to check that this element tree includes a specific piece. In this lesson we will walk through examples of each.

```js
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import expect from 'expect';
import expectJSX from 'expect-jsx';
expect.extend(expectJSX);
import LikeCounter from './LikeCounter';

describe('LikeCounter', () => {

  it('should render like counts', () => {
    const renderer = TestUtils.createRenderer();
    renderer.render(<LikeCounter count={5} />);
    const actual = renderer.getRenderOutput();
    const expected = '5 likes';
    expect(actual).toIncludeJSX(expected);
  });
});
```

## 11. The Redux Store - Multiple Actions

> When using Redux, we can test that our application state changes are working by testing that dispatching actions to the store creates our expected output. In this lesson we will run a few realistic actions back to back (as if the user is using the app) and then test that the state tree looks as we expect it to. These types of tests that ensure all of your redux logic is working as expected give you a lot of value for not too much effort (they test your entire app's state in one big swoop). You may also find it useful to add more granular/individual tests for your reducers and/or actions, which we will cover in other lessons in this course.

> NOTE: This lesson assumes you have used Redux. If you are new to Redux, it is recommended that you first watch the Getting Started With Redux course.

```js
import { store } from './store';
import expect from 'expect';

describe('store', () => {

  it('should work with a series of actions', () => {
    const actions = [
      {
        type: 'ADD_QUOTE_BY_ID',
        payload: {
          text: 'The best way to cheer yourself up is to try to cheer somebody else up.',
          author: 'Mark Twain',
          id: 1,
          likeCount: 24,
        },
      },
      {
        type: 'ADD_QUOTE_BY_ID',
        payload: {
          text: 'Whatever you are, be a good one',
          author: 'Abraham Lincoln',
          id: 2,
          likeCount: 0,
        },
      },
      {
        type: 'REMOVE_QUOTE_BY_ID',
        payload: { id: 1 },
      },
      {
        type: 'LIKE_QUOTE_BY_ID',
        payload: { id: 2 },
      },
      {
        type: 'LIKE_QUOTE_BY_ID',
        payload: { id: 2 },
      },
      {
        type: 'UNLIKE_QUOTE_BY_ID',
        payload: { id: 2 },
      },
      {
        type: 'UPDATE_THEME_COLOR',
        payload: { color: '#777777' }
      }
    ];

    actions.forEach(action => store.dispatch(action));

    const actual = store.getState();
    const expected = {
      quotes: [
        {
          text: 'Whatever you are, be a good one',
          author: 'Abraham Lincoln',
          id: 2,
          likeCount: 1,
        }
      ],
      theme: {
        color: '#777777',
      },
    };
    expect(actual).toEqual(expected);
  });
});
```

## 12. The Redux Store - Initial State

> When using Redux, it can be useful to test that your initial state is being rendered as expected. In this lesson, we will set up a test to check the initial state of our store.

```js
import { store } from './store';
import expect from 'expect';

describe('store', () => {

  it('should initialize', () => {
    const actual = store.getState();
    const expected = {
      quotes: [],
      theme: {
        color: '#5DC4C6',
      }
    };
    expect(actual).toEqual(expected);
  });
});
```

## 13. Redux Reducers

> Sometimes we want to test our Redux reducers to make sure they work as expected. In this lesson we will walk through setting up some Redux reducer tests for common situations and edge cases.

```js
// themeReducers.spec.js

import expect from 'expect';
import themeReducer from './themeReducer';

describe('themeReducer', () => {

  it('should change the theme color', () => {

    function stateBefore() {
      return {
        color: '#A3BE89',
      };
    }

    const action = {
      type: 'UPDATE_THEME_COLOR',
      payload: {
        color: '#B38EAE'
      }
    };
    const actual = themeReducer(stateBefore(), action);
    const expected = {
      color: '#B38EAE',
    };
    expect(actual).toEqual(expected);
  });
});
```

```js
// quoteReducer.spec.js

import expect from 'expect';
import quoteReducer from './quoteReducer';

describe('quoteReducer', () => {

  function stateBefore() {
    return [
      {
        text: 'Lorem ipsum',
        author: 'Jane Doe',
        id: 1,
        likeCount: 7,
      },
      {
        text: 'Ullamco laboris nisi ut aliquip',
        author: 'John Smith',
        id: 2,
        likeCount: 0,
      }
    ]
  }

  it('should add quotes by id', () => {
    const action = {
      type: 'ADD_QUOTE_BY_ID',
      payload: {
        text: 'This is a new quote',
        author: 'Someone awesome',
        id: 3,
        likeCount: 0,
      },
    };
    const actual = quoteReducer(stateBefore(), action);
    const expected = [
      {
        text: 'Lorem ipsum',
        author: 'Jane Doe',
        id: 1,
        likeCount: 7,
      },
      {
        text: 'Ullamco laboris nisi ut aliquip',
        author: 'John Smith',
        id: 2,
        likeCount: 0,
      },
      {
        text: 'This is a new quote',
        author: 'Someone awesome',
        id: 3,
        likeCount: 0,
      },
    ];
    expect(actual).toEqual(expected);
  });

  it('should return previous state when trying to make a quote like count negative', () => {
    const action = {
      type: 'UNLIKE_QUOTE_BY_ID',
      payload: { id: 2 },
    };
    const actual = quoteReducer(stateBefore(), action);
    const expected = stateBefore();
    expect(actual).toEqual(expected);
  });
});
```