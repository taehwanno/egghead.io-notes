# [Manage Complex State in React Apps with MobX](https://egghead.io/courses/manage-complex-state-in-react-apps-with-mobx)

* [x] 1.  Sync the UI with the app state using MobX observable and observer in React
* [x] 2.  Analyze React components with MobX-React devtools
* [x] 3.  Derive computed values and manage side effects with MobX reactions
* [x] 4.  Use observable objects, arrays, and maps to store state in MobX
* [x] 5.  Use MobX actions to change and guard state
* [x] 6.  Pass observable data through props in MobX
* [x] 7.  Handle user input and asynchronous actions with MobX
* [x] 8.  Connect MobX observer components to the store with the React Provider
* [x] 9.  Write custom MobX reactions with when and autorun

## 1. Sync the UI with the app state using MobX observable and observer in React

> Applications are driven by state. Many things, like the user interface, should always be consistent with that state. MobX is a general purpose FRP library that provides the means to derive, for example, a React based user interface automatically from the state and keep it synchronized.

> The net result of this approach is that writing applications becomes really straight-forward and boilerplate free.

* `@observable` in React Component

```jsx
import { observable } from 'mobx';
import React from 'react';
import { observer } from 'react-mobx';

@observer
class Counter extends React.Component {
  @observable count = 0;

  handleDec = () => {
    this.count--;
  };

  handleInc = () => {
    this.count++;
  };

  render() {
    return (
      <div>
        Counter: {this.count}
        <br />
        <button onClick={this.handleDec}> - </button>
        <button onClick={this.handleInc}> + </button>
      </div>
    );
  }
}
```

* `observable` outside React Component passed to `store` props

```jsx
import { observable } from 'mobx';
import React from 'react';
import { observer } from 'react-mobx';

const appState = observable({
  count: 0,
});
appState.increment = function() {
  this.count++;
};
appState.decrement = function() {
  this.count--;
};

@observer
class Counter extends React.Component {
  handleInc = () => {
    this.props.store.increment();
  };

  handleDec = () => {
    this.props.store.decrement();
  };

  render() {
    return (
      <div>
        Count: {this.props.store.count} <br />
        <button onClick={this.handleDec}> - </button>
        <button onClick={this.handleInc}> + </button>
      </div>
    );
  }
}
```

* `observable` outside React Component passed to directly

```jsx
import { observable } from 'mobx';
import React from 'react';
import { observer } from 'react-mobx';

const appState = observable({
  count: 0,
});
appState.increment = function() {
  this.count++;
};
appState.decrement = function() {
  this.count--;
};

@observer
class Counter extends React.Component {
  handleInc = () => {
    appState.increment();
  };

  handleDec = () => {
    appState.decrement();
  };

  render() {
    return (
      <div>
        Count: {appState.count} <br />
        <button onClick={this.handleDec}> - </button>
        <button onClick={this.handleInc}> + </button>
      </div>
    );
  }
}
```

## 2. Analyze React components with MobX-React devtools

> The MobX React devtools can be used to analyze when and why React components are re-rendered. They offer three features: 1) visualize when components re-render, 2) show the dependency tree of a component, to see which data it's rendering depends upon, and 3) log in detail which data is changed by your actions, and how these changes propagate through the application.

```shell
$ npm install --save mobx-react-devtools
```

```diff
import React from 'react';
import { observer } from 'react-mobx';
import { observable } from 'mobx';
+import DevTools from 'mobx-react-devtools';

const appState = observable({
  count: 0,
});
appState.increment = function() {
  this.count++;
};
appState.decrement = function() {
  this.count--;
};

@observer
class Counter extends React.Component {
  handleInc = () => {
    this.props.store.increment();
  };

  handleDec = () => {
    this.props.store.decrement();
  };

  render() {
    return (
      <div>
+       <DevTools />
        Count: {this.props.store.count} <br />
        <button onClick={this.handleDec}> - </button>
        <button onClick={this.handleInc}> + </button>
      </div>
    );
  }
}
```

## 3. Derive computed values and manage side effects with MobX reactions

> Derivations form the backbone of MobX and come in two flavors: computed values are values that can be derived from the state automatically. And reactions can be used to manage side effects, such as drawing the user interface. In this lesson you will learn how these concepts relate to each other and how they are optimized automatically by MobX.

