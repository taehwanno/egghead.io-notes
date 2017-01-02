# [Building React Applications with Idiomatic Redux](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)

- [x] 1. Redux: Simplifying the Arrow Functions
- [x] 2. Supplying the Initial State
- [x] 3. Persisting the State to the Local Storage
- [x] 4. Refactoring the Entry Point
- [x] 5. Adding React Router to the Project
- [x] 6. Navigating with React Router <Link>
- [x] 7. Filtering Redux State with React Router Params
- [x] 8. Using withRouter() to Inject the Params into Connected Components
- [x] 9. Using mapDispatchToProps() Shorthand Notation
- [x] 10. Colocating Selectors with Reducers
- [x] 11. Normalizing the State Shape
- [x] 12. Wrapping dispatch() to Log Actions
- [x] 13. Adding a Fake Backend to the Project
- [x] 14. Fetching Data on Route Change
- [x] 15. Dispatching Actions with the Fetched Data
- [x] 16. Wrapping dispatch() to Recognize Promises
- [x] 17. The Middleware Chain
- [x] 18. Applying Redux Middleware
- [x] 19. Updating the State with the Fetched Data
- [x] 20. Refactoring the Reducers
- [x] 21. Displaying Loading Indicators
- [x] 22. Dispatching Actions Asynchronously with Thunks
- [x] 23. Avoding Race Conditions with Thunks
- [x] 24. Display Error Messages
- [x] 25. Creating Data on the Server
- [x] 26. Normalizing API Responses with normalizr
- [x] 27. Updating Data on the Server

## 1. Simplifying the Arrow Functions

> We will learn how to reduce the syntactic noise in the action creators and components when using arrow functions.

## 2. Supplying the Initial State

> We will learn how to start a Redux app with a previously persisted state, and how it merges with the initial state specified by the reducers.

## 3. Persisting the State to the Local Storage

> We will learn how to use store.subscribe() to efficiently persist some of the app’s state to localStorage and restore it after a refresh.

## 4. Refactoring the Entry Point

> We will learn how to better separate the code in the entry point to prepare it for adding the router.

## 5. Adding React Router to the Project

> We will learn how to add React Router to a Redux project and make it render our root component.

## 6. Navigating with React Router `<Link>`

> We will learn how to change the address bar using a component from React Router.

## 7. Filtering Redux State with React Router Params

> We will learn how adding React Router shifts the balance of responsibilities, and how the components can use both at the same time.

## 8. Using `withRouter()` to Inject the Params into Connected Components

> We will learn how to use withRouter() to inject params provided by React Router into connected components deep in the tree without passing them down all the way down as props.

## 9. Using `mapDispatchToProps()` Shorthand Notation

> We will learn how to avoid the boilerplate code in mapDispatchToProps() for the common case where action creator arguments match the callback prop arguments.

## 10. Colocating Selectors with Reducers

> We will learn how to encapsulate the knowledge about the state shape in the reducer files, so that the components don’t have to rely on it.

```js
import { connect } from 'react-redux';
import { withRouter } from 'react-router';
import { toggleTodo } from '../actions';
import { getVisibleTodos } from '../reducers';
import TodoList from './TodoList';

const getVisibleTodos = (todos, filter) => {
  switch (filter) {
    case 'all':
      return todos;
    case 'completed':
      return todos.filter(t => t.completed);
    case 'active':
      return todos.filter(t => !t.completed);
    default:
      throw new Error(`Unknown filter: ${filter}.`);
  }
};

const mapStateToProps = (state, { params }) => ({
  todos: getVisibleTodos(state.todos, params.filter || 'all'),
});

const VisibleTodoList = withRouter(connect(
  mapStateToProps,
  { onTodoClick: toggleTodo }
)(TodoList));

export default VisibleTodoList;
```

> My mapStateToProps function uses the getVisibleTodos function, and it passes the slice of the state corresponding to the todos. However, if I ever change the state structure, I'll have to remember to update this whole side.

> The convention I follow is simple. The default export is always the reducer function, but any named export starting with get is a function that prepares the data to be displayed by the UI. We usually call these functions selectors because they select something from the current state.


```js
export const getVisibleTodos = (state, filter) => fromTodos.getVisibleTodos(state.todos, filter);
```

