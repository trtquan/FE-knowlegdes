Tree shaking is a term commonly used in the JavaScript context for [[dead-code elimination]]. It relies on the [[static structure]] of ES2015 module syntax, i.e. import and export. The name and concept have been popularized by the ES2015 module bundler rollup.

The webpack 2 release came with built-in support for ES2015 modules (alias harmony modules) as well as unused module export detection. The new webpack 4 release expands on this capability with a way to provide hints to the compiler via the "sideEffects" package.json property to denote which files in your project are "pure" and therefore safe to prune if unused.

#### Add a Utility

Let's add a new utility file to our project, src/math.js, that exports two functions:

project

```js
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
  |- bundle.js
  |- index.html
|- /src
  |- index.js
+ |- math.js
|- /node_modules
```

src/math.js

```js
export function square(x) {
  return x * x;
}

export function cube(x) {
  return x * x * x;
}
```

Set the mode configuration option to development to make sure that the bundle is not minified:

webpack.config.js

```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
+ mode: 'development',
+ optimization: {
+   usedExports: true,
+ },
};
```

With that in place, let's update our entry script to utilize one of these new methods and remove lodash for simplicity:

src/index.js

```js
- import _ from 'lodash';
+ import { cube } from './math.js';

  function component() {
-   const element = document.createElement('div');
+   const element = document.createElement('pre');

-   // Lodash, now imported by this script
-   element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   element.innerHTML = [
+     'Hello webpack!',
+     '5 cubed is equal to ' + cube(5)
+   ].join('\n\n');

    return element;
  }

  document.body.appendChild(component());

```

Note that we did not import the square method from the src/math.js module. That function is what's known as "dead code", meaning an unused export that should be dropped. Now let's run our npm script, npm run build, and inspect the output bundle:

dist/bundle.js (around lines 90 - 100)

```js
/* 1 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {
  'use strict';
  /* unused harmony export square */
  /* harmony export (immutable) */ __webpack_exports__['a'] = cube;
  function square(x) {
    return x * x;
  }

  function cube(x) {
    return x * x * x;
  }
});

```

Note the unused harmony export square comment above. If you look at the code below it, you'll notice that square is not being imported, however, it is still included in the bundle. We'll fix that in the next section.

Mark the file as side-effect-free
In a 100% ESM module world, identifying side effects is straightforward. However, we aren't there just yet, so in the mean time it's necessary to provide hints to webpack's compiler on the "pureness" of your code.

The way this is accomplished is the "`sideEffects`" package.json property.

```json
{
  "name": "your-project",
  "sideEffects": false
}
```
All the code noted above does not contain side effects, so we can simply mark the property as `false` to inform webpack that it can safely `prune` unused exports.


>A "side effect" is defined as code that performs a special behavior when imported, other than exposing one or more exports. An example of this are polyfills, which affect the global scope and usually do not provide an export.

If your code did have some side effects though, an array can be provided instead:
```js
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js"
  ]
}

```

The array accepts relative, absolute, and glob patterns to the relevant files. It uses micromatch under the hood.

> [[missing css import]]	]]Note that any imported file is subject to tree shaking. This means if you use something like css-loader in your project and import a CSS file, it needs to be added to the side effect list so it will not be unintentionally dropped in production mode:

```js
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js",
    "*.css"
  ]
}
```