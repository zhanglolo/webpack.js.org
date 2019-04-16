---
title: less-loader
source: https://raw.githubusercontent.com/webpack-contrib/less-loader/master/README.md
edit: https://github.com/webpack-contrib/less-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/less-loader
---

<p align="center">Compiles Less to CSS.</p>

使用 [css-loader](/loaders/css-loader/) 或 [raw-loader](/loaders/raw-loader/) 将其转换为 JS 模块，然后使用 [ExtractTextPlugin](/plugins/extract-text-webpack-plugin/) 将其提取到单独的文件中。

## 安装

```bash
npm install --save-dev less-loader less
```

less-loader 的 [`peerDependency`](https://docs.npmjs.com/files/package.json#peerdependencies)(同版本依赖) 是 [less](https://github.com/less/less.js) 。因此，可以实现精准版本控制。

## 示例

将 [css-loader](https://github.com/webpack-contrib/css-loader)、[style-loader](https://github.com/webpack-contrib/style) 和 less-loader 链式调用，可以把所有样式立即应用于 DOM。

```js
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.less$/,
            use: [{
                loader: "style-loader" // creates style nodes from JS strings
            }, {
                loader: "css-loader" // translates CSS into CommonJS
            }, {
                loader: "less-loader" // compiles Less to CSS
            }]
        }]
    }
};
```

可以通过 [loader options](https://webpack.docschina.org/configuration/module/#rule-options-rule-query) 将任何 Less 特定选项传递给 less-loader。请参阅 [Less documentation](http://lesscss.org/usage/#command-line-usage-options) 文档以查看 dash-case(连接符命名) 的所有可用选项。由于我们将这些选项以编程方式传递给 Less，所以您需要在这里使用 camelCase(驼峰命名) 传递这些选项:

```js
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.less$/,
            use: [{
                loader: "style-loader"
            }, {
                loader: "css-loader"
            }, {
                loader: "less-loader", options: {
                    strictMath: true,
                    noIeCompat: true
                }
            }]
        }]
    }
};
```

遗憾的是，Less 不会将所有选项一对一地映射到 camelCase。如有疑问，[请检查其可执行文件并搜索 dash-case(连接符命名)选项](https://github.com/less/less.js/blob/3.x/bin/lessc)。

##

通常，建议使用 [ExtractTextPlugin](https://github.com/webpack-contrib/extract-text-webpack-plugin) 将样式表提取到生产中的专用文件中。这样样式即可无需依赖 JavaScript:

```js
const ExtractTextPlugin = require("extract-text-webpack-plugin");

const extractLess = new ExtractTextPlugin({
    filename: "[name].[contenthash].css",
    disable: process.env.NODE_ENV === "development"
});

module.exports = {
    ...
    module: {
        rules: [{
            test: /\.less$/,
            use: extractLess.extract({
                use: [{
                    loader: "css-loader"
                }, {
                    loader: "less-loader"
                }],
                // use style-loader in development
                fallback: "style-loader"
            })
        }]
    },
    plugins: [
        extractLess
    ]
};
```

## Usage

### 引入

从 less-loader 4 开始，您现在可以选择 Less 的内置解析器和 webpackt 的解析器。默认情况下，使用 webpackt 的解析器。

#### webpack resolver

webpack 提供了一种[解析文件的高级机制](https://webpack.docschina.org/configuration/resolve/)。less-loader 应用一个 Less 插件，并且将所有查询参数传递给 webpack resolver。所以，你可以从 `node_modules` 导入你的 less 模块。只要加一个 `~` 前缀，告诉 webpack 去查询[`模块`](https://webpack.docschina.org/configuration/resolve/#resolve-modules)。

```css
@import "~bootstrap/less/bootstrap";
```

重要的是只使用 `~` 前缀，因为 `~/` 会解析为主目录。webpack 需要区分`bootstrap`和`〜bootstrap`，因为 CSS 和 Less 文件没有用于导入相对文件的特殊语法。`@import "file"` 与 `@import "./file";` 写法相同

##### Non-Less imports

使用 webpack resolver，你可以引入任何文件类型。你只需要一个导出有效的 Less 代码的 loader。通常，你还需要设置 `issuer` 条件，以确保此规则仅适用于 Less 文件的导入：

```js
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.js$/,
            issuer: /\.less$/,
            use: [{
                loader: "js-to-less-loader"
            }]
        }]
    }
};
```

#### Less 解析器

如果明确声明了 `paths` 选项，less-loader 则不会使用 webpack 的解析器。在本地文件夹中无法解析的模块将在给定的内容中搜索 `paths`。这是 Less 的默认行为。`paths` 应该是一个拥有绝对路径的数组：

```js
// webpack.config.js
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.less$/,
            use: [{
                loader: "style-loader"
            }, {
                loader: "css-loader"
            }, {
                loader: "less-loader", options: {
                    paths: [
                        path.resolve(__dirname, "node_modules")
                    ]
                }
            }]
        }]
    }
};
```

在这种情况下，所有的 webpack 功能，如导入 non-Less 文件或别名，都将失效。

### 插件

为了使用[插件](http://lesscss.org/usage/#plugins)，只需像下面这样简单设置 `plugins` 选项：

```js
// webpack.config.js
const CleanCSSPlugin = require("less-plugin-clean-css");

module.exports = {
    ...
            {
                loader: "less-loader", options: {
                    plugins: [
                        new CleanCSSPlugin({ advanced: true })
                    ]
                }
            }]
    ...
};
```

### 提取样式表

- [extract-loader](https://github.com/peerigon/extract-loader) (simpler, but specialized on the css-loader's output)
- [extract-text-webpack-plugin](https://github.com/webpack-contrib/extract-text-webpack-plugin) (more complex, but works in all use-cases)

将 CSS 与 webpack 捆绑在一起好处多多，如在开发环境中使用哈希 url 或 [hot module replacement](http://webpack.github.io/docs/hot-module-replacement-with-webpack.html) 引用图像和字体。另一方面，在生产环境中，根据 JS 的执行情况应用样式表并非最佳选择。因为渲染可能会延迟，甚至导致 [FOUC](https://en.wikipedia.org/wiki/Flash_of_unstyled_content) -- Flash of unstyled content (文档样式短暂失效)。因此，在最终的生产构建中，最好将它们作为单独的文件保存。

有两种方式可以从 bundle 中提取样式表：

- [extract-loader](https://github.com/peerigon/extract-loader)（简单，但仅用于 css-loader 的输出）
- [extract-text-webpack-plugin](https://github.com/webpack-contrib/extract-text-webpack-plugin)（复杂，但适用于所有使用情况）

### Source maps

要启用 CSS 的 source map，你需要将 `sourceMap` 选项传递给 _less-loader_ 和 _css-loader_。所以你的 `webpack.config.js' 应该是这样：

```javascript
module.exports = {
    ...
    module: {
        rules: [{
            test: /\.less$/,
            use: [{
                loader: "style-loader"
            }, {
                loader: "css-loader", options: {
                    sourceMap: true
                }
            }, {
                loader: "less-loader", options: {
                    sourceMap: true
                }
            }]
        }]
    }
};
```

Also checkout the [sourceMaps example](https://github.com/webpack-contrib/less-loader/tree/master/examples/sourceMaps).

如果你要编辑 Chrome 中的原始 Less 文件，[这里有一个很好的博客文章](https://medium.com/@toolmantim/getting-started-with-css-sourcemaps-and-in-browser-sass-editing-b4daab987fb0)。此博客文章是关于 Sass 的，但它也适用于 Less。

### CSS modules gotcha

语句中有关 Less 和 [CSS modules](https://github.com/css-modules/css-modules) 的相关文件路径存在已知问题 `url(...)`。[请参阅此问题以获取解释](https://github.com/webpack-contrib/less-loader/issues/109#issuecomment-253797335)。

## 维护人员

<table>
    <tr>
      <td align="center">
        <a href="https://github.com/jhnns"><img width="150" height="150" src="https://github.com/jhnns.png?s=150"></a>
        <br>
        <a href="https://github.com/jhnns">Johannes Ewald</a>
      </td>
    <tr>
</table>

[npm]: https://img.shields.io/npm/v/less-loader.svg
[npm-stats]: https://img.shields.io/npm/dm/less-loader.svg
[npm-url]: https://npmjs.com/package/less-loader
[node]: https://img.shields.io/node/v/less-loader.svg
[node-url]: https://nodejs.org
[deps]: https://david-dm.org/webpack-contrib/less-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/less-loader
[travis]: http://img.shields.io/travis/webpack-contrib/less-loader.svg
[travis-url]: https://travis-ci.org/webpack-contrib/less-loader
[appveyor-url]: https://ci.appveyor.com/project/jhnns/less-loader/branch/master
[appveyor]: https://ci.appveyor.com/api/projects/status/github/webpack-contrib/less-loader?svg=true
[coverage]: https://img.shields.io/codecov/c/github/webpack-contrib/less-loader.svg
[coverage-url]: https://codecov.io/gh/webpack-contrib/less-loader
[chat]: https://badges.gitter.im/webpack-contrib/webpack.svg
[chat-url]: https://gitter.im/webpack-contrib/webpack
