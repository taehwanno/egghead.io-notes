# [Advanced React Component Patterns](https://egghead.io/courses/advanced-react-component-patterns)

- [x] 1.Introducing Advanced React Component Patterns
- [x] 2. Build a Toggle Component
- [x] 3. Write Compound Components
- [x] 4. Make Compound React Components Flexible
- [x] 5. Make Enhanced React Components with Higher Order Components
- [x] 6. Handle props namespace clashes with Higher Order Components
- [x] 7. Improve debuggability of Higher Order Components
- [x] 8. Handle ref props with Higher Order Components
- [x] 9. Improve Unit Testability of Higher Order Components
- [x] 10. Handle static properties properly with Higher Order Components
- [x] 11. Use Render Props with React
- [x] 12. Use Prop Collections with Render Props
- [x] 13. Use Prop Getters with Render Props
- [x] 14. Use Component State Initializers
- [x] 15. Make Controlled React Components with Control Props
- [x] 16. Implement a React Context Provider
- [x] 17. Implement a Higher Order Component with Render Props
- [x] 18. Renderer Descendants Through shouldComponentUpdate
- [x] 19. Use Redux with Render Props

## 1. Introducing Advanced React Component Patterns

## 2. Build a Toggle Component

> In this lesson, we’ll write a simple toggle component which will serve as the foundation for the rest of the course. Kent walks through how to create a simple toggle with React setState. He shows us how to extend the functionality of the toggle by safely adding an onToggle prop that gets called after state is updated. It is important to define default props when you are calling a method from props.

```jsx
class Toggle extends React.Component {
  static defaultProps = { onToggle: () => {} }
  state = { on: false }
  toggle = () =>
    this.setState(({ on }) => ({ on: !on })),
    () => { this.props.onToggle(); }

  render() {
    const { on } = this.state;
    return (
      <Switch on={on} onClick={this.toggle} />
    );
  }
}
```

```jsx
function App() {
  return (
    <Toggle
      onToggle={on => console.log('toggle', on)}
    />
  )
}
```

## 3. Write Compound Components

> Compound component gives more rendering control to the user. The functionality of the component stays intact while how it looks and the order of the children can be changed at will. We get this functionality by using the special React.Children.map function to map over the children given to our <Toggle/> component. We map over the children to pass the on state as a prop to its children. We move the visual pieces of the component out into function components and add them as static properties to <Toggle/>.

```jsx
function ToggleOn({ on, children }) {
  return on ? children : null;
}

function ToggleOff({ on, children }) {
  return on ? null : children;
}

function ToggleButton({ on, toggle, ...props }) {
  return <Switch on={on} onClick={toggle} {...props} />
}

class Toggle extends React.Component {
  static On = ToggleOn
  static Off = ToggleOff
  static Button = ToggleButton
  static defaultProps = { onToggle: () => {} }
  state = { on: false }
  toggle = () =>
    this.setState(({ on }) => ({ on: !on })),
    () => { this.props.onToggle(); }

  render() {
    const children = React.Children.map(
      this.props.children,
      child =>
        React.cloneElement(child, {
          on: this.state.on,
          toggle: this.toggle,
        }),
    );
    return <div>{children}</div>;
  }
}
```

## 4. Make Compound React Components Flexible

> Our current compound component implementation is great, but it's limited in that users cannot render the structure they need. Let's allow the user to have more flexibility by using React context to share the implicit state to our child <Toggle/> components. We will walk through safely defining childContextTypes, providing the value with getChildContext, and, on each of the components that need that context, we define contextTypes so that React will pass the context that is being asked for.