```jsx
import React from 'react';
import { observer } from 'react-mobx';
import { computed, observable } from 'mobx';
import DevTools from 'mobx-react-devtools';

const t = new class Temperature {
  @observable unit = 'C';
  @observable temperatureCelsius = 25;

  @computed
  get temperatureKelvin() {
    console.log('calculating Kelvin');
    return this.temperaturCelsius * (9 / 5) + 32;
  }

  @computed
  get temperatureFahrenheit() {
    console.log('calculating Fahrenheit');
    return this.temperatureCelsius + 273.15;
  }

  @computed
  get temperature() {
    console.log('calculating temperature');
    switch (this.unit) {
      case 'K':
        return this.temperatureKelvin + 'K';
      case 'F':
        return this.temperatureFahrenheit + 'F';
      case 'C':
        return this.temperatureCelsius + 'C';
    }
  }
}();

const App = observer(({ temperature }) => (
  <div>
    {temperature.temperature}
    <DevTools />
  </div>
));

ReactDOM.render(<App temperature={t} />, document.getElementById('app'));
```

## 4. Use observable objects, arrays, and maps to store state in MobX

> So far we have used the observable decorator extensively. Let's take a closer look on what it does and see how the extendObservable and observable functions can be used to create observable objects, arrays and maps.

* observable `Array`

```jsx
class Temperature {
  id = Math.random();
  @observable unit = 'C';
  @observable temperatureCelsius = 25;

  @computed
  get temperatureKelvin() {
    console.log('calculating Kelvin');
    return this.temperaturCelsius * (9 / 5) + 32;
  }

  @computed
  get temperatureFahrenheit() {
    console.log('calculating Fahrenheit');
    return this.temperatureCelsius + 273.15;
  }

  @computed
  get temperature() {
    console.log('calculating temperature');
    switch (this.unit) {
      case 'K':
        return this.temperatureKelvin + 'K';
      case 'F':
        return this.temperatureFahrenheit + 'F';
      case 'C':
        return this.temperatureCelsius + 'C';
    }
  }
}

const temps = observable([]);
temps.push(new Temperature());

const App = observer(({ temperatures }) => (
  <div>
    {temperatures.map(t => <div key={t.id}>{t.temperature}</div>)}
    <DevTools />
  </div>
));

ReactDOM.render(<App temperature={temps} />, document.getElementById('app'));
```

* observable `Map`

```jsx
const temps = observable([
  asMap({
    Amsterdam: new Temperature(),
    Rome: new Temperature(),
  }),
]);
temps.push(new Temperature());

const App = observer(({ temperatures }) => (
  <div>
    {temperatures.entries().map(([city, t]) => (
      <div key={t.id}>
        {city}: {t.temperature}
      </div>
    ))}
    <DevTools />
  </div>
));
```

## 5. Use MobX actions to change and guard state

> This lesson explains how actions can be used to control and modify the state of your application. They help you to structure your code base and integrate well with the MobX React Devtools. Actions automatically create transactions, which group changes together.

> Actions automatically create transactions, which group changes together

```jsx
import React from 'react';
import { observer } from 'react-mobx';
import { action, computed, observable, useStrict } from 'mobx';
import DevTools from 'mobx-react-devtools';

useStrict(true);

const t = new class Temperature {
  @observable unit = 'C';
  @observable temperatureCelsius = 25;

  @computed
  get temperatureKelvin() {
    console.log('calculating Kelvin');
    return this.temperaturCelsius * (9 / 5) + 32;
  }

  @computed
  get temperatureFahrenheit() {
    console.log('calculating Fahrenheit');
    return this.temperatureCelsius + 273.15;
  }

  @computed
  get temperature() {
    console.log('calculating temperature');
    switch (this.unit) {
      case 'K':
        return this.temperatureKelvin + 'K';
      case 'F':
        return this.temperatureFahrenheit + 'F';
      case 'C':
        return this.temperatureCelsius + 'C';
    }
  }

  @action
  setUnit(newUnit) {
    this.unit = newUnit;
  }

  @action
  setCelsius(degrees) {
    this.temperatureCelsius = degrees;
  }

  @action('update temperature and unit')
  setTemperatureAndUnit(degrees, unit) {
    this.setCelsius(degrees);
    this.setUnit(unit);
  }
}();

const App = observer(({ temperature }) => (
  <div>
    {temperature.temperature}
    <DevTools />
  </div>
));

ReactDOM.render(<App temperature={t} />, document.getElementById('app'));
```

