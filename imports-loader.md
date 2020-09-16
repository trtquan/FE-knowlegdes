The imports loader allows you to use modules that depend on specific global variables.

This is useful for third-party modules that rely on global variables like $ or this being the window object. The imports loader can add the necessary require('whatever') calls, so those modules work with webpack.

For further hints on compatibility issues, check out Shimming of the official docs.