```jsx
const TOGGLE_CONTEXT = '__toggle__';

function ToggleOn({ children }, context) {
  const { on } = context[TOGGLE_CONTEXT];
  return on ? children : null;
}
ToggleOn.contextTypes = {
  [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
};

function ToggleOff({ children }, context) {
  const { on } = context[TOGGLE_CONTEXT];
  return on ? null : children;
}
ToggleOff.contextTypes = {
  [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
};

function ToggleButton(props, context) {
  const { on, toggle } = context[TOGGLE_CONTEXT];
  return <Switch on={on} onClick={toggle} {...props} />
}
ToggleButton.contextTypes = {
  [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
};

class Toggle extends React.Component {
  static On = ToggleOn;
  static Off = ToggleOff;
  static Button = ToggleButton;
  static defaultProps = { onToggle: () => {} };
  static childContextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  state = { on: false }
  toggle = () =>
    this.setState(({ on }) => ({ on: !on })),
    () => { this.props.onToggle(); }
  getChildContext() {
    return {
      [TOGGLE_CONTEXT]: {
        on: this.state.on,
        toggle: this.toggle,
      };
    };
  }
  render() {
    return <div>{this.props.children}</div>;
  }
}
```

## 5. Make Enhanced React Components with Higher Order Components

> The state shared between our toggle components is implicit because it lives inside context and is not easily accessible. In this lesson, we'll make a Higher Order Component (HOC) factory function which will allow the user to access that implicit state in their own components. Higher Order Components take a component and render a new component with some enhanced behaviors, in this case, the toggle context is added to the returned component. This factory component can get rid of duplicated code caused by needing to declare contextTypes on each component that needs it. Now we can wrap the component with withToggle and destructure on and toggle from the props.

```jsx
const TOGGLE_CONTEXT = '__toggle__';

const ToggleOn = withToggle(({ children, on }) => {
  return on ? children : null;
});

const ToggleOff = withToggle(({ children, on }) => {
  return on ? null : children;
});

const ToggleButton = withToggle(
  ({ on, toggle, ..props }) => {
    return (
      <Switch
        on={on}
        onClick={toggle}
        {...props}
      />
    );
  });

function withToggle(Component) {
  function Wrapper(props, context) {
    const toggleContext = context[TOGGLE_CONTEXT];
    return <Component {...toggleContext} {...props} />
  }
  Wrapper.contextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  return Wrapper;
}

const MyToggle = withToggle(({ on, toggle }) => (
  <button onClick={toggle}>
    {on ? 'on' : 'off'}
  </button>
));

class Toggle extends React.Component {
  static On = ToggleOn;
  static Off = ToggleOff;
  static Button = ToggleButton;
  static defaultProps = { onToggle: () => {} };
  static childContextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  state = { on: false }
  toggle = () =>
    this.setState(({ on }) => ({ on: !on })),
    () => { this.props.onToggle(); }
  getChildContext() {
    return {
      [TOGGLE_CONTEXT]: {
        on: this.state.on,
        toggle: this.toggle,
      };
    };
  }
  render() {
    return <div>{this.props.children}</div>;
  }
}
```

## 6. Handle props namespace clashes with Higher Order Components

> One problem Higher Order Components face is they share the props namespace with the other props the user wants to pass to that component. In this lesson, we’ll take a look at how we can resolve these naming conflicts.

```jsx
const TOGGLE_CONTEXT = '__toggle__';

const ToggleOn = withToggle({ children, toggle: { on } }) => {
  return on ? children : null;
});

const ToggleOff = withToggle({ children, toggle: { on }) => {
  return on ? null : children;
});

const ToggleButton = withToggle(
  ({ toggle: { on, toggle }, ..props }) => {
    return (
      <Switch
        on={on}
        onClick={toggle}
        {...props}
      />
    );
  });

function withToggle(Component) {
  function Wrapper(props, context) {
    const toggleContext = context[TOGGLE_CONTEXT];
    return <Component {...props} toggle={toggleContext} />
  }
  Wrapper.contextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  return Wrapper;
}

const MyToggle = withToggle(({ toggle: { on, toggle } }) => (
  <button onClick={toggle}>
    {on ? 'on' : 'off'}
  </button>
));

class Toggle extends React.Component {
  static On = ToggleOn;
  static Off = ToggleOff;
  static Button = ToggleButton;
  static defaultProps = { onToggle: () => {} };
  static childContextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  state = { on: false }
  toggle = () =>
    this.setState(({ on }) => ({ on: !on })),
    () => { this.props.onToggle(); }
  getChildContext() {
    return {
      [TOGGLE_CONTEXT]: {
        on: this.state.on,
        toggle: this.toggle,
      };
    };
  }
  render() {
    return <div>{this.props.children}</div>;
  }
}
```

