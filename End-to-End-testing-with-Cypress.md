# [End to End testing with Cypress](https://egghead.io/courses/end-to-end-testing-with-cypress)

- [x] 1. Course Overview: End to End Testing with Cypress
- [x] 2. Add Cypress to an Existing Application with npm
- [x] 3. Visit a page with Cypress
- [x] 4. Find and Test Focused Input with Chrome’s DevTools in Cypress
- [x] 5. Test React’s Controlled Input with Cypress Selector Playground
- [x] 6. Reduce Code Duplication in Cypress Tests with beforeEach
- [x] 7. Stub Network Requests in a Cypress Test
- [x] 8. Load Data from Test Fixtures in Cypress
- [x] 9. Wait for XHR Responses in a Cypress Test
- [x] 10. Create a Single Custom Cypress Command from Multiple Commands
- [x] 11. Stub a Post Request for Successful Form Submission with Cypress
- [x] 12. Test XHR Failure Conditions with Cypress
- [x] 13. Interact with Hidden Elements in a Cypress Test
- [x] 14. Create Aliases for DOM Elements in Cypress Tests
- [x] 15. Test Variations of a Feature in Cypress with a data-driven Test
- [x] 16. Access step-by-step Logs with Chrome devTools and Cypress UI
- [x] 17. Create True end-to-end Tests with Cypress
- [x] 18. Run Cypress Tests without the UI

## 1. Course Overview: End to End Testing with Cypress

> Testing applications in a real browser is incredibly valuable.

> Historically, the tools available to automate these tests have left a bit to be desired. Those tests control the browser as a black box and as our applications get more complex, it gets harder to keep those tests from becoming flaky.

> We often end up doing time consuming and costly manual testing.

> In this course we'll see how Cypress is changing all of that.

> Cypress allows us to create relaible, flake-free tests that run in a real browser with a powerful UI that makes easy work of debugging. Our tests are written in a straightforward JavaScript API built on tool that may already be familiar, like Mocha and Chai.

> This course will teach you how to create reliable tests for your application without the pain that traditionally comes with UI testing.

## 2. Add Cypress to an Existing Application with npm

> Adding Cypress to a project is a simple npm install away. We won’t need any global dependencies beyond node and npm to get started with Cypress. In this lesson we’ll look at our existing application, add Cypress as a dev dependency with npm and fire it up for the first time to have a look around.

```shell
$ npm install --save-dev cypress
```

- `package.json`

```json
{
  "scripts": {
    "cypress": "cypress open"
  }
}
```

```shell
$ npm run cypress
```

- `cypress/integration/first.spec.js`

```js
describe('Simple test', () => {
  it('Works', () => {
    expect(true).to.equal(true);
  })
});
```

## 3. Visit a page with Cypress

> In order to test our application, Cypress will need to visit it in the browser. In this lesson we’ll see how we can visit our application with Cypress and how to configure a baseUrl for Cypress to make that even easier.

- `cypress/integration/form-input.spec.js`

```js
describe('Form Input', () => {
  it('Visits the app', () => {
    cy.visit('http://localhost:3030');
  });
});
```

- `cypress.json`

```json
{
  "baseUrl": "http://localhost:3030"
}
```

- `cypress/integration/form-input.spec.js`

```diff
describe('Form Input', () => {
  it('Visits the app', () => {
-    cy.visit('http://localhost:3030');
+    cy.visit('/');
  });
});
```

## 4. Find and Test Focused Input with Chrome’s DevTools in Cypress

> In this lesson, we’ll add tests that finds a focused input. We’ll use Chrome’s dev tools from inside the Cypress runner to inspect the element and update our test to verify that the expected element is focused. We’ll see how Cypress can be used to test drive our application by creating a failing test and updating our application code to make it pass.

- `cypress/integration/form-input.spec.js`

```js
describe('Form Input', () => {
  it('Visits the app', () => {
    cy.visit('/');
    cy.focused().should('have.class', 'new-todo');
  });
});
```

## 5. Test React’s Controlled Input with Cypress Selector Playground

> React based applications often use controlled inputs, meaning the input event leads to the application code setting the value of the very input we’re typing into. Since this moves the input setting behavior into the application code, we should have a test to guard against future changes that might break this behavior. In this lesson, we’ll use the Selector Playground feature in Cypress and create a test that enters text into an input and asserts that the value is the same as the entered text.

- `cypress/integration/form-input.spec.js`

