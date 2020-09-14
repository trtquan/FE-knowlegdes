In frontend development, modules are usually handled as follows:

*  During development, code exists as many, often small, modules.
*  For deployment, these modules are bundled into a few, relatively large, files.

The reasons for bundling are:

1. Fewer files need to be retrieved in order to load all modules.
2. Compressing the bundled file is slightly more efficient than compressing separate files.
3. During bundling, unused exports can be removed, potentially resulting in significant space savings.

Reason #1 is important for HTTP/1, where the cost for requesting a file is relatively high. That will change with HTTP/2, which is why this reason doesn’t matter there.

Reason #3 will remain compelling. It can only be achieved with a module format that has a static structure.