> Now I can go back to my component, and I can import getVisibleTodos from the root reducer file. It encapsulates all the knowledge about the application state shape, so I can just pass it the whole state of my application, and it will figure out how to select the visible todos according to the logic described in selectors.

## 11. Normalizing the State Shape

> We will learn how to normalize the state shape to ensure data consistency that is important in real-world applications.

## 12. Wrapping `dispatch()` to Log Actions

> We will learn how centralized updates in Redux let us log every state change to the console along with the action that caused it.

## 13. Adding a Fake Backend to the Project

> We will learn about fake backend module that we will be using throughout the next lessons to simulate data fetching.

## 14. Fetching Data on Route Change

> We will learn how to fire up an async request when the route changes.

## 15. Dispatching Actions with the Fetched Data

> We will learn how to dispatch a Redux action after the data has been fetched and recap how to do it when the route changes.

## 16. Wrapping `dispatch()` to Recognize Promises

> The receiveTodos action creator is not very useful by itself because anytime we call it, we want to fetch the todos first. Also, they accept the same argument so it would be great if we could group this code into a single action creator.

```js
// src/components/VisibleTodoList.js

fetchData() {
  const { filter, receiveTodos } = this.props;
  fetchTodos(filter).then(todos =>
    receiveTodos(filter, todos)
  );
}
```

> I'm adding a special kind of action creator that I'm going to call an asynchronous action creator. It takes filter as an argument and it calls the API fetchTodos method with it. I'm using the promise .then method to transform the result of the promise from the response to the action object generated by receiveTodos given the filter and the response.

```js
// src/api/index.js

export const fetchTodos = (filter) =>
  api.fetchTodos(filter).then(response =>
    receiveTodos(filter, response)
  );
```

> However, by default, Redux only allows dispatch in plain objects rather than promises. We can teach it to recognize promises by using the same trick that we use for addLoggingToDispatch.

```js
// configureStore.js

const addPromiseSupportToDispatch = (store) => {
  const rawDispatch = store.dispatch;
  return (action) => {
    if (typeof action.then === 'function') {
      return action.then(rawDispatch);
    }
  };
}

if (process.env.NODE_ENV !== 'production') {
  store.dispatch = addLoggingToDispatch(store);
}

store.dipatch = addLoggingToDispatch(store);
```

## 17. The Middleware Chain

> While this method of extending the store works, it's not really great at re-override the public API and replace it with custom functions. 

```js
// configureStore.js

const addLoggingToDispatch = (store) => {
  return (next) => { 
    if (!console.group) {
      return next;
    }

    return (action) => { ... }
  }
}
```

> Middleware is a powerful system that lets us put custom behavior before action reaches the reducers. People use middleware for different purposes, such as login, analytics, error-handling, asynchronous counterflow, and more.

## 18. Applying Redux Middleware

> We will learn how to replace the middleware we wrote and the custom code we used to glue it together with the existing core and third party utilities.

## 19. Updating the State with the Fetched Data

> We will learn how moving the source of truth for the data to the server changes the state shape and the reducers in our app.

## 20. Refactoring the Reducers

> We will learn how to remove the duplication in our reducer files and how to keep the knowledge about the state shape colocated with the newly extracted reducers.

```js
// reducers/index.js

import byId, * as fromById from './byId';
import createList, * as fromList from './createList';

const listByFilter = combineReducers({
  all: createList('all'),
  active: createList('active'),
  completed: createList('completed'),
});

const todos = combineReudcers({
  byId,
  listByFilter,
});

export default todos;

export cosnt getVisibleTodos = (state, filter) => {
  const ids = fromList.getIds(state.listByFilter[filter]);
  return ids.map(id => fromById.getTodo(state.byId, id));
};
```

```js
// reducers/createList.js

const createList = (filter) => {
  return (state = [], action) => {
    if (action.filter !== filter) {
      return;
    }
    switch (action.type) {
      case 'RECEIVE_TODOS':
        return action.response.map(todo => todo.id);
      default:
        return state;
    }
  };
};

export default createList;

export const getIds = (state) => state;
```

```js
// reducers/byId.js

const byId = (state = {}, action) => {
  switch (action.type) {
    case 'RECEIVE_TODOS':
      const nextState = { ...state };
      action.response.forEach(todo => {
        nextState[todo.id] = todo;
      });
      return nextState;
    default:
      return state;
  }
};

export default byId;

export const getTodo = (state, id) => state[id];
```