## 7. Improve debuggability of Higher Order Components

> React DevTools and React's built-in development warnings rely on the displayName and name property of your react components to give you a good developer experience. Unfortunately, Higher Order Components make it harder for React to give that good experience. In this lesson, we’ll learn about the displayName property and how that impacts React Developer Tools. Then we'll improve the displayName for our Higher Order Component by changing where we call the withToggle Higher Order Component.

```jsx
function withToggle(Component) {
  function Wrapper(props, context) {
    const toggleContext = context[TOGGLE_CONTEXT];
    return <Component {...props} toggle={toggleContext} />;
  }

  Wrapper.contextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  Wrapper.displayName = `withToggle(${Component.displayName || Component.name})`;

  return Wrapper;
}
```

```jsx
const TOGGLE_CONTEXT = '__toggle__';

const ToggleOn = { children, toggle: { on } }) => on ? children : null;

const ToggleOff = ({ children, toggle: { on }) => on ? null : children;

const ToggleButton = ({ toggle: { on, toggle }, ..props }) => <Switch on={on} onClick={toggle} {...props} />;

class Toggle extends React.Component {
  static On = withToggle(ToggleOn);
  static Off = withToggle(ToggleOff);
  static Button = withToggle(ToggleButton);
  static defaultProps = { onToggle: () => {} };
  static childContextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  state = { on: false }
  toggle = () =>
    this.setState(({ on }) => ({ on: !on })),
    () => { this.props.onToggle(); }
  getChildContext() {
    return {
      [TOGGLE_CONTEXT]: {
        on: this.state.on,
        toggle: this.toggle,
      };
    };
  }
  render() {
    return <div>{this.props.children}</div>;
  }
}
```

## 8. Handle ref props with Higher Order Components

> When you wrap a component with a Higher Order Component, it becomes impossible to forward the ref prop down the component tree due to the special nature of the ref prop in React. In this lesson, we’ll see that problem in action with our Higher Order Component. We will pass the ref as an innerRef prop so that the Higher Order Component can re-assign the ref to the component that it is wrapping.

```jsx
function withToggle(Component) {
  function Wrapper({ innerRef, ...props }, context) {
    const toggleContext = context[TOGGLE_CONTEXT];
    return (
      <Component
        {...props}
        ref={innerRef}
        toggle={toggleContext}
      />
    );
  }

  Wrapper.contextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  Wrapper.displayName = `withToggle(${Component.displayName || Component.name})`;

  return Wrapper;
}
```

## 9. Improve Unit Testability of Higher Order Components

> Let’s make it a little easier to test components which have been wrapped by our Higher Order Component by exposing the underlying component. Inside of our Higher Order Component, we will assign a WrappedComponent property to our Component that we passed in.

```jsx
function withToggle(Component) {
  function Wrapper({ innerRef, ...props }, context) {
    const toggleContext = context[TOGGLE_CONTEXT];
    return (
      <Component
        {...props}
        ref={innerRef}
        toggle={toggleContext}
      />
    );
  }

  Wrapper.contextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  Wrapper.displayName = `withToggle(${Component.displayName || Component.name})`;
  Wrapper.WrappedComponent = Component;

  return Wrapper;
}
```

## 10. Handle static properties properly with Higher Order Components

