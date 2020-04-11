---
title: ZopfliWebpackPlugin
source: https://raw.githubusercontent.com/webpack-contrib/zopfli-webpack-plugin/master/README.md
edit: https://github.com/webpack-contrib/zopfli-webpack-plugin/edit/master/README.md
repo: https://github.com/webpack-contrib/zopfli-webpack-plugin
---

Webpack 的 Node-Zopfli 插件.

## 安装

```bash
npm i -D zopfli-webpack-plugin
```

## 用法

``` javascript
var ZopfliPlugin = require("zopfli-webpack-plugin");
module.exports = {
  plugins: [
    new ZopfliPlugin({
      asset: "[path].gz[query]",
      algorithm: "zopfli",
      test: /\.(js|html)$/,
      threshold: 10240,
      minRatio: 0.8
    })
  ]
}
```

## 参数

* `asset`: 目标资源名。 `[file]` 被替换成原资源. `[path]` 被替换成原资源路径 `[query]` 替换成原查询字符串. 默认为 `"[path].gz[query]"`.
* `filename`: `function(asset)` 接受资源名 (被处理完之后的)，然后返回新的资源名。默认为 `false`.
* `algorithm`: 可以为 `function(buf, callback)` 也可以是字符串. 字符串来自 `zopfli`.
* `test`: 所有匹配正则表达式的资源会得到处理。默认为全部资源。
* `threshold`: 只有大于指定文件大小（size）的资源会得到处理。 单位为字节（bytes）。 默认为 `0`.
* `minRatio`: 只有资源压缩率（compress ratio）大于指定值的资源会得到处理。默认为 `0.8`.
* `deleteOriginalAssets`: 是否删除原资源。默认为 `false`.

## 可选参数

* `verbose`: 默认值: `false`,
* `verbose_more`: 默认值: `false`,
* `numiterations`: 默认值: `15`,
* `blocksplitting`: 默认值: `true`,
* `blocksplittinglast`: 默认值: `false`,
* `blocksplittingmax`: 默认值: `15`

## 维护人员

<table>
  <tbody>
    <tr>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars3.githubusercontent.com/u/166921?v=3&s=150">
        </br>
        <a href="https://github.com/bebraw">Juho Vepsäläinen</a>
      </td>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars2.githubusercontent.com/u/8420490?v=3&s=150">
        </br>
        <a href="https://github.com/d3viant0ne">Joshua Wiens</a>
      </td>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars3.githubusercontent.com/u/533616?v=3&s=150">
        </br>
        <a href="https://github.com/SpaceK33z">Kees Kluskens</a>
      </td>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars3.githubusercontent.com/u/3408176?v=3&s=150">
        </br>
        <a href="https://github.com/TheLarkInn">Sean Larkin</a>
      </td>
    </tr>
  <tbody>
</table>


[npm]: https://img.shields.io/npm/v/zopfli-webpack-plugin.svg
[npm-url]: https://npmjs.com/package/zopfli-webpack-plugin

[deps]: https://david-dm.org/webpack-contrib/zopfli-webpack-plugin.svg
[deps-url]: https://david-dm.org/webpack-contrib/zopfli-webpack-plugin

[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack

[test]: http://img.shields.io/travis/webpack-contrib/zopfli-webpack-plugin.svg
[test-url]: https://travis-ci.org/webpack-contrib/zopfli-webpack-plugin

[cover]: https://codecov.io/gh/webpack-contrib/zopfli-webpack-plugin/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/zopfli-webpack-plugin