## 6. Pass observable data through props in MobX

> This lesson explains how you can build larger trees of react component and how they are managed efficiently by MobX. You will learn how to pass observable data structures around in props and how the Devtools can be used to analyze the performance of your UI.

```jsx
import React from 'react';
import { observer } from 'react-mobx';
import { action, computed, observable, useStrict } from 'mobx';
import DevTools from 'mobx-react-devtools';

useStrict(true);

class Temperature {
  id = Math.random();
  @observable unit = 'C';
  @observable temperatureCelsius = 25;

  @computed
  get temperatureKelvin() {
    console.log('calculating Kelvin');
    return this.temperaturCelsius * (9 / 5) + 32;
  }

  @computed
  get temperatureFahrenheit() {
    console.log('calculating Fahrenheit');
    return this.temperatureCelsius + 273.15;
  }

  @computed
  get temperature() {
    console.log('calculating temperature');
    switch (this.unit) {
      case 'K':
        return this.temperatureKelvin + 'K';
      case 'F':
        return this.temperatureFahrenheit + 'F';
      case 'C':
        return this.temperatureCelsius + 'C';
    }
  }

  @action
  setUnit(newUnit) {
    this.unit = newUnit;
  }

  @action
  setCelsius(degrees) {
    this.temperatureCelsius = degrees;
  }

  @action('update temperature and unit')
  setTemperatureAndUnit(degrees, unit) {
    this.setCelsius(degrees);
    this.setUnit(unit);
  }

  @action
  inc() {
    this.setCelsius(this.temperatureCelsius + 1);
  }
};

const temps = observable([]);
temps.push(new Temperature(20, 'K'));
temps.push(new Temperature(25, 'F'));
temps.push(new Temperature(20, 'C'));

const App = observer(({ temperatures }) => (
  <div>
    {temperatures.map(t =>
      <TView key={t.id} temperature={t} />
    )}
    <DevTools />
  </div>
));

@observer
class TView extends React.Component {
  @action
  onTemperatureClick = () => {
    this.props.temperature.inc();
  }

  render() {
    const t = this.props.temperature;
    return <li key={t.id} onClick={this.onTemperatureClick}>{t.temperature}</li>;
  }
}

ReactDOM.render(<App temperatures={temps} />, document.getElementById('app'));
```

## 7. Handle user input and asynchronous actions with MobX

> In this lesson we will finally do something useful with our temperatures. We extend it into a small app in which users can enter a location, which is used as input for an external API to fetch weather data. In the mean time MobX keeps our UI in sync with the current state of the request.

```jsx
import React from 'react';
import { observer } from 'react-mobx';
import { action, computed, observable, useStrict } from 'mobx';
import DevTools from 'mobx-react-devtools';

useStrict(true);

class Temperature {
  id = Math.random();
  @observable unit = 'C';
  @observable temperatureCelsius = 25;
  @observable location = 'Amsterdam, NL';
  @observable loading = true;

  constructor(location) {
    this.location = location;
    this.fetch();
  }

  @action
  fetch() {
    window.fetch(`http://api.openweathermap.org/data/2.5/weather?appid=${APPID}&q=${this.location}`)
      .then(res => res.json())
      .then(action(json => {
        this.temperatureCelsius = json.main.temp - 273.15;
        this.loading = false;
      }));
  }

  @computed
  get temperatureKelvin() {
    console.log('calculating Kelvin');
    return this.temperaturCelsius * (9 / 5) + 32;
  }

  @computed
  get temperatureFahrenheit() {
    console.log('calculating Fahrenheit');
    return this.temperatureCelsius + 273.15;
  }

  @computed
  get temperature() {
    console.log('calculating temperature');
    switch (this.unit) {
      case 'K':
        return this.temperatureKelvin + 'K';
      case 'F':
        return this.temperatureFahrenheit + 'F';
      case 'C':
        return this.temperatureCelsius + 'C';
    }
  }

  @action
  setUnit(newUnit) {
    this.unit = newUnit;
  }

  @action
  setCelsius(degrees) {
    this.temperatureCelsius = degrees;
  }

  @action('update temperature and unit')
  setTemperatureAndUnit(degrees, unit) {
    this.setCelsius(degrees);
    this.setUnit(unit);
  }

  @action
  inc() {
    this.setCelsius(this.temperatureCelsius + 1);
  }
};

const temps = observable([]);

