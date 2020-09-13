
Actions are payloads of information that send data from your application to your store. They are the only source of information for the store. You send them to the store using store.dispatch().

Actions are plain JavaScript objects. Actions must have a type property that indicates the type of action being performed. Types should typically be defined as string constants. Once your app is large enough, you may want to move them into a separate module.

It's a good idea to pass as little data in each action as possible

Action creators

 It's easy to conflate the terms “action” and “action creator”, so do your best to use the proper term.

In Redux, action creators simply return an action:

In traditional Flux, action creators often trigger a dispatch when invoked, like so:

```js
function addTodoWithDispatch(text) {
  const action = {
    type: ADD_TODO,
    text
  }
  dispatch(action)
}
```

In Redux this is not the case. Instead, to actually initiate a dispatch, pass the result to the dispatch() function:

```js
dispatch(addTodo(text))
dispatch(completeTodo(index))
```

Alternatively, you can create a bound action creator that automatically dispatches:

const boundAddTodo = text => dispatch(addTodo(text))
const boundCompleteTodo = index => dispatch(completeTodo(index))

The dispatch() function can be accessed directly from the store as store.dispatch(), but more likely you'll access it using a helper like [react-redux]'s connect(). You can use bindActionCreators() to automatically bind many action creators to a dispatch() function.

Action creators can also be asynchronous and have side-effects. You can read about [[async-actions]] in the advanced tutorial to learn how to handle AJAX responses and compose action creators into async control flow. Don't skip ahead to async actions until you've completed the basics tutorial, as it covers other important concepts that are prerequisite for the advanced tutorial and async actions.
