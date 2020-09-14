[The module bundler Rollup](https://github.com/rollup/rollup) proved that ES6 modules can be combined efficiently, because they all fit into a single scope (after renaming variables to eliminate name clashes). This is possible due to two characteristics of ES6 modules:

* Their static structure means that the bundle format does not have to account for conditionally loaded modules (a common technique for doing so is putting module code in functions).
* Imports being read-only views on exports means that you don’t have to copy exports, you can refer to them directly.
