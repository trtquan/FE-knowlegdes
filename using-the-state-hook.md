### Equivalent Class Example

```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

The state starts as { count: 0 }, and we increment state.count when the user clicks a button by calling this.setState(). We’ll use snippets from this class throughout the page.

Hooks and Function Components

```js
const Example = (props) => {
  // You can use Hooks here!
  return <div />;
}
```

You might have previously known these as “[[stateless-components]]”. We’re now introducing the ability to use React state from these, so we prefer the name [[react-function-components]]

### What’s a Hook
Our new example starts by importing the useState Hook from React:

```js
import React, { useState } from 'react';

function Example() {
  // ...
}
```

A Hook is a special function that lets you “hook into” React features.

Eexample, useState is a Hook that lets you add React state to function components

**When would I use a Hook?** If you write a function component and realize you need to add some state to it, previously you had to convert it to a class. Now you can use a Hook inside the existing function component. We’re going to do that right now!

> [[rules-of-hooks]]

### Declaring a State Variable
In a class, we initialize the count state to 0 by setting this.state to { count: 0 } in the constructor:

```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
```

In a function component, we have no this, so we can’t assign or read this.state. Instead, we call the useState Hook directly inside our component:

```js
import React, { useState } from 'react';

function Example() {
  // Declare a new state variable, which we'll call "count"
  const [count, setCount] = useState(0);
```

**What does calling useState do?** It declares a “state variable”. Our variable is called `count` but we could call it anything else, like banana. This is a way to “preserve” some values between the function calls — useState is a new way to use the exact same capabilities that this.state provides in a class. Normally, variables “disappear” when the function exits but state variables are preserved by React.

**What do we pass to useState as an argument?** The only argument to the useState() Hook is the initial state. Unlike with classes, the state doesn’t have to be an object. We can keep a number or a string if that’s all we need. In our example, we just want a number for how many times the user clicked, so pass 0 as initial state for our variable. (If we wanted to store two different values in state, we would call useState() twice.)

**What does useState return?** It returns a pair of values: the current state and a function that updates it. This is why we write const [count, setCount] = useState(). This is similar to this.state.count and this.setState in a class, except you get them in a pair.

Now that we know what the useState Hook does, our example should make more sense:

We declare a state variable called count, and set it to 0. React will remember its current value between re-renders, and provide the most recent one to our function. If we want to update the current count, we can call setCount.

> You might be wondering: why is `useState` not named `createState` instead?

>“Create” wouldn’t be quite accurate because the state is only created the first time our component renders. During the next renders, useState gives us the current state. Otherwise it wouldn’t be “state” at all! There’s also a reason why Hook names always start with use. [[rules-of-hooks]]

### Reading State

When we want to display the current count in a class, we read this.state.count:

```js
  <p>You clicked {this.state.count} times</p>
```

In a function, we can use count directly:

```js
  <p>You clicked {count} times</p>
```

Updating State

```js
//In a class, we need to call this.setState() to update the count state:

  <button onClick={() => this.setState({ count: this.state.count + 1 })}>
    Click me
  </button>
  
//In a function, we already have setCount and count as variables so we don’t need this:

  <button onClick={() => setCount(count + 1)}>
    Click me
  </button>
 ```
 
 Recap
 
 ```js
 1:  import React, { useState } from 'react';
 2:
 3:  function Example() {
 4:    const [count, setCount] = useState(0);
 5:
 6:    return (
 7:      <div>
 8:        <p>You clicked {count} times</p>
 9:        <button onClick={() => setCount(count + 1)}>
10:         Click me
11:        </button>
12:      </div>
13:    );
14:  }
 ```
 
 * Line 1: We import the useState Hook from React. It lets us keep local state in a function component.
* Line 4: Inside the Example component, we declare a new state variable by calling the useState Hook. It returns a pair of values, to which we give names. We’re calling our variable count because it holds the number of button clicks. We initialize it to zero by passing 0 as the only useState argument. The second returned item is itself a function. It lets us update the count so we’ll name it setCount.
* Line 9: When the user clicks, we call setCount with a new value. React will then re-render the Example component, passing the new count value to it.

### Tip: Using Multiple State Variables

Declaring state variables as a pair of [something, setSomething] is also handy because it lets us give different names to different state variables if we want to use more than one:

```js
function ExampleWithManyStates() {
  // Declare multiple state variables!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
```

You **don’t have to use many state variables**. State variables can hold objects and arrays just fine, so you can still group related data together. However, unlike this.setState in a class, **updating a state variable always replaces it instead of merging it**