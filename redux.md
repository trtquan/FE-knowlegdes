 Redux is a predictable state container for JavaScript apps.
 
 ### Installation
 Redux is available as a package on NPM for use with a module bundler or in a Node application:
 ```text
 # NPM
npm install redux

# Yarn
yarn add redux
 ```
 
 ### [[redux-toolkit]] 
 
 Redux itself is small and unopinionated. We also have a separate addon package called Redux Toolkit, which includes some opinionated defaults that help you use Redux more effectively. It's our official recommended approach for writing Redux logic
 
 RTK includes utilities that help simplify many common use cases, including [[store]]-setup, creating reducers and writing immutable update logic, and even creating entire "slices" of state at once.
 
 Create a React Redux App
 
 The recommended way to start new apps with React and Redux is by using the official Redux+JS template for Create React App, which takes advantage of Redux Toolkit and React Redux's integration with React components
 
 ```js
 npx create-react-app my-app --template redux
 ```
 
 The whole state of your app is stored in an object tree inside a single store. The only way to change the state tree is to emit an action, an object describing what happened. To specify how the actions transform the state tree, you write pure [[reducers]].
 
 Instead of mutating the state directly, you specify the mutations you want to happen with plain objects called [[actions]]. Then you write a special function called a [[reducers]] to decide how every action transforms the entire application's state.

In a typical Redux app, there is just a single store with a single root reducing function. As your app grows, you split the root reducer into smaller reducers independently operating on the different parts of the state tree. This is exactly like how there is just one root component in a React app, but it is composed out of many small components.

### Core concept

To change something in the state, you need to dispatch an action. An action is a plain JavaScript object.

Enforcing that every change is described as an action lets us have a clear understanding of what’s going on in the app. If something changed, we know why it changed. Actions are like breadcrumbs of what has happened

. Finally, to tie state and actions together, we write a function called a [[reducers]].
it’s just a function that takes state and action as arguments, and returns the next state of the app.

### Three Principles

The global state of your application is stored in an object tree within a single store.

**Single source of truth**

tate in development, for a faster development cycle. Some functionality which has been traditionally difficult to implement - Undo/Redo, for example - can suddenly become trivial to implement, if all of your state is stored in a single tree.

**State is read-only**

The only way to change the state is to emit an [[actions]], an object describing what happened.

This ensures that neither the views nor the network callbacks will ever write directly to the state. Instead, they express an intent to transform the state. Because all changes are centralized and happen one by one in a strict order, there are no subtle race conditions to watch out for. As actions are just plain objects, they can be logged, serialized, stored, and later replayed for debugging or testing purposes.

**Changes are made with pure functions**

To specify how the state tree is transformed by actions, you write pure reducers.

Reducers are just pure functions that take the previous state and an action, and return the next state. Remember to return new state objects, instead of mutating the previous state. You can start with a single reducer, and as your app grows, split it off into smaller reducers that manage specific parts of the state tree. Because reducers are just functions, you can control the order in which they are called, pass additional data, or even make reusable reducers for common tasks such as pagination.
