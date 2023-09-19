# Intro to React - Lecture Notes

## Intro

- Gain a fundamental understanding of React
- Going to start with a blank html file
- we are going to slowly intro React concepts
- Learn that React is just functions and objects
- how to deploy

## Lesson 02

- this very similar to how React works
- We can replace basic JS with React
- Once we have a React element, explain children
- Question using raw React (not using JSX) how would you create a `div` React element with a class of `'container'` that contains the text "Hello World"?

## Lesson 03

- creating elements with function can be cumbersome
- JSX was invented to make this a bit more ergonomic
- Change element to JSX
- Add Babel script
- Easier to compose
- JSX uses className rather than class
- we provide attributes like HTML
- Move string to `content`
- Curly braces in JSX means moving to JS land, must be an expression, not a statement
  - try IIFE
  - also applies to props as attributes
- create a props object

  ```
  const props = {
    className: 'container',
    children: 'Hello World
  }
  ```

- spread props, similar to object props
- override props as example

## 04 Lesson

- sometimes people make mistakes when using a component
- create our own prop types to demo validation, add it to `SayHello.propTypes`

  ```js
  const PropTypes = {
    string(props, propName, componentName) {
      if (typeof props[propName] !== 'string) {
        return new Error(`Hey, you should pass a string for ${propName} to ${componentName}, but instead you passed a ${typeof props[propName]}.`)
      }
    }
  }
  ```

- becauase this common, we can use the PropTypes package.
- <script src="https://unpkg.com/prop-types@15.6.1/prop-types.js"></script>
- have to add `isRequired`
- for class style components we can use a static property
- add prop validation
  ```js
          static propTypes = {
          firstName: PropTypes.string.isRequired,
          lastName: PropTypes.string.isRequired,
        };
  ```
- change to production

## Lesson 05

- render a message into a div
- what happens if we don't pass a message
- provide `<div>No message</div>` with `if`
- replace JSX with CRE
- refactor to ternary
- wrap the whole thing in a div
- must use a ternary because expression must be in JSX
- refactor back to JSX

## Lesson 06

