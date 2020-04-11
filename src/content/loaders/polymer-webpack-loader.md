---
title: polymer-webpack-loader
source: https://raw.githubusercontent.com/webpack-contrib/polymer-webpack-loader/master/README.md
edit: https://github.com/webpack-contrib/polymer-webpack-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/polymer-webpack-loader
---

[![npm version](https://badge.fury.io/js/polymer-webpack-loader.svg)](https://badge.fury.io/js/polymer-webpack-loader)
[![build status](https://travis-ci.org/webpack-contrib/polymer-webpack-loader.svg?branch=master)](https://travis-ci.org/webpack-contrib/polymer-webpack-loader)

> [Polymer](https://www.polymer-project.org/) component loader for [webpack](https://webpack.docschina.org/).

polymer-webpack-loader 允许编写混合的 HTML, CSS 和 JavaScript Polymer 元素，并仍然使用完整的 webpack 生态系统，包括模块捆绑和代码拆分。

<img width="1024" alt="" src="https://user-images.githubusercontent.com/1066253/28131928-3b257288-66f0-11e7-8295-cb968cefb040.png">

可以转换组件:

- `<link rel="import" href="./my-other-element.html">` -> `import './my-other-element.html';`
- `<dom-module>` 成为在运行时注册的字符串
- `<script src="./other-script.js"></script>` -> `import './other-script.js';`
- `<script>/* contents */</script>` -> `/* contents */`

这意味着什么呢?

任何不是**外部链接**且不以 `~`, `/`, `./` 或一系列 `../` 开头的 `<link>` "href" or `<script>` "src", 都会在其开头添加一个 `./`。为了防止这种变化，使用下面的忽略链接选项。

## Path Translations

| `tag`                                                     | `import`                                |
| --------------------------------------------------------- | --------------------------------------- |
| `<link rel="import" href="path/to/some-element.html">`    | `import "./path/to/some-element.html"`  |
| `<link rel="import" href="/path/to/some-element.html">`   | `import "/path/to/some-element.html"`   |
| `<link rel="import" href="../path/to/some-element.html">` | `import "../path/to/some-element.html"` |
| `<link rel="import" href="./path/to/some-element.html">`  | `import "./path/to/some-element.html"`  |
| `<link rel="import" href="~path/to/some-element.html">`   | `import "~path/to/some-element.html"`   |

## Configuring the Loader

```javascript
{
  test: /\.html$/,
  include: Condition(s) (optional),
  exclude: Condition(s) (optional),
  options: {
    ignoreLinks: Condition(s) (optional),
    ignorePathReWrite: Condition(s) (optional),
    processStyleLinks: Boolean (optional),
    htmlLoader: Object (optional)
  },
  loader: 'polymer-webpack-loader'
},
```

### include: Condition(s)

请参阅 webpack 文档中的 [Rule.include]和 [Condition]。与 include 匹配的路径将由 polymer-webpack-loader 处理。警告：如果此属性存在，polymer-webpack-loader 将只处理符合给定条件的文件。如果你的组件有一个 `<link>` 指向另一个组件（例如在另一个目录中），那么这个 include 条件也**必须**匹配该目录。

[rule.include]: https://webpack.docschina.org/configuration/module/#rule-include
[condition]: https://webpack.docschina.org/configuration/module/#condition

### exclude: Condition(s)

请参阅 webpack 文档中的 [Rule.exclude] 和 [Condition]。与 exclude 匹配的路径将被 polymer-webpack-loader 忽略。注：通过 `<link>` 导入的文件将不会被排除。请查看 `Options.ignoreLinks`。

[rule.exclude]: https://webpack.docschina.org/configuration/module/#rule-exclude

### 选项

#### ignoreLinks: Condition(s)

如果 `<link>` 的指向符合 webpack 的 [Condition]，将不会转换为 `import`。

#### ignorePathReWrite: Condition(s)

如果 `<link>` 的路径匹配 webpack 中的 [Condition]，那么在转换为 `import` 时不会发生改变。这对于遵守别名规则，loader 句法（例如 `markup-inline-loader!./my-element.html`）或模块路径都有用处。

#### processStyleLinks Boolean

设置为 true 时，位于 dom 模块内部的 `<link import="css" href="...">` 或者 `<link rel="stylesheet" href="...">` 将被重写为 `<style>require('...')</style>`。这将允许文件由 webpack 配置中设置的 loader 来处理它们的文件类型。

1. 任何在 `<dom-module>` 中但没有包含在 `<template>` 中的 `<link>` 都会被添加到 `<template>` 标签中。
2. 只有当 `<link>` 的 href 是相对路径时，loader 才会执行替换。任何以 `http`, `https` 或 `//` 开头的链接都不会被替换。

```html
<dom-module>
  <link rel="stylesheet" href="file1.css" />
  <template>
    <link rel="stylesheet" href="file2.css" />
  </template>
</dom-module>

会生成如下代码

<dom-module>
  <template>
    <style>
      require('file1.css')
    </style>
    <style>
      require('file2.css')
    </style>
  </template>
</dom-module>
```

#### htmlLoader: Object

传递给 html-loader 的选项，请参阅 [html-loader](/loaders/html-loader/)。

### Use with Babel (or other JS transpilers)

如需转译 `<script>` 块的内容，可以参考 [chain an additional loader](https://webpack.docschina.org/configuration/module/#rule-use)。

```js
module: {
  loaders: [
    {
      test: /\.html$/,
      use: [
        // Chained loaders are applied last to first
        { loader: "babel-loader" },
        { loader: "polymer-webpack-loader" }
      ]
    }
  ];
}

// alternative syntax

module: {
  loaders: [
    {
      test: /\.html$/,
      // Chained loaders are applied right to left
      loader: "babel-loader!polymer-webpack-loader"
    }
  ];
}
```

### Use of HtmlWebpackPlugin

出现 polymer-webpack-loader 冲突时，该如何配置 HtmlWebpackPlugin，请看示例：

```javascript
{
  test: /\.html$/,
  loader: 'html-loader',
  include: [
    path.resolve(__dirname, './index.html'),
  ],
},
{
  test: /\.html$/,
  loader: 'polymer-webpack-loader'
}
```

根据 html-loader 使用的进程，将 index.html 文件暴露给 polymer-webpack-loader。这种情况需要从 polymer-webpack-loader 中排除 html 文件，或者寻找其他方法来避免冲突。请参阅：[html-webpack-plugin 模板选项](https://github.com/jantimon/html-webpack-plugin/blob/master/docs/template-option.md)

## Shimming（匀场模块）

并非所有聚合物元素都要被编写为模块才能执行，也并非所有聚合物元素需要更改才能使用 webpack。最常见的问题是模块不在全局范围内执行。变量，函数和类将不再是全局的，除非它们被声明为全局对象 (window) 上的属性。

```js
class MyElement {} // I'm not global anymore
window.myElement = MyElement; // Now I'm global again
```

- Use the [exports-loader](https://webpack.docschina.org/guides/shimming/#exports-loader) to
  add a module export to components which expect a symbol to be global.
- Use the [imports-loader](https://webpack.docschina.org/guides/shimming/#imports-loader) when a script
  expects the `this` keyword to reference `window`.
- Use the [ProvidePlugin](https://webpack.docschina.org/guides/shimming/#provideplugin) to add a module
  import statement when a script expects a variable to be globally defined (but is now a module export).
- Use the [NormalModuleReplacementPlugin](https://webpack.docschina.org/plugins/normal-module-replacement-plugin/)
  to have webpack swap a module-compliant version for a script.

You may need to apply multiple shimming techniques to the same component.

对于外部库代码，webpack 提供 [匀场选项 shimming options](https://webpack.docschina.org/guides/shimming/)。

使用 exports-loader 将模块导出添加到期望符号为全局的组件。
脚本需要 this 引用关键字时使用 imports-loader window。
当脚本需要全局定义变量（但现在是模块导出）时，使用 ProvidePlugin 添加模块导入语句。
使用 NormalModuleReplacementPlugin 让 webpack 交换脚本的模块兼容版本。
您可能需要将多种匀场技术应用于相同的组件。

## Boostrapping Your Application

The webcomponent polyfills must be added in a specific order. If you do not delay loading the main bundle with your components, you will see the following exceptions in the browser console:

```
Uncaught TypeError: Failed to construct 'HTMLElement': Please use the 'new' operator, this DOM object constructor cannot be called as a function.
```

Reference the [demo html file](https://github.com/webpack-contrib/polymer-webpack-loader/blob/master/demo/src/index.ejs)
for the proper loading sequence.

## Maintainers

<table>
  <tbody>
    <tr>
      <td align="center">
        <a href="https://github.com/bryandcoulter">
          <img width="150" height="150" src="https://avatars.githubusercontent.com/u/18359726?v=3">
          </br>
          Bryan Coulter
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/ChadKillingsworth">
          <img width="150" height="150" src="https://avatars.githubusercontent.com/u/1247639?v=3">
          </br>
          Chad Killingsworth
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/robdodson">
          <img width="150" height="150" src="https://avatars.githubusercontent.com/u/1066253?v=3">
          </br>
          Rob Dodson
        </a>
      </td>
    </tr>
  <tbody>
</table>
