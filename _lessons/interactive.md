---
layout: page
permalink: "/interactive/"
title: "Making Web Pages Interactive"
questions:
- FIXME
keypoints:
- FIXME
---

- Browsers allow us to define an _event handler_ to specify what to do in response to a user action
  - A callback function that is (usually) given an _event object_ containing information about what the user did
- Pass the callback function as a specifically-named property of the thing whose behavior we are specifying
- We'll switch back to single-page examples for a moment

<!-- @src/interactive/hello-button.html -->
```js
  <body>
    <div id="app">
      <!-- this is filled in -->
    </div>
    <script type="text/babel">
      let counter = 0;
      const sayHello = (event) => {
        counter += 1
        console.log(`Hello, button: ${counter}`)
      }

      ReactDOM.render(
        <button onClick={sayHello}>click this</button>,
        document.getElementById("app")
      )
    </script>
  </body>
```

- Global variables and functions are a poor way to structure code
- Better to define the component as a class
  - And then use a method as the event handler

<!-- @src/interactive/display-counter.html -->
```js
      class Counter extends React.Component {

        constructor (props) {
          super(props)
          this.state = {counter: 0}
        }

        increment = (event) => {
          this.setState({counter: this.state.counter+1})
        }

        render() {
          return (
            <p>
              <button onClick={this.increment}>increment</button>
              <br/>
              current: {this.state.counter}
            </p>
          )
        }
      }

      ReactDOM.render(
        <Counter />,
        document.getElementById("app")
      )
```

- `ReactDOM.render` call at the end does what it always has
- Class has three parts
  1. Constructor passes the properties up to `React.Component`'s constructor
     and then creates a member variable called `state`
     that holds this component's state.
  2. The `increment` method uses `setState` (inherited from `React.Component`)
     to change the value of the counter.
     We *must* do this rather than creating and modifying `this.counter`
     so that React will notice the change in state
     and re-draw what it needs to.
  3. The `render` method takes the place of the functions we have been using so far.
     It can do anything it wants, but must return some HTML (using JSX).
     Here, it:
     - creates a button with an event handler
     - displays the current value of the counter
- React calls components' `render` methods after `setState` is used to update their state
  - It does some thinking behind the scenes to minimize how much redrawing takes place

## Models and Views

- Common practice to separate models (which store data) from views (which display it)
  - Models are typically classes
  - Views are typically pure functions
- Re-implement the counter using
  - `App`: stores the state and provides methods for altering it
  - `NumberDisplay`: does nothing except display a number
  - `UpAndDown`: provides buttons to go up and down
- Crucial design features: `NumberDisplay` and `UpAndDown` don't know:
  - What they're displaying
  - What actions are being taken on their behalf
  - So they're easier to re-use
- Again, we're cheating on the component loading

<!-- @src/interactive/multi-component/index.html -->
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Hello World</title>
    <meta charset="utf-8">
    <script src="https://fb.me/react-15.0.1.js"></script>
    <script src="https://fb.me/react-dom-15.0.1.js"></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.js"></script>
    <script src="NumberDisplay.js" type="text/babel"></script>
    <script src="UpAndDown.js" type="text/babel"></script>
    <script src="app.js" type="text/babel"></script>
  </head>
  <body>
    <div id="app">
      <!-- this is filled in -->
    </div>
    <script type="text/babel">
      ReactDOM.render(
        <App />,
        document.getElementById("app")
      )
    </script>
  </body>
</html>
```

<!-- @src/interactive/multi-component/NumberDisplay.js -->
```js
const NumberDisplay = (props) => {
  return (<p>{props.label}: {props.value}</p>)
}
```

<!-- @src/interactive/multi-component/UpAndDown.js -->
```js
const UpAndDown = (props) => {
  return (
    <p>
      <button onClick={props.up}> [+] </button>
      <button onClick={props.down}> [-] </button>
    </p>
  )
}
```

<!-- @src/interactive/multi-component/app.js -->
```js
class App extends React.Component {

  constructor (props) {
    super(props)
    this.state = {counter: 0}
  }

  increment = (event) => {
    this.setState({counter: this.state.counter + 1})
  }

  decrement = (event) => {
    this.setState({counter: this.state.counter - 1})
  }

  render = () => {
    return (
      <div>
        <UpAndDown up={this.increment} down={this.decrement} />
        <NumberDisplay label='counter' value={this.state.counter} />
      </div>
    )
  }
}
```

FIXME: diagram

- This may seem pretty complicated
- Because it is, in this small example
- But this strategy is widely used to manage large applications
  - Data and event handlers are defined near the top
  - Then passed down for other components to use