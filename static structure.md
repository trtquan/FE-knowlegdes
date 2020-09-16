Current JavaScript module formats have a dynamic structure: What is imported and exported can change at runtime. One reason why ES6 introduced its own module format is to enable a static structure, which has several benefits. But before we go into those, let’s examine what the structure being static means.

It means that you can determine imports and exports at compile time (statically) – you only need to look at the source code, you don’t have to execute it. ES6 enforces this syntactically: You can only import and export at the top level (never nested inside a conditional statement). And import and export statements have no dynamic parts (no variables etc. are allowed).

The following are two examples of CommonJS modules that don’t have a static structure. In the first example, you have to run the code to find out what it imports:

```js
var my_lib;
if (Math.random()) {
    my_lib = require('foo');
} else {
    my_lib = require('bar');
}
```
In the second example, you have to run the code to find out what it exports:
```js
if (Math.random()) {
    exports.baz = ···;
}

```

ECMAScript 6 modules are less flexible and force you to be static. As a result, you get several benefits, which are described next.

#### Benefit:  [[dead-code elimination]] during bundlin

#### Benefit: [[compact bundling]], no custom bundle format 
 
#### Benefit: faster lookup of imports

If you require a library in CommonJS, you get back an object:
```js
var lib = require('lib');
lib.someFunc(); // property lookup
```

Thus, accessing a named export via lib.someFunc means you have to do a property lookup, which is slow, because it is dynamic.

In contrast, if you import a library in ES6, you statically know its contents and can optimize accesses:

```js
import * as lib from 'lib';
lib.someFunc(); // statically resolved
```

#### Benefit: variable checking

With a static module structure, you always statically know which variables are visible at any location inside the module:

* Global variables: increasingly, the only completely global variables will come from the language proper. Everything else will come from modules (including functionality from the standard library and the browser). That is, you statically know all global variables.
* Module imports: You statically know those, too.
* Module-local variables: can be determined by statically examining the module.

This helps tremendously with checking whether a given identifier has been spelled properly. This kind of check is a popular feature of linters such as JSLint and JSHint; in ECMAScript 6, most of it can be performed by JavaScript engines.

#### Benefit: ready for macros

Macros are still on the roadmap for JavaScript’s future. If a JavaScript engine supports macros, you can add new syntax to it via a library. Sweet.js is an experimental macro system for JavaScript. The following is an example from the Sweet.js website: a macro for classes.

```js
// Define the macro
macro class {
    rule {
        $className {
                constructor $cparams $cbody
                $($mname $mparams $mbody) ...
        }
    } => {
        function $className $cparams $cbody
        $($className.prototype.$mname
            = function $mname $mparams $mbody; ) ...
    }
}

// Use the macro
class Person {
    constructor(name) {
        this.name = name;
    }
    say(msg) {
        console.log(this.name + " says: " + msg);
    }
}
var bob = new Person("Bob");
bob.say("Macros are sweet!");
```

For macros, a JavaScript engine performs a preprocessing step before compilation: If a sequence of tokens in the token stream produced by the parser matches the pattern part of the macro, it is replaced by tokens generated via the body of macro. The preprocessing step only works if you are able to statically find macro definitions. Therefore, if you want to import macros via modules then they must have a static structure.

#### Benefit: ready for types #
Static type checking imposes constraints similar to macros: it can only be done if type definitions can be found statically. Again, types can only be imported from modules if they have a static structure.

Types are appealing because they enable statically typed fast dialects of JavaScript in which performance-critical code can be written. One such dialect is [Low-Level JavaScript (LLJS).](http://lljs.org/)

#### Support for both synchronous and asynchronous loading
ECMAScript 6 modules must work independently of whether the engine loads modules synchronously (e.g. on servers) or asynchronously (e.g. in browsers). Its syntax is well suited for synchronous loading, asynchronous loading is enabled by its static structure: Because you can statically determine all imports, you can load them before evaluating the body of the module (in a manner reminiscent of AMD modules).

#### Support for cyclic dependencies between modules 
Support for cyclic dependencies was a key goal for ES6 modules. Here is why:

Cyclic dependencies are not inherently evil. Especially for objects, you sometimes even want this kind of dependency. For example, in some trees (such as DOM documents), parents refer to children and children refer back to parents. In libraries, you can usually avoid cyclic dependencies via careful design. In a large system, though, they can happen, especially during refactoring. Then it is very useful if a module system supports them, because the system doesn’t break while you are refactoring.

[more](https://exploringjs.com/es6/ch_modules.html#static-module-structure)