```js
describe('Form Input', () => {
  it('Visits the app', () => {
    cy.visit('/');
    cy.focused().should('have.class', 'new-todo');
  });

  it.only('Accepts input', () => {
    const typedText = 'New Todo';
    cy.visit('/');
    cy.get('.new-todo')
      .type(typedText)
      .should('have.value', typedText);
  });
});
```

## 6. Reduce Code Duplication in Cypress Tests with beforeEach

> A lot of our tests will share setup steps. Since Cypress is using Mocha under the hood, we can take advantage of existing features such as the ability to define a beforeEach block that runs before each of our tests. In this lesson we’ll refactor two tests that contain duplicate commands by moving that duplication into the beforeEach for our describe block.

- `cypress/integration/form-input.spec.js`

```diff
describe('Form Input', () => {
+ beforeEach(() => {
+   cy.visit('/);
+ });

  it('Visits the app', () => {
-   cy.visit('/');
    cy.focused().should('have.class', 'new-todo');
  });

  it.only('Accepts input', () => {
    const typedText = 'New Todo';
-   cy.visit('/');
    cy.get('.new-todo')
      .type(typedText)
      .should('have.value', typedText);
  });
});
```

## 7. Stub Network Requests in a Cypress Test

> To keep our tests fast and easily repeatable, it makes sense to create many integration tests and fewer full end-to-end tests. In this lesson, we’ll see how to stub out network requests to create repeatable integration tests by ensuring our application runs in a known state for our tests.

- `cypress/integration/app-init.spec.js`

```js
describe('App initialization', () => {
  it('Displays todos from API on load', () => {
    cy.server();
    cy.route('GET', '/api/todos', [
      { id: 1, name: 'One', isCompleted: false },
      { id: 2, name: 'Two', isCompleted: false },
      { id: 3, name: 'Three', isCompleted: false },
      { id: 4, name: 'Four', isCompleted: false },
    ]);
    cy.visit('/');
    cy.get('.todo-list li').should('have.length', 4);
  });
});
```

## 8. Load Data from Test Fixtures in Cypress

> When creating integration tests with Cypress, we’ll often want to stub network requests that respond with large datasets. All of this mock data can lead to test code that is hard to read. In this lesson, we’ll see how to use fixtures to keep sample data in files and easily load it on demand in your tests.

- `cypress/fixtures/todos.json`

```json
[
  { "id": 1, "name": "One", "isCompleted": false },
  { "id": 2, "name": "Two", "isCompleted": false },
  { "id": 3, "name": "Three", "isCompleted": false },
  { "id": 4, "name": "Four", "isCompleted": false },
]
```

- `cypress/integration/app-init.spec.js`

```diff
describe('App initialization', () => {
  it('Displays todos from API on load', () => {
    cy.server();
-   cy.route('GET', '/api/todos', [
-     { id: 1, name: 'One', isCompleted: false },
-     { id: 2, name: 'Two', isCompleted: false },
-     { id: 3, name: 'Three', isCompleted: false },
-     { id: 4, name: 'Four', isCompleted: false },
-   ]);
+   cy.fixture('todos')
+     .then((todos) => {
+       cy.route('GET', '/api/todos', todos);
+     });
    cy.visit('/');
    cy.get('.todo-list li').should('have.length', 4);
  });
});
```

It's same to

```diff
describe('App initialization', () => {
  it('Displays todos from API on load', () => {
    cy.server();
-   cy.fixture('todos')
-     .then((todos) => {
-       cy.route('GET', '/api/todos', todos);
-     })
+   cy.route('GET', '/api/todos', 'fixture:todos');
    cy.visit('/');
    cy.get('.todo-list li').should('have.length', 4);
  });
});
```

## 9. Wait for XHR Responses in a Cypress Test

> When testing interactions that require asynchronous calls, we’ll need to wait on responses to make sure we’re asserting about the application state at the right time. With Cypress, we don’t have to use arbitrary time periods to wait. In this lesson, we’ll see how to use an alias for a network request and wait for it to complete without having to wait longer than required or guess at the duration.

- `cypress/integration/app-init.spec.js`

```diff
describe('App initialization', () => {
  it('Displays todos from API on load', () => {
    cy.server();
    cy.route('GET', '/api/todos', 'fixture:todos')
+     .as('load')
    cy.visit('/');
+
+   cy.wait('@load');
+
    cy.get('.todo-list li').should('have.length', 4);
  });
});
```

## 10. Create a Single Custom Cypress Command from Multiple Commands

> Cypress provides a straightforward API that allows you to define custom commands. In this lesson, we’ll take a series of Cypress commands and wrap them up in a single custom command so we can easily repeat these steps in multiple specs.