```jsx
function withToggle(Component) {
  function Wrapper({ innerRef, ...props }, context) {
    const toggleContext = context[TOGGLE_CONTEXT];
    return (
      <Component
        {...props}
        ref={innerRef}
        toggle={toggleContext}
      />
    );
  }

  Wrapper.contextTypes = {
    [TOGGLE_CONTEXT]: PropTypes.object.isRequired,
  };
  Wrapper.displayName = `withToggle(${Component.displayName || Component.name})`;
  Wrapper.WrappedComponent = Component;

  return hoistNonReactStatics(Wrapper, Component);
}
```

## 11. Use Render Props with React

> In this lesson, we'll take a step back and reexamine the problem by doing a few tiny refactorings to arrive at a new pattern called render props. render props is just a function that you pass to a component that renders jsx. This pattern gives us the ability to use Reacts lifecycle methods where as with Higher Order Components, you could not. We'll see that it allows us to avoid using APIs like context and all of the issues we had to deal with when using Higher Order Components.

```jsx
class Toggle extends React.Component {
  static defaultProps = { onToggle: () => {} }
  state = { on: false }
  toggle = () =>
    this.setState(
      ({ on }) => ({ on: !on }),
      () => this.props.onToggle(this.state.on),
    )

  render() {
    return this.props.render({
      on: this.state.on,
      toggle: this.toggle,
    });
  }
}

function App() {
  return (
    <Toggle
      onToggle={on => console.log('toggle', on)}
      render={({ on, toggle }) => (
        <div>
          <Switch on={on} onClick={toggle} />
          <div>{on ? 'on' : 'off'}</div>
        </div>
      ))}
    />
  );
}
```

- Higher Order Component's Problems
  - displayName, Wrapped component, the React statics, the ref
  - Naming Collision
  - Difficulty in typing with TypeScript or Flow
- `HoC` vs `render prop`
  - Both composition mechanism
  - `HoC` composition takes place statically during the construction phase of the application.
  - `render props` composition happens right within React's normal composition model, during the render phase.
    - This means that I can take advantage of React's lifecycle and the natural flow of props and state because the composition model is dynamic.

## 12. Use Prop Collections with Render Props

> Sometimes you have common use cases that require common props to be applied to certain elements. You can collect these props into an object for users to simply apply to their elements and we'll see how to do that in this lesson.

```jsx
class Toggle extends React.Component {
  static defaultProps = { onToggle: () => {} }
  state = { on: false }
  toggle = () =>
    this.setState(
      ({ on }) => ({ on: !on }),
      () => this.props.onToggle(this.state.on),
    )

  render() {
    return this.props.render({
      on: this.state.on,
      toggle: this.toggle,
      togglerProps: {
        'aria-expaneded': this.state.on,
        onClick: this.toggle,
      },
    });
  }
}

function App() {
  return (
    <Toggle
      onToggle={on => console.log('toggle', on)}
      render={({ on, toggle, togglerProps }) => (
        <div>
          <Switch
            on={on}
            {...togglerProps}
          />
          <hr />
          <button {...togglerProps}>
            {on ? 'on' : 'off'}
          </button>
        </div>
      ))}
    />
  );
}
```

## 13. Use Prop Getters with Render Props

> When you're using prop collections, sometimes you can run into trouble with exposing implementation details of your prop collections. In this lesson, we'll see how we can abstract that away by simply creating a function called a prop getter that will compose the user's props with our prop collection.

```jsx
const compose = (...fns) => (...args) => fns.forEach(fn => fn && fn(...args))
class Toggle extends React.Component {
  static defaultProps = { onToggle: () => {} }
  state = { on: false }
  toggle = () =>
    this.setState(
      ({ on }) => ({ on: !on }),
      () => this.props.onToggle(this.state.on),
    )
  getTogglerProps = ({ onClick, ...props } = {}) => {
    return {
      'aria-expaneded': this.state.on,
      onClick: compose(onClick, this.toggle),
      ...props,
    };
  }
  render() {
    return this.props.render({
      on: this.state.on,
      toggle: this.toggle,
      getTogglerProps: this.getTogglerProps,
    });
  }
}

function App() {
  return (
    <Toggle
      onToggle={on => console.log('toggle', on)}
      render={({ on, toggle, getTogglerProps }) => (
        <div>
          <Switch
            on={on}
            {...getTogglerProps()}
          />
          <hr />
          <button
            {...getTogglerProps({
              onClick: () => alert('hi'),
              id: 'hi,
            })}
          >
            {on ? 'on' : 'off'}
          </button>
        </div>
      ))}
    />
  );
}
```

