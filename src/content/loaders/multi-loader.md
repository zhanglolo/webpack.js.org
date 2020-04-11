---
title: multi-loader
source: https://raw.githubusercontent.com/webpack-contrib/multi-loader/master/README.md
edit: https://github.com/webpack-contrib/multi-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/multi-loader
---

multi-loader 需要多次加载模块，每次加载不同的 loader。就像在多入口点一样，导出最后一项的出口。


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![chat][chat]][chat-url]



用于分离模块和组合使用多个 loader 的 `webpack` loader。
这个 loader 会多次引用一个模块，每次不同的 loader 加载这个模块，
就像下面配置中定义的那样。

_注意：在多入口，最后一项的 exports 会被导出。_

## 要求

此模块需要 Node v6.9.0+ 和 webpack v4.0.0+。

## 起步

你需要预先安装 `multi-loader`：

```console
$ npm install multi-loader --save-dev
```

然后，在 `webpack` 配置中添加 loader。例如：

```javascript
var multi = require("multi-loader");
{
  module: {
    loaders: [
      {
        test: /\.css$/,
        // Add CSS to the DOM
        // and
        // Return the raw content
        loader: multi(
          "style-loader!css-loader!autoprefixer-loader",
          "raw-loader"
        )
      }
    ];
  }
}
```

然后，通过你偏爱的方式去运行 `webpack`。

## License

[npm]: https://img.shields.io/npm/v/multi-loader.svg
[npm-url]: https://npmjs.com/package/multi-loader
[deps]: https://david-dm.org/webpack-contrib/multi-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/multi-loader
[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack
