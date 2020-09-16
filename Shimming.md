The webpack compiler can understand modules written as ES2015 modules, CommonJS or AMD. However, some third party libraries may expect global dependencies (e.g. $ for jQuery). The libraries might also create globals which need to be exported. These "broken modules" are one instance where shimming comes into play.

> We don't recommend using globals! The whole concept behind webpack is to allow more modular front-end development. This means writing isolated modules that are well contained and do not rely on hidden dependencies (e.g. globals). Please use these features only when necessary.

Another instance where shimming can be useful is when you want to [[polyfill]] browser functionality to support more users. In this case, you may only want to deliver those polyfills to the browsers that need patching (i.e. load them on demand).

### Shimming Globals
Let's start with the first use case of shimming global variables. Before we do anything let's take another look at our project

```text
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
|- /src
  |- index.js
|- /node_modules
```

Remember that lodash package we were using? For demonstration purposes, let's say we wanted to instead provide this as a global throughout our application. To do this, we can use [[ProvidePlugin]].

The [[ProvidePlugin]] makes a package available as a variable in every module compiled through [[Webpack]]. If `webpack` sees that variable used, it will include the given package in the final bundle. Let's go ahead by removing the import statement for lodash and instead provide it via the plugin

src/index.js


```js
- import _ from 'lodash';
-
  function component() {
    const element = document.createElement('div');

-   // Lodash, now imported by this script
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');

    return element;
  }

  document.body.appendChild(component());
```

webpack.config.js

```js
 const path = require('path');
+ const webpack = require('webpack');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist'),
    },
+   plugins: [
+     new webpack.ProvidePlugin({
+       _: 'lodash',
+     }),
+   ],
  };
```

What we've essentially done here is tell webpack...


>If you encounter at least one instance of the variable _, include the lodash package and provide it to the modules that need it.

We can also use the ProvidePlugin to expose a single export of a module by configuring it with an "array path" (e.g. [module, child, ...children?]). So let's imagine we only wanted to provide the join method from lodash wherever it's invoked

src/index.js

```js
  function component() {
    const element = document.createElement('div');

-   element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   element.innerHTML = join(['Hello', 'webpack'], ' ');

    return element;
  }

  document.body.appendChild(component());
```
webpack.config.js


```
  const path = require('path');
  const webpack = require('webpack');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist'),
    },
    plugins: [
      new webpack.ProvidePlugin({
-       _: 'lodash',
+       join: ['lodash', 'join'],
      }),
    ],
  };
```

This would go nicely with [[Tree Shaking]] as the rest of the lodash library should get dropped.


### Granular Shimming
Some legacy modules rely on this being the window object. Let's update our index.js so this is the case:

```js
function component() {
    const element = document.createElement('div');

    element.innerHTML = join(['Hello', 'webpack'], ' ');
+
+   // Assume we are in the context of `window`
+   this.alert('Hmmm, this probably isn\'t a great idea...')

    return element;
  }

  document.body.appendChild(component());
```

This becomes a problem when the module is executed in a CommonJS context where this is equal to module.exports. In this case you can override this using the [[imports-loader]]: