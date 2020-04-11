---
title: null-loader
source: https://raw.githubusercontent.com/webpack-contrib/null-loader/master/README.md
edit: https://github.com/webpack-contrib/null-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/null-loader
---

返回空模块

[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![chat][chat]][chat-url]



返回一个空模块的 webpack loader。

此 loader 的一个用途是，使依赖项导入的模块静音。
例如，项目依赖于一个 ES6 库，会导入不需要的 polyfill，
因此删除它将不会导致功能损失。

## 要求

null-loader 的一个用途是使依赖项导入的模块静音。例如，当项目依赖于 ES6 库而并不需要 polyfill，因此删除它将不会影响任何功能。测试 polyfill 的路径，它不会包含在依赖包中：

```js
const path = require("path");

module.exports = {
  module: {
    rules: [
      {
        test: path.resolve(__dirname, "node_modules/library/polyfill.js"),
        use: "null-loader"
      }
    ]
  }
};
```

然后，通过你偏爱的方式去运行 `webpack`。

## 贡献人员

如果你从未阅读过我们的贡献指南，请在上面花点时间。

#### [贡献指南](https://raw.githubusercontent.com/webpack-contrib/null-loader/master/.github/CONTRIBUTING)

[npm]: https://img.shields.io/npm/v/null-loader.svg
[npm-url]: https://npmjs.com/package/null-loader
[deps]: https://david-dm.org/webpack-contrib/null-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/null-loader
[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack
