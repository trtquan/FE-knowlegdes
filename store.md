The Store is the object that brings them together. The store has the following responsibilities:

* Holds application state;
* Allows access to state via getState();
* Allows state to be updated via dispatch(action);
* Registers listeners via subscribe(listener);
* Handles unregistering of listeners via the function returned by subscribe(listener).


It's important to note that you'll only have a single store in a Redux application. When you want to split your data handling logic, you'll use [[reducer-composition]] instead of many stores.

Dispatching Actions

[[data-flow]]