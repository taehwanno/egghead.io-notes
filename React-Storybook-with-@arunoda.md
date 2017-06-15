# React Storybook with [@arunoda](https://github.com/arunoda)

- [x] 1. ​Getting Started with React Storybook
- [x] 2. Jest Snapshot Testing with React Storybook
- [x] 3. Add React Storybook to a Project
- [x] 4. Write the First React Storybook Story
- [x] 5. Load React Storybook Stories Dynamically
- [x] 6. Inspect Component Events with React Storybook

**Lecture reference github repository**

- [kadira-samples/basic-react-storybook-app](https://github.com/kadira-samples/basic-react-storybook-app)

# [1. Getting Started with React Storybook](https://egghead.io/lessons/react-getting-started-with-react-storybook#/tab-transcript)

```bash
$ yarn global add create-react-app hello-storybook
$ yarn global add getstorybook

$ cd hello-storybook
$ getstorybook
$ yarn storybook
```

Convert storybook to static website (`build-storybook`)

```bash
$ yarn build-storybook
```

# [2. Jest Snapshot Testing with React Storybook](https://egghead.io/lessons/react-jest-snapshot-testing-with-react-storybook)

> React Storybook allows for easy snapshot testing integration with Facebook's Jest testing framework. Snapshot testing allows you to capture the state of your UI and then notify you when that UI has been altered. We can easily create and update snapshots with Storybook!

- Install dependencies

```bash
$ yarn add --dev @kadira/storybook @kadira/storyshots
```

- Add `test-storybook` script to `package.json`

```json
"scripts": {
  "test-storybook": "storyshots"
}
```

- Change react component code and Update snapshot

```bash
$ yarn test-storybook -- -u
```

- Finally, test storybook

```bash
$ yarn test-storybook
```

# [3. Add React Storybook to a Project](https://egghead.io/lessons/react-add-react-storybook-to-a-project)

> In this lesson, we show how to setup React Storybook into an existing React project. We’ll go through common configuration files and how to adjust them.

# [4. Write the First React Storybook Story](https://egghead.io/lessons/react-write-the-first-react-storybook-story)

> Story is the smallest unit of React Storybook. A story describes a React Component, and its various states and variations. Stories can describe complex components with dynamic properties and data loaded from a service. In this lesson we will write our first React Storybook story.

- import css entry file to `.storybook/config.js`
- simple example

```jsx
// MainSection.js

import React from 'react';
import { storiesOf } from '@kadira/storybook';
import MainSection from '../components/MainSection';

const stories = storiesOf('MainSection', module);

stories.add('with a single todo item', () => (
  <MainSection
    todos={[{ id: '1', text: 'Learn React' }]}
    actions={[]}
  />
));
```

# [5. Load React Storybook Stories Dynamically](https://egghead.io/lessons/react-load-react-storybook-stories-dynamically)

> React Component stories can be dynamically loaded into React Storybook instead of doing it manually every time.

```jsx
import { configure } from '@kadira/storybook';

import 'todomvc-app-css/index.css';

const req = require.context('../src/components', true, /\.stories\.js$/);

function loadStories() {
  req.keys().forEach(path => req(path));
}

configure(loadStories, module);
```

# [6. Inspect Component Events with React Storybook](https://egghead.io/lessons/react-inspect-component-events-with-react-storybook)

> Components trigger actions via function props to notify of events that occur within the Component. We can inspect those events using React Storybook.

> you could write actions about any prop event in your components, and try to see the output inside the action logger.

```js
import React from 'react';
import { storiesOf, action } from '@kadira/storybook';
import Header from '../components/Header';

const stories = storiesOf('Header', module);

stories.add('default view', () => (
  <Header
    addTodo={action('new-todo')}
  />
));
```