> Redux does not enforce that you encapsulate the knowledge about the state shape in particular reducer files. However, it's a nice pattern, because it lets you change the state that is stored by reducers without having to change your components or your tests if you use selectors together with reducers in your tests.

## 21. Displaying Loading Indicators

> We will learn how to display the loading indicators while the data is being fetched.

```js
// reducers/createList.js

import { combineReducers } from 'redux';

const createList = (filter) => {
  const ids = (state = [], action) => {
    if (action.filter !== filter) {
      return state;
    }
    switch (action.type) {
      case 'RECEIVE_TODOS':
        return action.response.map(todo => todo.id);
      default:
        return state;
    }
  };

  const isFetching = (state = false, action) => {
    if (action.filter !== filter) {
      return state;
    }
    switch (action.type) {
      case 'REQUEST_TODOS':
        return true;
      case 'RECEIVE_TODOS':
        return false;
      default:
        return state;
    }
  };

  return combineReducers({
    ids,
    isFetching,
  });
};

export default createList;

export const getIds = (state) => state.ids;
export const getisFetching = (state) => state.isFetching;
```

```js
// reducers/index.js

import { combineReducers } from 'redux';
import byId, * as fromById from './byId';
import createList, * as fromList from './createList';

const listByFilter = combineReducers({
  all: createList('all'),
  active: createList('active'),
  completed: createList('completed'),
});

const todos = combineReducers({
  byId,
  listByFilter,
});

export default todos;

export const getVisibleTodos = (state, filter) => {
  const ids = fromList.getIds(state.listByFilter[filter]);
  return ids.map(id => fromById.getTodo(state.byId, id));
};

export const getIsFetching = (state, filter) => {
  fromList.getisFetching(state.listByFilter[filter]);
};
```

redux api인 `combineReducers`을 재귀적으로 사용하는 모습이 인상적이다.

## 22. Dispatching Actions Asynchronously with Thunks

> We will learn about “thunks”—the most common way to write async action creators in Redux.

```js
// actions/index.js

const requestTodos = (filter) => ({
  type: 'REQUEST_TODOS',
  filter,
});

export const receiveTodos = (filter) => ({
  type: 'RECEIVE_TODOS',
  filter,
  response,
});
```

> I want to dispatch requestTodos when they start fetching, and receiveTodos when they finish fetching, but the fetchTodos action creator only resolves through the receiveTodos action.

```js
// actions/index.js

export const fetchTodos = (filter) =>
  api.fetchTodos(filter).then(response =>
    receiveTodos(filter, response)
  );
```

> An action promise resolves through a single action at the end, but we want an abstraction that represents multiple actions dispatched over the period of time. This is why rather than return a promise, I want to return a function that accepts a dispatch callback argument.

> This lets me call dispatch as many times as I like at any point of time during the async operation. I can dispatch the requestTodos action in the beginning, and when the promise resolves, I can explicitly dispatch another receiveTodos action at the end.

```js
// actions/index.js

export const fetchTodos = (filter) => (dispatch) => {
  dispatch(requestTodos(filter));

  return api.fetchTodos(filter).then(response => {
    dispatch(receiveTodos(filter, response));
  });
}
```

> This means more typing than returning a promise, but it also gives me more flexibility. A promise can only express one async value, so fetchTodos now returns a function with a callback argument so that it can call it multiple times during the async operation.

> Such functions returned from other functions are often called thunks

> This is why when we see an action that is really a function, a thunk, we call it with store.dispatch as an argument so that it can dispatch other actions by itself. The store.dispatch function becomes available as the dispatch argument inside the thunk.

```js
// configureStore.js

cosnt configureStore = () => {
  const middlewares = [thunk];
  if (process.env.NODE_ENV !== 'production') {
    middlewares.push(creatLogger());
  }
}
```

> The thunk middleware is a powerful, composable way to express async action creators that want to emit several actions during the course of an async operation.

> This lets the components specify the intention to start an async operation without worrying which actions get dispatched and when.

## 23. Avoiding Race Conditions with Thunks

> We will learn how Redux Thunk middleware lets us conditionally dispatch actions to avoid unnecessary network requests and potential race conditions.

> We don't check if the tab is already loading before starting a request, and then a bunch of RECEIVE_TODOS action comes back, potentially resulting in a race condition.

