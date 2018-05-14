## STR:

1. Clone this repo
2. `yarn install`
3. `yarn build`

## Expected:

There is no `dist/vendors~pageA~pageB.js` chunk generated, since after the CSS is extracted, there should be no common JS code between the two pages.

## Actual:

A `dist/vendors~pageA~pageB.js` file is generated, and is only 78 bytes after CSS extraction:

```
$ webpack
clean-webpack-plugin: C:\Users\Ed\src\testcase-webpack-splitchunks-css\dist has been removed.
Hash: 92762fba701da2f64083
Version: webpack 4.8.3
Time: 988ms
Built at: 2018-05-14 20:24:31
                  Asset      Size  Chunks             Chunk Names
vendors~pageA~pageB.css   138 KiB       0  [emitted]  vendors~pageA~pageB
 vendors~pageA~pageB.js  78 bytes       0  [emitted]  vendors~pageA~pageB
               pageB.js  1.09 KiB       1  [emitted]  pageB
               pageA.js  1.09 KiB       2  [emitted]  pageA
Entrypoint pageA = vendors~pageA~pageB.css vendors~pageA~pageB.js pageA.js
Entrypoint pageB = vendors~pageA~pageB.css vendors~pageA~pageB.js pageB.js
[1] ./src/page-b.js 47 bytes {1} [built]
[3] ./src/page-a.js 47 bytes {2} [built]
    + 2 hidden modules
Child mini-css-extract-plugin node_modules/css-loader/index.js!node_modules/bootstrap/dist/css/bootstrap.min.css:
    Entrypoint mini-css-extract-plugin = *
       2 modules
```

The minified file contains:

```js
(window.webpackJsonp=window.webpackJsonp||[]).push([[0],[function(n,w,o){}]]);
```

There seems to be two bugs:
1. a file containing only webpack helper functions should be removed/optimized away, but isn't
2. the `splitChunks.minSize` default threshold of 30KB seems to not take into account CSS extraction (ie the size should be measured after extraction).