## 14. Use Component State Initializers

> In this lesson, we'll allow users to initialize our component's state with their own defaults and support a reset functionality to reset to the initial state.

```jsx
const compose = (...fns) => (...args) => fns.forEach(fn => fn && fn(...args))
class Toggle extends React.Component {
  static defaultProps = {
    defaultOn: false,
    onToggle: () => {},
    onReset: () => {},
  }
  initialState = { on: this.props.defaultOn }
  state = this.initialState
  reset = () => {
    this.setState(
      this.initialState,
      () => {
        this.props.onReset(this.state.on);
      },
    );
  }
  toggle = () =>
    this.setState(
      ({ on }) => ({ on: !on }),
      () => this.props.onToggle(this.state.on),
    )
  getTogglerProps = ({ onClick, ...props } = {}) => {
    return {
      'aria-expaneded': this.state.on,
      onClick: compose(onClick, this.toggle),
      ...props,
    };
  }
  render() {
    return this.props.render({
      on: this.state.on,
      toggle: this.toggle,
      getTogglerProps: this.getTogglerProps,
    });
  }
}

function App() {
  return (
    <Toggle
      defaultOn={true}
      onToggle={on => console.log('toggle', on)}
      onReset={on => console.log('reset', on)}
      render={toggle => (
        <div>
          <Switch
            {...toggle.getTogglerProps({
              on: toggle.on,
            })}
          />
          <hr />
          <button onClick={() => toggle.reset()}>
            Reset
          </button>
        </div>
      )}
    />
  );
}
```

## 15. Make Controlled React Components with Control Props

> Sometimes users of your component want to have more control over what the internal state is. In this lesson we'll learn how to allow users to control some of our component’s state just like the <input />'s value prop.

```jsx
const compose = (...fns) => (...args) => fns.forEach(fn => fn && fn(...args))
class Toggle extends React.Component {
  static defaultProps = {
    defaultOn: false,
    onToggle: () => {},
    onReset: () => {},
  }
  initialState = { on: this.props.defaultOn }
  state = this.initialState
  reset = () => {
    if (this.isOnControlled()) {
      this.props.onReset(!this.props.on);
    } else {
      this.setState(
        this.initialState,
        () => this.props.onReset(this.state.on),
      );
    }
  }
  toggle = () => {
    if (this.isOnControlled()) {
      this.props.onToggle(!this.props.on);
    } else {
      this.setState(
        ({ on }) => ({ on: !on }),
        () => this.props.onToggle(this.state.on),
      );
    }
  }
  getTogglerProps = ({ onClick, ...props } = {}) => {
    return {
      'aria-expaneded': this.state.on,
      onClick: compose(onClick, this.toggle),
      ...props,
    };
  }
  isOnControlled() {
    return this.props.on !== undefined;
  }
  render() {
    return this.props.render({
      on:
        this.isOnControlled()
          ? this.props.on
          : this.state.on,
      toggle: this.toggle,
      getTogglerProps: this.getTogglerProps,
    });
  }
}

function App() {
  return (
    <Toggle
      defaultOn={true}
      onToggle={on => console.log('toggle', on)}
      onReset={on => console.log('reset', on)}
      render={toggle => (
        <div>
          <Switch
            {...toggle.getTogglerProps({
              on: toggle.on,
            })}
          />
          <hr />
          <button onClick={() => toggle.reset()}>
            Reset
          </button>
        </div>
      )}
    />
  );
}
```

