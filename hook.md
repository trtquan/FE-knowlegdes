[[the-gradual-adoption-strategy]]

Hooks provide a more direct API to the React concepts you already know: [[props]], [[state]], [[context]], [[refs]], and [[react-lifecycle]]

### It’s hard to reuse stateful logic between components

React doesn’t offer a way to “attach” reusable behavior to a component (for example, connecting it to a store). If you’ve worked with React for a while, you may be familiar with patterns like [[render-props]] and [[higher-order-components]]  that try to solve this. But these patterns require you to restructure your components when you use them, which can be cumbersome and make code harder to follow. If you look at a typical React application in [[react-dev-tools]]

you will likely find a “[[wrapper-hell]]” of components surrounded by layers of [[react-redux-providers]], consumers, higher-order components, render props, and other abstractions. While we could filter them out in DevTools, this points to a deeper underlying problem: React needs a better primitive for sharing stateful logic.
[[using-the-state-hook]]
With Hooks, you can extract [[stateful-logic]] from a component so it can be tested independently and reused. Hooks allow you to reuse stateful logic without changing your component hierarchy. This makes it easy to share Hooks among many components or with the community.

[[building-your-own-hooks]]

### Complex components become hard to understand

For example, components might perform some data fetching in componentDidMount and componentDidUpdate. However, the same componentDidMount method might also contain some unrelated logic that sets up event listeners, with cleanup performed in componentWillUnmount. Mutually related code that changes together gets split apart, but completely unrelated code ends up combined in a single method. This makes it too easy to introduce bugs and inconsistencies

To solve this, Hooks let you split one component into smaller functions based on what pieces are related (such as setting up a subscription or fetching data), rather than forcing a split based on lifecycle methods. You may also opt into managing the component’s local state with a reducer to make it more predictable. [[using-the-effect-hook]]

### Classes confuse both people and machines

[[hooks-at-a-glance]]

[[thinking-in-hooks]]

[[building-your-own-hooks]]

[[rules-of-hooks]]
 [[hooks-API-reference]]

linter plugin

```txt
npm i eslint-plugin-react-hooks
```

