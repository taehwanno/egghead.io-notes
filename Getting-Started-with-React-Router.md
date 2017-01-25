# [Getting Started with React Router](https://egghead.io/courses/getting-started-with-react-router)

- [x] 1. Development Environment Setup
- [x] 2. Router, Route, and Link
- [x] 3. hashHistory vs browserHistory
- [x] 4. activeStyle & activeClassName
- [x] 5. Nested Routes
- [x] 6. indexRoute
- [x] 7. Route Parameters
- [x] 8. Named Components
- [x] 9. Querystring Parameters
- [x] 10. Redirect
- [x] 11. setRouteWillLeaveHook

# [1. Development Environment Setup](https://egghead.io/lessons/react-react-router-development-environment-setup?course=getting-started-with-react-router)

> In this lesson we'll install our dependencies and setup a simple build process for converting our ES6 into ES5 using Babel and Webpack for the rest of the react-router lessons.

# [2. Router, Route, and Link](https://egghead.io/lessons/react-react-router-router-route-and-link)

> In this lesson we'll take our first look at the most common components available to us in react-router; Router, Route, and Link.

# [3. hashHistory vs browserHistory](https://egghead.io/lessons/react-react-router-hashhistory-vs-browserhistory)

> In this lesson we'll look at hashHistory which uses a hash hack to track our route changes vs browserHistory which delivers clean urls, but requires server work to implement

> If I reload over here, we can see that, rather than getting the hashtag path, we'll just get a very clean /about and /contact. In order to use this, though, you're going to need to set up your server to always return your application at any route.

> Generally speaking, that means in an express application, you're just going to return index HTML every single time a URL is called. Then the single page application will take care of the routing.

# [4. activeStyle & activeClassName](https://egghead.io/lessons/react-react-router-activestyle-activeclassname)

> react-router provides two props for setting a specific style on a Link component whose path matches our current route. activeStyle allows for an inline style while activeClassName allows for an class defined in an external stylesheet.

# [5. Nested Routes](https://egghead.io/lessons/react-react-router-nested-routes)

> Since react-router routes are components, creating nested routes is as simple as making one route a child of another in JSX. In this lesson we'll explore nesting routes and accessing the children of parent routes.

# [6. IndexRoute](https://egghead.io/lessons/react-react-router-indexroute)

> IndexRoute allows us to define a default child component to be rendered at a specific route when no other sub-route is available.

대응되는 자식 라우트가 존재하지 않을 때 기본적으로 보여줄 라우트를 설정할 때 `IndexRoute`를 사용한다.

# [7. Router Parameters](https://egghead.io/lessons/angularjs-react-router-route-parameters)

> A router library is no good if we have to hardcode every single route in our application. In this lesson we look at how to access variables in our routes and pass them into our components.

```jsx
import React from 'react';
import { Router, Route, Link, hashHistory } from 'react-router';

const Message = (props) =>
  <div><h1>{props.params.message || 'Hello'}</h1><Links /></div>;

const Links = () =>
  <nav>
    <Link to="/">Hello</Link>
    <Link to="/Hi">Hi</Link>
    <Link to="/Yo">Yo</Link>
  </nav>;

class App extends React.Component {
  render() {
    return (
      <Router history={hashHistory}>
        <Route path="/(:message)" component={Message} />
      </Router>
    );
  }
}

export default App;
```

`<Route path="(:message)" component={Message} />`의 `path`에서 볼 수 있듯이 `:message`의 경우 변수를 의미하며 `()` 괄호의 경우 필수가 아닌 optional variable을 의미한다.

# [8. Named Components](https://egghead.io/lessons/react-react-router-named-components)

> In this lesson we'll learn how to render multiple component children from a single route. 

```jsx
import React from 'react';
import { Router, Route, Link, IndexRoute, hashHistory } from 'react-router';

const Home = () => <h1>Home</h1>;
const HomeBody = () => <div>this is the home body</div>;
const Other = () => <h1>Other</h1>;
const OtherBody = () => <div>this is the Other body</div>;

const Container = (props) =>
  <div>{props.header}{props.body}<Links /></div>

const Links = () =>
  <nav>
    <Link to="/">Home</Link>
    <Link to="/other">Other</Link>
  </nav>;

class App extends React.Component {
  render() {
    return (
      <Router history={hashHistory}>
        <Route path="/" component={Container}>
          <IndexRoute components={{ header: Home, body: HomeBody }} />
          <Route path="/other" components={{ header: Other, body: OtherBody }} />
        </Route>
      </Router>
    );
  }
}

export default App;
```

# [9. Querystring Parameters](https://egghead.io/lessons/react-react-router-querystring-parameters)

> In this lesson we'll extract props for our component from a querystring in the route url.

```jsx
import React from 'react';
import { Router, Route, Link, hashHistory } from 'react-router';

const Page = (props) =>
  <div><h1>{props.location.query.message || 'Hello'}</h1><Links /></div>;

const Links = () =>
  <nav>
    <Link to={{ pathname: '/', query: { message: 'Yo' } }} />
  </nav>;

class App extends React.Component {
  render() {
    return (
      <Router history={hashHistory}>
        <Route path="/" component={Page} />
      </Router>
    );
  }
}

export default App;
```

# [10. Redirect](https://egghead.io/lessons/react-react-router-redirect)

> The Redirect component in react-router does exactly what it sounds like. It allows us to redirect from from one route to another.

```jsx
import React from 'react';
import {
  Router,
  Route,
  Link,
  IndexRoute,
  Redirect,
  hashHistory,
} from 'react-router';

const Home = () => <h1>Home</h1>;
const NewPage = (props) =>  <div><h1>New Page {props.params.id}</h1></div>;
const Container = (props) => <div><Links />{props.children}</div>;

const Links = () =>
  <nav>
    <Link to="/">Home</Link>
    <Link to="/old/oldId">Old</Link>
    <Link to="/new/newId">New</Link>
  </nav>;

class App extends React.Component {
  render() {
    return (
      <Router history={hashHistory}>
        <Route path="/" component={Container}>
          <IndexRoute component={Home} />
          <Route path="/new/:id" component={NewPage} />
          <Redirect from='/old/:id' to='/new/:id' />
        </Route>
      </Router>
    );
  }
}

export default App;
```

# [11. setRouteWillLeaveHook](https://egghead.io/lessons/react-react-router-setroutewillleavehook)

> setRouteWillLeaveHook provides a method for us to intercept a route change before leaving the current route.

```jsx
import React from 'react';
import { Router, Route, Link, hashHistory } from 'react-router';

// const Home = () => <div><h1>Home</h1><Links /></div>;

class Home extends React.Component {
  componentWillMount(){
    this.context.router.setRouteLeaveHook(
      this.props.route,
      this.routerWillLeave
    )
  }

  routerWillLeave( nextLocation ){
    return `leaving home for ${nextLocation.pathname}`
  }

  render() {
    return <div><h1>Home</h1><Links /></div>;
  }
}

Home.contextTypes = { router: React.PropTypes.object.isRequired };

const About = () => <div><h1>About</h1><Links /></div>;
const Links = () => {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="about">About</Link>
    </nav>
  )
};

const App = () => {
  return (
    <Router history={ hashHistory }>
      <Route path="/" component={Home} />
      <Route path="/about" component={About} />
    </Router>
  )
};

export default App;
```

`this.context.router.setRouteLeaveHook`에서 2번째의 콜백의 경우 `return` 값이 `string`일 경우 `alert`가 발생한다.

[react-router API#setrouteleavehookroute-hook](https://github.com/ReactTraining/react-router/blob/master/docs/API.md#setrouteleavehookroute-hook) 참고