const App = observer(({ temperatures }) => (
  <div>
    <TemperatureInput temperatures={temperatures} />
    {temperatures.map(t =>
      <TView key={t.id} temperature={t} />
    )}
    <DevTools />
  </div>
));

@observer
class TemperatureInput extends React.Component {
  @observable input = '';

  @action
  onChange = (e) => {
    this.input = e.target.value;
  }

  @action
  onSubmit = () => {
    this.props.temperatures.push(new Temperature(this.input));
    this.input = '';
  }

  render() {
    return (
      <li>
        Destination <input type="text" value={this.input} onChange={this.onChange} />
        <button onClick={onSubmit}>Add</button>
      </li>
    )
  }
}

@observer
class TView extends React.Component {
  @action
  onTemperatureClick = () => {
    this.props.temperature.inc();
  }

  render() {
    const t = this.props.temperature;
    return (
      <li
        key={t.id}
        onClick={this.onTemperatureClick}
      >
        {t.location}:{t.temperature}
        {t.loading ? 'loading..' : t.temperature}
      </li>
    );
  }
}

ReactDOM.render(<App temperatures={temps} />, document.getElementById('app'));
```

## 8. Connect MobX observer components to the store with the React Provider

> Passing down stores through each layer of components can be a tedious job. This lesson demonstrates how Provider can be used to automatically connect observer based components to stores, by leveraging React's context mechanism in the background.

```diff
import React from 'react';
-import { observer } from 'react-mobx';
+import { observer, Provider } from 'react-mobx';
import { action, computed, observable, useStrict } from 'mobx';
import DevTools from 'mobx-react-devtools';

useStrict(true);

class Temperature {
  id = Math.random();
  @observable unit = 'C';
  @observable temperatureCelsius = 25;
  @observable location = 'Amsterdam, NL';
  @observable loading = true;

  constructor(location) {
    this.location = location;
    this.fetch();
  }

  @action
  fetch() {
    window.fetch(`http://api.openweathermap.org/data/2.5/weather?appid=${APPID}&q=${this.location}`)
      .then(res => res.json())
      .then(action(json => {
        this.temperatureCelsius = json.main.temp - 273.15;
        this.loading = false;
      }));
  }

  @computed
  get temperatureKelvin() {
    console.log('calculating Kelvin');
    return this.temperaturCelsius * (9 / 5) + 32;
  }

  @computed
  get temperatureFahrenheit() {
    console.log('calculating Fahrenheit');
    return this.temperatureCelsius + 273.15;
  }

  @computed
  get temperature() {
    console.log('calculating temperature');
    switch (this.unit) {
      case 'K':
        return this.temperatureKelvin + 'K';
      case 'F':
        return this.temperatureFahrenheit + 'F';
      case 'C':
        return this.temperatureCelsius + 'C';
    }
  }

  @action
  setUnit(newUnit) {
    this.unit = newUnit;
  }

  @action
  setCelsius(degrees) {
    this.temperatureCelsius = degrees;
  }

  @action('update temperature and unit')
  setTemperatureAndUnit(degrees, unit) {
    this.setCelsius(degrees);
    this.setUnit(unit);
  }

  @action
  inc() {
    this.setCelsius(this.temperatureCelsius + 1);
  }
};

const temps = observable([]);

const App = observer(
+  ['temperatures'],
  ({ temperatures }) => (
    <div>
-      <TemperatureInput temperatures={temperatures} />
+      <TemperatureInput />
      {temperatures.map(t =>
        <TView key={t.id} temperature={t} />
      )}
      <DevTools />
    </div>
  ));

+@observer(['temperatures'])
class TemperatureInput extends React.Component {
  @observable input = '';

  @action
  onChange = (e) => {
    this.input = e.target.value;
  }

  @action
  onSubmit = () => {
    this.props.temperatures.push(new Temperature(this.input));
    this.input = '';
  }

  render() {
    return (
      <li>
        Destination <input type="text" value={this.input} onChange={this.onChange} />
        <button onClick={onSubmit}>Add</button>
      </li>
    )
  }
}

@observer
class TView extends React.Component {
  @action
  onTemperatureClick = () => {
    this.props.temperature.inc();
  }

  render() {
    const t = this.props.temperature;
    return (
      <li
        key={t.id}
        onClick={this.onTemperatureClick}
      >
        {t.location}:{t.temperature}
        {t.loading ? 'loading..' : t.temperature}
      </li>
    );
  }
}