- `cypress/support/commands.js`

```js
Cypress.Commands.add('seedAndVisit', () => {
  cy.server();
  cy.route('GET', '/api/todos', 'fixture:todos').as('load')
  cy.visit('/');

  cy.wait('@load');
});
```

- `cypress/integration/app-init.spec.js`

```diff
describe('App initialization', () => {
  it('Displays todos from API on load', () => {
    cy.server();
-   cy.route('GET', '/api/todos', 'fixture:todos')
-     .as('load')
-   cy.visit('/');
-
-   cy.wait('@load');
-
+   cy.seedAndVisit();
    cy.get('.todo-list li').should('have.length', 4);
  });
});
```

- `cypress/integration/form-input.spec.js`

```diff
describe('Form Input', () => {
  beforeEach(() => {
-   cy.visit('/');
+   cy.seedAndVisit();
  });

  // ...
});
```

- `cypress/support/commands.js`

```diff
+Cypress.Commands.add('seedAndVisit', (seedData = 'fixture:todos') => {
  cy.server();
- cy.route('GET', '/api/todos', 'fixture:todos').as('load')
+ cy.route('GET', '/api/todos', seedData).as('load')
  cy.visit('/');

  cy.wait('@load');
});
```

- `cypress/integration/form-input.spec.js`

```diff
describe('Form Input', () => {
  beforeEach(() => {
-   cy.seedAndVisit();
+   cy.seedAndVisit([]);
  });

  // ...
});
```

## 11. Stub a Post Request for Successful Form Submission with Cypress

> In this lesson well stub a POST request and use Cypress commands to fill in and submit a form. We’ll wait for the submission to resolve and then assert that the new item was added to the list.

- `cypress/integration/form-submission.spec.js`

```js
describe('Form submission', () => {
  it('Adds a new todo item', () => {
    const newTodo = 'Buy Milk';
    cy.server();
    cy.route({
      method: 'POST',
      url: '/api/todos',
      response: { id: 123, name: newTodo, isComplete: false }
    }).as('save');

    cy.seedAndVisit();

    cy.get('.new-todo')
      .type(newTodo)
      .type('{enter}');

    cy.wait('@save');

    cy.get('.todo-list li')
      .should('have.length', 5);
  });
});
```

## 12. Test XHR Failure Conditions with Cypress

> Testing your application’s behavior when an XHR call results in an error can be difficult. The use of stubs for XHR calls makes it easy for us to setup failure scenarios and ensure that our front-end responds the way we expect. In this lesson, we’ll stub a 500 response for a form submission and verify that our application responds appropriately.

- `cypress/integration/form-submission.spec.js`

```js
describe('Form submission', () => {
  it('Adds a new todo item', () => {
    // ...
  });

  it.only('Shows an error message for a failed form submission', () => {
    const newTodo = 'Test';
    cy.server();
    cy
      .route({
        method: 'POST',
        url: '/api/todos',
        status: 500,
        response: {}
      })
      .as('save');

    cy.seedAndVisit();

    cy.get('.new-todo')
      .type(newTodo)
      .type('{enter}');

    cy.wait('@save');

    cy.get('.todo-list li').should('have.length', 4);

    cy.get('.error').should('be.visible');
  });
});
```

## 13. Interact with Hidden Elements in a Cypress Test

> We often only show UI elements as a result of some user interaction. Cypress detects visibility and by default won’t allow your test to interact with an element that isn’t visible. In this lesson, we’ll work with a button that is shown on hover and see how you can either bypass the visibility restriction or use Cypress to update the state of your application, making items visible prior to interacting with them.

- `cypress/integration/list-items.spec.js`

```js
describe('List Item Behavior', () => {
  it('Deletes an item', () => {
    cy.server();
    cy
      .route({
        method: 'DELETE',
        url: '/api/todos/*',
        response: {},
      })
      .as('delete');

    cy.seedAndVisit();

    cy
      .get('.todo-list li')
      .first()
      .find('.destory')
      .invoke('show')
      .click();

    cy.wait('@delete');

    cy.get('.todo-list li').should('have.length', 3);
  });
});
```

`force click` as

```diff
cy.get('.todo-list li')
  .first()
  .find('.destory')
- .invoke('show')
+ .click({ force: true });
```

But recommendation is use of `invoke('show')`.

## 14. Create Aliases for DOM Elements in Cypress Tests