## 16. Implement a React Context Provider

> If you have state that needs to exist throughout your application, then you may find yourself passing props all over the application and even "drilling" the prop through components that don't really care about the prop at all. In this lesson, we'll see a sample of a small app that has the "prop drilling problem" and learn how to implement the "Provider pattern" to access context state anywhere in the component tree.

```jsx
const compose = (...fns) => (...args) =>
  fns.forEach(fn => fn && fn(...args))

class Toggle extends React.Component {
  static defaultProps = {
    defaultOn: false,
    onToggle: () => {},
    onReset: () => {},
  }
  initialState = {on: this.props.defaultOn}
  state = this.initialState
  reset = () => {
    if (this.isOnControlled()) {
      this.props.onReset(!this.props.on)
    } else {
      this.setState(this.initialState, () =>
        this.props.onReset(this.state.on),
      )
    }
  }
  toggle = () => {
    if (this.isOnControlled()) {
      this.props.onToggle(!this.props.on)
    } else {
      this.setState(
        ({on}) => ({on: !on}),
        () => this.props.onToggle(this.state.on),
      )
    }
  }
  getTogglerProps = (
    {onClick, ...props} = {},
  ) => ({
    onClick: compose(onClick, this.toggle),
    'aria-expanded': this.state.on,
    ...props,
  })
  isOnControlled() {
    return this.props.on !== undefined
  }
  render() {
    return this.props.render({
      on: this.isOnControlled()
        ? this.props.on
        : this.state.on,
      toggle: this.toggle,
      reset: this.reset,
      getTogglerProps: this.getTogglerProps,
    })
  }
}

class ToggleProvider extends React.Component {
  static contextName = '__toggle__'
  static Renderer = class extends React.Component {
    static childContextTypes = {
      [ToggleProvider.contextName]:
        PropTypes.object.isRequired,
    }
    getChildContext() {
      return {
        [ToggleProvider.contextName]: this.props
          .toggle,
      }
    }
    render() {
      return this.props.children
    }
  }
  render() {
    const {
      children,
      ...remainingProps
    } = this.props
    return (
      <Toggle
        {...remainingProps}
        render={toggle => (
          <ToggleProvider.Renderer
            toggle={toggle}
            children={children}
          />
        )}
      />
    )
  }
}

function ConnectedToggle(props, context) {
  return props.render(
    context[ToggleProvider.contextName],
  )
}
ConnectedToggle.contextTypes = {
  [ToggleProvider.contextName]:
    PropTypes.object.isRequired,
}

function Nav() {
  return (
    <ConnectedToggle
      render={toggle => (
        <nav style={{flex: 1}}>
          <ul
            style={{
              display: 'flex',
              justifyContent: 'space-around',
              listStyle: 'none',
              paddingLeft: '0',
            }}
          >
            <li>
              <a href="index.html">
                {toggle.on ? '🏡' : 'Home'}
              </a>
            </li>
            <li>
              <a href="/about/">
                {toggle.on ? '❓' : 'About'}
              </a>
            </li>
            <li>
              <a href="/blog/">
                {toggle.on ? '📖' : 'Blog'}
              </a>
            </li>
          </ul>
        </nav>
      )}
    />
  )
}

function NavSwitch() {
  return (
    <div
      style={{
        display: 'flex',
        flexDirection: 'column',
        justifyContent: 'center',
        textAlign: 'center',
      }}
    >
      <div>
        <ConnectedToggle
          render={toggle =>
            toggle.on ? '🦄' : 'Enable Emoji'}
        />
      </div>
      <ConnectedToggle
        render={toggle => (
          <Switch
            {...toggle.getTogglerProps({
              on: toggle.on,
            })}
          />
        )}
      />
    </div>
  )
}

function Header() {
  return (
    <div>
      <div
        style={{
          display: 'flex',
          justifyContent: 'space-around',
          alignItems: 'center',
        }}
      >
        <Nav />
        <NavSwitch />
      </div>
    </div>
  )
}

function Subtitle() {
  return (
    <ConnectedToggle
      render={toggle =>
        toggle.on
          ? '👩‍🏫 👉 🕶'
          : 'Teachers are awesome'}
    />
  )
}

function Title() {
  return (
    <div>
      <h1>
        <ConnectedToggle
          render={toggle =>
            `Who is ${toggle.on
              ? '🕶❓'
              : 'awesome?'}`}
        />
      </h1>
      <Subtitle />
    </div>
  )
}

function Article() {
  return (
    <div>
      <ConnectedToggle
        render={toggle =>
          [
            'Once, I was in',
            toggle.on ? '🏫‍' : 'school',
            'when I',
            toggle.on ? '🤔' : 'realized',
            'something...',
          ].join(' ')}
      />
      <hr />
      <ConnectedToggle
        render={toggle =>
          [
            'Without',
            toggle.on ? '👩‍🏫' : 'teachers',
            `I wouldn't know anything so`,
            toggle.on ? '🙏' : 'thanks',
            toggle.on ? '👩‍🏫❗️' : 'teachers!',
          ].join(' ')}
      />
    </div>
  )
}

