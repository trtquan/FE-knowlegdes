Automatically load modules instead of having to import or require them everywhere.

```js
new webpack.ProvidePlugin({
  identifier: ['module1', 'property1'],
  // ...
});

```

By default, module resolution path is current folder (./**) and `node_modules`.

It is also possible to specify full path:


```js
const path = require('path');

new webpack.ProvidePlugin({
  identifier: path.resolve(path.join(__dirname, 'src/module1'))
  // ...
});

```


Whenever the identifier is encountered as free variable in a module, the module is loaded automatically and the identifier is filled with the exports of the loaded module (or property in order to support named exports).

For importing the default export of an ES2015 module, you have to specify the default property of module.



Usage: [[missing jQuery in React]]
To automatically load jquery we can simply point both variables it exposes to the corresponding node module:


```js
new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery'
});
```

Then in any of our source code:

```js
// in a module
$('#item'); // <= just works
jQuery('#item'); // <= just works
// $ is automatically set to the exports of module "jquery"

```

Usage: [[ provide Lodash Map global;]]

```js
new webpack.ProvidePlugin({
  _map: ['lodash', 'map']
});

```