- Rerendering!
- Going to show the current time (but doesn't refresh or update)
- want that to update every second
- refactor code into function `tick` and call tick
- add `setInterval(tick, 1000)`
- refactor to put time in `input`
  - important to note the focus remains on the input
- change from JSX to template string
  - `rootElement.innerHTML = element` last line in function
  - no getting an update of the _entire_ DOM structure rather than just the parts that change

## Lesson 07

- add `<div>box</div>`
- add `style` to `div` with `{paddingLeft: '20px'}`
  - uses an object with camel case properties
  - objects are easier to compose
  - could change value to a numbe
- add `className="box box--small"`
  - as opposed to `class` attribute
- extract props to object `props` and spread them in `div`
- extract `className` value to variable
- can't have `class` as variable name, syntax error
- extract to `Box` component
- render `Box` with `backgroundColor`
  - lost padding, shallow merge
  - refactor `Box` props to `{style, ...rest}`
  - would be much harder to do with a string
- factor out `box--small` to element attribute
- merge `className` prop
  - handle `undefined` case
  - default `''`
  - could trim
- add some medium and large boxes
- User shouldn't know about the class name
  - rather use a `size` prop
  - calculate `` sizeClassName = size ? `box--${size}` : '' ``

```js
function Box({ style, size, className = '', ...rest }) {
  const sizeClassName = size ? `box--${size}` : '';
  return (
    <div
      className={`box ${className} ${sizeClassName}`}
      style={{ paddingLeft: 20, ...style }}
      {...rest}
    />
  );
}
```

```js
<Box
  size='small'
  style={{backgroundColor: 'lightblue'}}
>
    small box
</Box>
<Box
  size='medium'
  style={{backgroundColor: 'pink'}}
>
  medium box
</Box>
<Box
  size='large'
  style={{backgroundColor: 'orange'}}
>
  large box
</Box>
```

## Lesson 08

- rendering some ui based on State
- explain setState, show how `setState` works
- add onClick `setState({eventEcount: state.eventCount + 1 })
- Demo onFocus or onHover
- add event handler for input to update the You typed area
- could also pull that out in `updateUserName`
- `console.log` event

```js
function increment() {
  setState({ eventCount: state.eventCount + 1 });
}

function updateUsername(event) {
  setState({
    username: event.target.value,
  });
}
```

## Lesson 09

- make a function called `StopWatch`
  ```js
    function StopWatch() {
      return (
        <div>
          <label>0ms</label>
          <button>start</button>
          <button>clear</button>
      )
    }
  ```
- change `element` to `StopWatch`
- copy and paste some styles to make it look nicer
- remove hard coded number, `lapse` from props
- Make start buttton dynamic: start, stop use `running` from props
- make class called StopWatch
  - move contents of function component into `render`
  - change props to `props`
  - move props to `state`
  - remove props
- make it dynamic, dynamically update
- add `onclick` handler to run button
  - `handleRunClick = () => alert('you clicked');`
  - `this.setState({lapse: 10, running: true})`
- add `onClick` handler to clear button
  
  - `handleClearClick => this.setState({lapse: 0, running: false})`
- kchange `handleRunClick`

  ```js
  handleRunClick = () => {
    startTime = Date.now() - this.state.lapse;
    setInterval(() => {
      this.setState({ lapse: Date.now() - startTime });
    });
    this.setState({ running: true });
  };
  ```

- refactor so we are calling `setState` so often
- we only want to update when running

  ```js
  handleRunClick = () => {
    startTime = Date.now() - this.state.lapse;
    this.setState(state => {
      if (state.running) {
        clearInterval(this.timer);
      } else {
        this.timer = setInterval(() => {
          this.setState({ lapse: Date.now() - startTime });
        });
      }
      return { running: !state.running };
    });
  };
  ```

- Clear button not working tho
  
  - Must clear the interval when click reset

````js
class StopWatch extends React.Component {

  state = {lapse: 0, running: false}

  handleRunClick = () => {
    this.setState(state => {
      if (state.running) {
        clearInterval(this.timer)
      } else {
        const startTime = Date.now() - this.state.lapse
        this.timer = setInterval(() => {
          this.setState({
            lapse: Date.now() - startTime,
          })
        })
      }
      return {running: !state.running}
    })
  }

  handleClearClick = () => {
    clearInterval(this.timer)
    this.setState({lapse: 0, running: false})
  }

  render() {
    const {lapse, running} = this.state
    const buttonStyles = {
        border: '1px solid #ccc',
        background: '#fff',
        fontSize: '2em',
        padding: 15,
        margin: 5,
        width: 200,
    }

    return (
      <div style={{textAlign: 'center'}}>
        <label
          style={{
            fontSize: '5em',
            display: 'block',
          }}
        >
            {lapse}ms
        </label>

        <button onClick={this.handleRunClick} style={buttonStyles}>
          {running ? 'Stop' : 'Start'}
        </button>

        <button onClick={this.handleClearClick} style={buttonStyles}>
          Clear
        </button>
      </div>
    )
  }
}
```
````

## Lesson 11

- describe component
  - initialise state
- sometimes it's cumbersome to have a an arrow function in the prop
  - factor out function to `handleClick`
  - but it's broken
  - the problem is fundamental to JS
  - fix with `.bind(this)`
- use public class fields just move everything out of the constructor

  - use bind
  - then use fat arrow function

## Lesson 13

- make new class `NameForm`
  - add render method
  - form with lable, input, and button
- will do a full page refresh
- add handleSubmit
  - preventDefault()
- add `console.log({target: event.target})`
- add `console.log(event.target[0].value)`
- `console.log(event.target.elements.username.value)`
- use ref
  - `ref={node => this.inputNode = node}`
  - then we can `console.log(this.inputNode.value)`
- first two are just regular HTML
- the `ref` prop is just a react thing

​```js
class NameForm extends React.Component {
  handleSubmit = event => {
    event.preventDefault();
    console.log({ target: event.target });
    console.log(event.target[0].value);
    console.log(event.target.elements.username.value);
    console.log(this.inputNode.value);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input
            type="text"
            name="username"
            ref={node => {
              this.inputNode = node;
            }}
          />
        </label>
        <button type="submit">Submit</button>
      </form>
    );
  }
}
```

## Lesson 14

- want to provide meaningful feedback for the user of our form
  - show errors as we type
  - disable the submit button until the form is valid
- need state to keep track if form is valid
  - `state = {error: null}`
  - on `input` add `onChange={this.handleChange}`
  - add handler
  ```js
  handleChange = event => {
    const { value } = event.target;
    this.setState({ error: this.props.getErrorMessage(value) });
  };
  ```
- disable button if `Boolean(error)`
- button starts enabled, so use cDM to set state `{error: this.props.getErrorMessage('')}`
- under input we'll add `<div style={{color: 'red'}}>{error}</div`>`
- we can move our cDM function to property instead