function Post() {
  return (
    <div>
      <Title />
      <Article />
    </div>
  )
}

function App() {
  return (
    <ToggleProvider>
      <div>
        <Header />
        <Post />
      </div>
    </ToggleProvider>
  )
}
```

## 17. Implement a Higher Order Component with Render Props

> When making a reusable component, you'll find that people often like to have the API they're most familiar with, so in this lesson we'll recreate the withToggle Higher Order Component using our new ConnectedToggle render prop component.

```jsx
function withToggle(Component) {
  function Wrapper(props, context) {
    const { innerRef, ...remainingProps } = props;
    return (
      <ConnectedToggle
        render={toggle => (
          <Component
           {...remainingProps}
           toggle={toggle}
          />
        )}
      />
    )
  }

  Wrapper.displayName = `withToggle(${Component.displayName || Component.name})`
  Wrapper.propTypes = { innerRef: PropTypes.func };
  Wrapper.WrappedComponent = Component;
  return hoistNonReactStatics(Wrapper, Component);
}
```

## 18. Renderer Descendants Through shouldComponentUpdate

> One problem with using context is that any component in the hierarchy could return true from shouldComponentUpdate without knowing that state in context has changed. In this lesson, we’ll look at how the react-broadcast library can be used to avoid that problem.

```jsx
class ToggleProvider extends React.Component {
  static channel = '__toggle__channel__'

  render() {
    const {
      children,
      ...remainingProps
    } = this.props
    return (
      <Toggle
        {...remainingProps}
        render={toggle => (
          <ReactBroadcast.Broadcast
            channel={ToggleProvider.channel}
            value={toggle}
          >
          </ReactBroadcast.Broadcast>
        )}
      />
    )
  }
}

function ConnectedToggle(props, context) {
  return (
    <ReactBroadcast.Subscriber
      channel={ToggleProvider.channel}
    >
      {toggle => props.render(toggle)}
    </ReactBroadcast.Subscriber>
  );
}
```

## 19. Use Redux with Render Props

> Now that we have everything wired up to share and update the state of the toggled boolean across our application, let's see how easy it would be to change from the state of a single boolean to state in a redux store! We’ll call it rendex (render props + redux). 😍

```jsx
const compose = (...fns) => (...args) => fns.forEach(fn => fn && fn(...args))