```js
export const fetchTodos = (filter) => (dispatch, getState) => {
  if (getIsFetching(getState(), filter)) {
    return Promise.resolve();
  }
  // ...
};
```

> The thunk middleware itself does not use this promise, but it becomes the return value of dispatching this action creator, so I can use it inside the component to schedule some code after the asynchronous action has completed.

> Finally, the thunk middleware has no opinion on what you return from the thunk itself. As a convention, I prefer to always return a promise that represents the corresponding asynchronous operation, whether or not it has called the server.

> The return value of the thunk becomes the return value of dispatching this thunk, and I can use this to wait for the asynchronous operation to finish inside my component in order to show a message or start an animation.

## 24. Displaying Error Messages

> We will learn how to handle an error inside an async action, display its message in the user interface, and offer user an opportunity to retry the action.

> I can also remove the old action creators that I have aligned into it. If I change the action types, I also need to change the corresponding reducers ...

```js
// components/VisibleTodoList.js

const mapStateToProps = (state, { params }) => {
  const filter = params.filter || 'all';
  return {
    isFetching: getIsFetching(state, filter),
    errorMessage: getErrorMessage(state, filter),
    todos: getVisibleTodos(state, filter),
    filter,
  };
};
```

```js
// reducers/index.js

export const getIsFetching = (state, filter) =>
  fromList.getIsFetching(state.listByFilter[filter]);

export const getErrorMessage = (state, filter) =>
  fromList.getErrorMessage(state.listByFilter[filter]);
```

```js
// reducers/createList.js

export cosnt getErrorMessage = (state) => state.errorMessage;
```

> The getErrorMessage selector does not exist yet so I need to scroll up to where I import the selectors from the root reducers file, and I will add getErrorMessage alongside getIsFetching. Inside the root reducers file I'm copy/paste in the getIsFetching selector, and I'm changing the name to get error message. It will also delegate to a selector with a same name defined in the createList.js.

> I am passing the rejection handler as a second argument to the promise.then method. If the promise returned by the API gets rejected, this function will be called with the error as the argument.

> You might have seen a different way of handling promise errors in some examples where only one argument is passed and then you call catch on the resulting promise. The downside of this approach is if one of your reducers or subscribe components throws while handling this action you'll get into the catch block and so you'll display an internal error message to the user.

```js
// actions/index.js

return api.fetchTodos(filter).then(
  response => {
    dispatch({
      type: 'FETCH_TODOS_SUCCESS',
      filter,
      response,
    });
  },
).catch(
  error => {
    dispatch({
      type: 'FETCH_TODOS_FAILURE',
      filter,
      message: error.message || 'Something went wrong.',
    });
  }
)
```

> To avoid this error, I recommend that you don't use catch in this scenario and just pass the second argument so it catches only the errors from the underlying API promise. The error object usually comes with a message that we can wrap or display directly to the user with a fallback.

## 25. Creating Data on the Server

> We will learn how to wait until the item is created on the server, and update the corresponding local state.

## 26. Normalizing API Responses with normalizr

> We will learn how to use normalizr to convert all API responses to a normalized format so that we can simplify the reducers.

```js
// reducers/byId.js

const byId = (state = {}, action) => {
  switch (action.type) {
    case 'FETCH_TODOS_SUCCESS':
      action.response.forEach(todo => {
        nextState[todo.id] = todo;
      });
    return nextState;
    case 'ADD_TODO_SUCCESS':
      return {
        ...state,
        [action.response.id]: action.response,
      };
    default:
      return state;
  }
}
```

> In the byId reducer, I currently have to handle different server actions in a different way, because they have different response shape. For example, the FETCH_TODOS_SUCCESS action has a response, which is an array of todos.

> Instead of adding new cases for every new API call, I want to normalize the responses so the response shape is always the same.

> It contains two fields called entities and result. Entities contains a normalized dictionary called todos that contains every todo in the response by its id. Normalizr found these todo objects in the response by following the array of todos schema.

> The second field is the result. It's an array of todo ids. They are in the same order as the todos in the original response array. However, Normalizr replaced each todo with its id, and moved every todo into the todos dictionary.

## 27. Updating Data on the Server

> We will learn how to wait until the item is updated on the server, and then update the corresponding local state.

> This is the benefit of normalizing the API responses. Updates to the entities get merged automatically. 