+const temps = observable([]);

ReactDOM.render(
- <App temperatures={temps} />
+  <Provider temperatures={temps}>
+    <App />
+  </Provider>,
  document.getElementById('app'),
);
```

## 9. Write custom MobX reactions with when and autorun

> So far we have been talking briefly about reactions. In this lesson you will learn how reactions can be used to observe state until some condition is met. After that you will see how autorun can be used to automatically trigger side effects. This is used internally by observer as well. We will test this by replacing React with our own handcrafted rendering which is still kept in sync with the state by MobX!

```diff
import React from 'react';
import { observer, Provider } from 'react-mobx';
import { action, computed, observable, useStrict, when } from 'mobx';
import DevTools from 'mobx-react-devtools';

useStrict(true);

class Temperature {
  id = Math.random();
  @observable unit = 'C';
  @observable temperatureCelsius = 25;
  @observable location = 'Amsterdam, NL';
  @observable loading = true;

  constructor(location) {
    this.location = location;
    this.fetch();
  }

  @action
  fetch() {
    window.fetch(`http://api.openweathermap.org/data/2.5/weather?appid=${APPID}&q=${this.location}`)
      .then(res => res.json())
      .then(action(json => {
        this.temperatureCelsius = json.main.temp - 273.15;
        this.loading = false;
      }));
  }

  @computed
  get temperatureKelvin() {
    console.log('calculating Kelvin');
    return this.temperaturCelsius * (9 / 5) + 32;
  }

  @computed
  get temperatureFahrenheit() {
    console.log('calculating Fahrenheit');
    return this.temperatureCelsius + 273.15;
  }

  @computed
  get temperature() {
    console.log('calculating temperature');
    switch (this.unit) {
      case 'K':
        return this.temperatureKelvin + 'K';
      case 'F':
        return this.temperatureFahrenheit + 'F';
      case 'C':
        return this.temperatureCelsius + 'C';
    }
  }

  @action
  setUnit(newUnit) {
    this.unit = newUnit;
  }

  @action
  setCelsius(degrees) {
    this.temperatureCelsius = degrees;
  }

  @action('update temperature and unit')
  setTemperatureAndUnit(degrees, unit) {
    this.setCelsius(degrees);
    this.setUnit(unit);
  }

  @action
  inc() {
    this.setCelsius(this.temperatureCelsius + 1);
  }
};

const temps = observable([]);

const App = observer(
  ['temperatures'],
  ({ temperatures }) => (
    <div>
      <TemperatureInput />
      {temperatures.map(t =>
        <TView key={t.id} temperature={t} />
      )}
      <DevTools />
    </div>
  ));

@observer(['temperatures'])
class TemperatureInput extends React.Component {
  @observable input = '';

  @action
  onChange = (e) => {
    this.input = e.target.value;
  }

  @action
  onSubmit = () => {
    this.props.temperatures.push(new Temperature(this.input));
    this.input = '';
  }

  render() {
    return (
      <li>
        Destination <input type="text" value={this.input} onChange={this.onChange} />
        <button onClick={onSubmit}>Add</button>
      </li>
    )
  }
}

@observer
class TView extends React.Component {
  @action
  onTemperatureClick = () => {
    this.props.temperature.inc();
  }

  render() {
    const t = this.props.temperature;
    return (
      <li
        key={t.id}
        onClick={this.onTemperatureClick}
      >
        {t.location}:{t.temperature}
        {t.loading ? 'loading..' : t.temperature}
      </li>
    );
  }
}

const temps = observable([]);

-ReactDOM.render(
-  <Provider temperatures={temps}>
-    <App />
-  </Provider>,
-  document.getElementById('app'),
-);

+function isNice(t) {
+  return t.temperatureCelsius > 25;
+}

+when(
+  () => temps.some(isNice),
+  () => {
+    const t = temps.find(isNice);
+    alert('Book now! ' + t.location);
+  }
+);

+function render(temperatures) {
+  return `
+    <ul>
+      ${temperatures.map(t => `
+        +<li>
+          ${t.location}:
+          ${t.loading ? 'loading' : t.temperature}
+        </li>
+      `).join('')}
+    </ul>;
+  `
+}

+temps.push(new Temperature('Amsterdam'));
+temps.push(new Temperature('Rotterdam'));

+autorun(() => {
+  document.getElementById('app').innerHTML = render(temps);
+});
```