class Rendux extends React.Component {
  static defaultProps = {
    initialState: {},
    onUpdate: () => {},
    onReset: () => {},
    reducer: state => state,
  }
  initialState = this.props.initialState
  rootReducer = (state, action) => {
    if (action.type === '__RENDUX_RESET__') {
      return this.initialState
    }
    return this.props.reducer(state, action)
  }
  store = Redux.createStore(
    this.rootReducer,
    this.initialState,
  )
  state = this.initialState
  reset = () => {
    if (this.isStateControlled()) {
      this.props.onReset(this.initialState)
    } else {
      this.store.dispatch({
        type: '__RENDUX_RESET__',
      })
    }
  }
  componentDidMount() {
    this.unsubscribe = this.store.subscribe(() =>
      this.setState(this.store.getState()),
    )
  }
  componentWillUnmount() {
    this.unsubscribe()
  }
  isStateControlled() {
    return this.props.state !== undefined
  }
  render() {
    return this.props.render({
      state: this.isStateControlled()
        ? this.props.state
        : this.store.getState(),
      dispatch: this.store.dispatch,
      reset: this.reset,
    })
  }
}

class RenduxProvider extends React.Component {
  static channel = '__rendux_channel__'
  render() {
    const {
      children,
      ...remainingProps
    } = this.props
    return (
      <Rendux
        {...remainingProps}
        render={rendux => (
          <ReactBroadcast.Broadcast
            channel={RenduxProvider.channel}
            value={rendux}
          >
            {children}
          </ReactBroadcast.Broadcast>
        )}
      />
    )
  }
}

function ConnectedRendux(props, context) {
  return (
    <ReactBroadcast.Subscriber
      channel={RenduxProvider.channel}
    >
      {rendux => props.render(rendux)}
    </ReactBroadcast.Subscriber>
  )
}

function withRendux(Component) {
  function Wrapper(props, context) {
    const {innerRef, ...remainingProps} = props
    return (
      <ConnectedRendux
        render={rendux => (
          <Component
            {...remainingProps}
            rendux={rendux}
            ref={innerRef}
          />
        )}
      />
    )
  }
  Wrapper.displayName = `withRendux(${Component.displayName ||
    Component.name})`
  Wrapper.propTypes = {innerRef: PropTypes.func}
  Wrapper.WrappedComponent = Component
  return hoistNonReactStatics(Wrapper, Component)
}

class UpdateBlocker extends React.Component {
  shouldComponentUpdate() {
    return false
  }
  render() {
    return this.props.children
  }
}

function MyInput() {
  return (
    <ConnectedRendux
      render={rendux => (
        <input
          defaultValue={
            rendux.state.on ? 'on' : 'off'
          }
          placeholder="Type 'off' or 'on'"
          onChange={event => {
            if (event.target.value === 'on') {
              rendux.dispatch({
                type: 'toggle',
                value: true,
              })
            } else if (
              event.target.value === 'off'
            ) {
              rendux.dispatch({
                type: 'toggle',
                value: false,
              })
            }
            rendux.dispatch({
              type: 'input_change',
              value: event.target.value,
            })
          }}
        />
      )}
    />
  )
}

function MySwitch() {
  return (
    <ConnectedRendux
      render={rendux => (
        <div
          style={{
            marginTop: 20,
            marginBottom: 20,
          }}
        >
          <Switch
            on={rendux.state.on}
            onClick={() =>
              rendux.dispatch({
                type: 'toggle',
                value: !rendux.state.on,
              })}
          />
        </div>
      )}
    />
  )
}

function StatePrinter() {
  return (
    <ConnectedRendux
      render={rendux => (
        <div style={{textAlign: 'left'}}>
          state:
          <pre>
            {JSON.stringify(
              rendux.state,
              null,
              2,
            )}
          </pre>
        </div>
      )}
    />
  )
}

function App() {
  return (
    <RenduxProvider
      initialState={{on: true}}
      reducer={(state, action) => {
        switch (action.type) {
          case 'toggle':
            return {
              ...state,
              on: action.value,
            }
          case 'input_change':
            return {
              ...state,
              inputValue: action.value,
            }
          default:
            return state
        }
      }}
    >
      <UpdateBlocker>
        <MyInput />
        <MySwitch />
        <StatePrinter />
      </UpdateBlocker>
    </RenduxProvider>
  )
}
```