> We’ll often need to access the same DOM elements multiple times in one test. Your first instinct might be to use cy.get and assign the result to a variable for reuse. This might appear to work fine at first, but can lead to trouble. Everything in Cypress is done asynchronously and you’re interacting with an application’s DOM, which is changing as your tests interact with it. In this lesson, we’ll see how we can reference DOM elements in multiple places with the alias feature in Cypress.

- `cypress/integration/list-items.spec.js`

```diff
describe('List Item Behavior', () => {
  it('Deletes an item', () => {
    cy.server();
    cy
      .route({
        method: 'DELETE',
        url: '/api/todos/*',
        response: {},
      })
      .as('delete');

    cy.seedAndVisit();

    cy
      .get('.todo-list li')
+     .as('list')
+
+   cy.get('@list')
      .first()
      .find('.destory')
      .invoke('show')
      .click();

    cy.wait('@delete');

-   cy.get('.todo-list li').should('have.length', 3);
+   cy.get('@list').should('have.length', 3);
  });
});
```

## 15. Test Variations of a Feature in Cypress with a data-driven Test

> Many applications have features that can be used with slight variations. Instead of maintaining multiple tests with nearly identical code, we can take advantage of the JavaScript runtime and use normal data structures and plain old JavaScript to test and make assertions for multiple interactions in a single test.

- `mixed_todos.json`

```json
[
  { "id": 1, "name": "One", "isCompleted": false },
  { "id": 2, "name": "Two", "isCompleted": false },
  { "id": 3, "name": "Three", "isCompleted": false },
  { "id": 4, "name": "Four", "isCompleted": false },
]
```

- `cypress/integration/footer.spec.js`

```js
describe('Footer', () => {
  it('Filters todos', () => {
    const filters = [
      { link: 'Active', expectedLength: 2 },
      { link: 'Completed', expectedLength: 2 },
      { link: 'All', expectedLength: 4 },
    ];
    cy.seedAndVisit('fixture:mixed_todos');

    cy.wrap(filters)
      .each(filter => {
        cy.contains(filter.link).click()
        cy.get('.todo-list li').should('have.length', filter.expectedLength);
      });
  });
});
```

## 16. Access step-by-step Logs with Chrome devTools and Cypress UI

> Testing is a great way to find and prevent future issues. Cypress takes this to the next level with a UI that gives you access to step-by-step logs of your test run as well as direct access to Chrome’s devTools. In this lesson, we’ll introduce a logic error and use the Cypress UI to track down and fix our bug.

Chrome DevTools with `debugger`

## 17. Create True end-to-end Tests with Cypress

> Integration tests let us keep our tests fast and reliable. They also allow us to test scenarios that are hard to recreate in a full end-to-end setup. That being said, we should round out our test suite with some high-level smoke tests. In this lesson, we’ll create some tests that seed our data-source and avoid stubbing our network calls, allowing us to test all parts of the application while using known data to keep our tests flake-free.

- `db.json` with `json-server`

```json
{
  "todos": []
}
```

- `cypress/integration/smoke-test-spec.js`

```js
describe('Smoke Tests', () => {
  beforeEach(() => {
    cy.request('DELETE', '/api/todos/all');
  });

  context('No Todos', () => {
    it('Adds a new todo', () => {
      cy.server();
      cy.route('POST', '/api/todos').as('save');

      cy.visit('/');

      cy.get('.new-todo')
        .type('New todo')
        .type('{enter}')

      cy.wait('@save');

      cy.get('.todo-list li').should('have.length', 1)
    });
  });

  context('With Todos', () => {
    beforeEach(() => {
      cy.fixture('todos')
        .then(todos => {
          cy.request('POST', '/api/todos/bulkload', { todos });
        });

      cy.server();
      cy.route('GET', '/api/todos').as('load');

      cy.visit('/');

      cy.wait('@load');
    });

    it.only('Deletes todos', () => {
      cy.route('DELETE', '/api/todos/*').as('delete');

      cy.get('.todo-list li')
        .each($el => {
          cy.wrap($el)
            .find('.destory')
            .invoke('show')
            .click()

          cy.wait('@delete');
        });
    });
  });
});
```

## 18. Run Cypress Tests without the UI

> The Cypress UI is a great way to work through individual tests and while TDD-ing new features, but it isn’t ideal for running large test suites or for running on a CI server. In this lesson, we’ll add an npm script to run all of our tests without the UI and look at the results of a full test run.

- `package.json`

```json
{
  "scripts": {
    "cypress": "cypress open",
    "cypress:all": "cypress run"
  }
}
```
