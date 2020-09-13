**Reducers** specify how the application's state changes in response to actions sent to the store. Remember that actions only describe what happened, but don't describe **how the application's state changes**.

### Designing the State Shape
[[designing-the-state-shape]]
In Redux, all the application state is stored as a single object. It's a good idea to think of its shape before writing any code. What's the minimal representation of your app's state as an object?

For our todo app, we want to store two different things:

* The currently selected visibility filter.
* The actual list of todos.

>**Note on Relationships**
In a more complex app, you're going to want different entities to reference each other. We suggest that you keep your state as normalized as possible, without any nesting. Keep every entity in an object stored with an ID as a key, and use IDs to reference it from other entities, or lists. Think of the app's state as a database. This approach is described in [[normalizr]]'s documentation in detail. For example, keeping todosById: { id -> todo } and todos: array /<id/> inside the state would be a better idea in a real app, but we're keeping the example simple.

### Handling Actions

Now that we've decided what our state object looks like, we're ready to write a reducer for it. The reducer is a pure function that takes the previous state and an action, and returns the next state.

```js
(previousState, action) => nextState
```

It's called a reducer because it's the type of function you would pass to Array.prototype.reduce(reducer, ?initialValue). It's very important that the reducer stays pure. Things you should `never` do inside a reducer:

Mutate its arguments;
Perform side effects like API calls and routing transitions;
Call non-pure functions, e.g. Date.now() or Math.random().

Given the same arguments, it should calculate the next state and return it. No surprises. No side effects. No API calls. No mutations. Just a calculation.

With this out of the way, let's start writing our reducer by gradually teaching it to understand the [[actions]] we defined earlier.

We'll start by specifying the initial state. Redux will call our reducer with an undefined state for the first time. This is our chance to return the initial state of our app:

We don't mutate the state. We create a copy with Object.assign(). Object.assign(state, { visibilityFilter: action.filter }) is also wrong: it will mutate the first argument. You must supply an empty object as the first parameter. You can also enable the object spread operator proposal to write { ...state, ...newState } instead.

We return the previous state in the default case. It's important to return the previous state for any unknown action.


### Handling More Actions

We have two more actions to handle! Just like we did with SET_VISIBILITY_FILTER, we'll import the ADD_TODO and TOGGLE_TODO actions and then extend our reducer to handle ADD_TODO.
```js
import {
  ADD_TODO,
  TOGGLE_TODO,
  SET_VISIBILITY_FILTER,
  VisibilityFilters
} from './actions'

...

function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    default:
      return state
  }
}
```
Just like before, we never write directly to state or its fields, and instead we return new objects. The new todos is equal to the old todos concatenated with a single new item at the end. The fresh todo was constructed using the data from the action.

Finally, the implementation of the TOGGLE_TODO handler shouldn't come as a complete surprise:
```js
case TOGGLE_TODO:
  return Object.assign({}, state, {
    todos: state.todos.map((todo, index) => {
      if (index === action.index) {
        return Object.assign({}, todo, {
          completed: !todo.completed
        })
      }
      return todo
    })
  })
  ```

Because we want to update a specific item in the array without resorting to mutations, we have to create a new array with the same items except the item at the index. If you find yourself often writing such operations, it's a good idea to use a helper like immutability-helper, updeep, or even a library like Immutable that has native support for deep updates. Just remember to never assign to anything inside the state unless you clone it first.

### Splitting Reducers

```js
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: state.todos.map((todo, index) => {
          if (index === action.index) {
            return Object.assign({}, todo, {
              completed: !todo.completed
            })
          }
          return todo
        })
      })
    default:
      return state
  }
}
```

Is there a way to make it easier to comprehend? It seems like `todos` and `visibilityFilter` are updated completely independently. Sometimes state fields depend on one another and more consideration is required, but in our case we can easily split updating `todos` into a separate function:

```js
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}

function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: todos(state.todos, action)
      })
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: todos(state.todos, action)
      })
    default:
      return state
  }
}
```

This is called [[reducer-composition]]

Note that each of these reducers is managing its own part of the global state. The state parameter is different for every reducer, and corresponds to the part of the state it manages.

This is already looking good! When the app is larger, we can split the reducers into separate files and keep them completely independent and managing different data domains.

Finally, Redux provides a utility called combineReducers() that does the same boilerplate logic that the todoApp above currently does. With its help, we can rewrite todoApp like this:

>Note for ES6 Savvy Users
Because combineReducers expects an object, we can put all top-level reducers into a separate file, export each reducer function, and use import * as reducers to get them as an object with their names as the keys:

>import { combineReducers } from 'redux'
import * as reducers from './reducers'

>const todoApp = combineReducers(reducers)
>Because import * is still new syntax, we no longer use it in the documentation to avoid confusion, but you may encounter it in some community examples.