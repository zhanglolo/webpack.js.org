---
title: postcss-loader
source: https://raw.githubusercontent.com/postcss/postcss-loader/master/README.md
edit: https://github.com/postcss/postcss-loader/edit/master/README.md
repo: https://github.com/postcss/postcss-loader
---

  
  <p>用于使用 <a href="http://postcss.org/">PostCSS</a> 处理CSS的 <a href="http://webpack.js.org/">webpack</a> Loader</p>
</div>

## Install

```bash
npm i -D postcss-loader
```

## Usage

##

**postcss.config.js**
```js
module.exports = {
  parser: 'sugarss',
  plugins: {
    'postcss-import': {},
    'postcss-cssnext': {},
    'cssnano': {}
  }
}
```

您可以在[此处]((https://github.com/michael-ciniawsky/postcss-load-config))阅读有关常见 PostCSS 配置的详细信息。

### `Config Cascade`

您可以在不同的目录中使用不同的 `postcss.config.js` 文件。webpack 会从 `path.dirname(file)` 开始，并向上遍历文件树，直至找到配置文件。

```
|– components
| |– component
| | |– index.js
| | |– index.png
| | |– style.css (1)
| | |– postcss.config.js (1)
| |– component
| | |– index.js
| | |– image.png
| | |– style.css (2)
|
|– postcss.config.js (1 && 2 (recommended))
|– webpack.config.js
|
|– package.json
```

在配置 `postcss.config.js` 后，需要将 `postcss-loader` 添加到 `webpack.config.js` 中。 您可以单独使用它，也可以与 `css-loader` 一起使用（推荐）。在 `css-loader` 和 `style-loader` **之后**使用它，但如果使用其它预处理器 loader，例如 `sass|less|stylus-loader`，需要将 `postcss-loader` 放在其**之前**。

**webpack.config.js**
```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'postcss-loader' ]
      }
    ]
  }
}
```

> ⚠️ 当单独使用 `postcss-loader` （没有使用 `css-loader` ）时，不要在你的CSS中使用 `@import`，因为这会导致 bundle 文件非常臃肿

**webpack.config.js (recommended)**
```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'postcss-loader'
        ]
      }
    ]
  }
}
```

## Options

| 名称                       | 类型                | 默认值      | 描述                                     |
| :------------------------: | :-----------------: | :---------: | :--------------------------------------- |
| [`exec`](#exec)            | `{Boolean}`         | `undefined` | 在 `CSS-in-JS` 中启用 PostCSS 解析器支持 |
| [`parser`](#syntaxes)      | `{String\|Object}`  | `undefined` | 配置 PostCSS 解析器                      |
| [`syntax`](#syntaxes)      | `{String\|Object}`  | `undefined` | 配置 PostCSS 语法                        |
| [`stringifier`](#syntaxes) | `{String\|Object}`  | `undefined` | 配置 PostCSS Stringifier                 |
| [`config`](#config)        | `{Object}`          | `undefined` | 配置 `postcss.config.js` 路径 && `ctx`   |
| [`plugins`](#plugins)      | `{Array\|Function}` | `[]`        | 配置 PostCSS 插件                        |
| [`sourceMap`](#sourceMap)  | `{String\|Boolean}` | `false`     | 启用 Source Maps                         |

### `Exec`

如果你没有使用 [`postcss-js`][postcss-js] 解析器解析JS样式，需添加 `exec` 选项。

```js
{
  test: /\.style.js$/,
  use: [
    'style-loader',
    { loader: 'css-loader', options: { importLoaders: 1 } },
    { loader: 'postcss-loader', options: { parser: 'sugarss', exec: true } }
  ]
}
```

### `Config`

| 名称                  | 类型       | 默认值      | 描述                       |
| :-------------------: | :--------: | :---------: | :------------------------- |
| [`path`](#path)       | `{String}` | `undefined` | `postcss.config.js` 路径   |
| [`context`](#context) | `{Object}` | `undefined` | `postcss.config.js` 上下文 |

#### `Path`

您可以使用 `config.path` 选项手动指定配置文件（`postcss.config.js`）的路径。如果将配置存放在单独的 `./config || ./.config` 文件夹中，您需要配置此选项。

> ⚠️ 否则，**不需要**也**不**推荐配置此选项

**webpack.config.js**
```js
{
  loader: 'postcss-loader',
  options: {
    config: {
      path: 'path/to/postcss.config.js'
    }
  }
}
```

#### `Context (ctx)`

| 名称      | 类型       | 默认值                | 描述                             |
| :-------: | :--------: | :-------------------: | :------------------------------- |
| `env`     | `{String}` | `'development'`       | `process.env.NODE_ENV`           |
| `file`    | `{Object}` | `loader.resourcePath` | `extname`, `dirname`, `basename` |
| `options` | `{Object}` | `{}`                  | 选项                             |

`postcss-loader` 将上下文 `ctx` 暴露给配置文件，使你的 `postcss.config.js` 动态化，所以可以使用这一选项来实现一些神奇的功能 ✨

**postcss.config.js**
```js
module.exports = ({ file, options, env }) => ({
  parser: file.extname === '.sss' ? 'sugarss' : false,
  plugins: {
    'postcss-import': { root: file.dirname },
    'postcss-cssnext': options.cssnext ? options.cssnext : false,
    'autoprefixer': env == 'production' ? options.autoprefixer : false,
    'cssnano': env === 'production' ? options.cssnano : false
  }
})
```

**webpack.config.js**
```js
{
  loader: 'postcss-loader',
  options: {
    config: {
      ctx: {
        cssnext: {...options},
        cssnano: {...options},
        autoprefixer: {...options}
      }
    }
  }
}
```

### `Plugins`

**webpack.config.js**
```js
{
  loader: 'postcss-loader',
  options: {
    ident: 'postcss',
    plugins: (loader) => [
      require('postcss-import')({ root: loader.resourcePath }),
      require('postcss-cssnext')(),
      require('autoprefixer')(),
      require('cssnano')()
    ]
  }
}
```

> ⚠️ 当使用 `{Function}/require` 时，需要在 `options` 中配置一个标识符（`ident`）（复杂选项）。`ident` 可以自由命名，只要它是唯一的。建议命名为（`ident：'postcss'`）

### `Syntaxes`

| 名称                          | 类型                 | 默认值      | 描述                       |
| :---------------------------: | :------------------: | :---------: | :------------------------- |
| [`parser`](#parser)           | `{String\|Function}` | `undefined` | 自定义 PostCSS 解析器      |
| [`syntax`](#syntax)           | `{String\|Function}` | `undefined` | 自定义 PostCSS 语法        |
| [`stringifier`](#stringifier) | `{String\|Function}` | `undefined` | 自定义 PostCSS Stringifier |

#### `Parser`

**webpack.config.js**
```js
{
  test: /\.sss$/,
  use: [
    ...,
    { loader: 'postcss-loader', options: { parser: 'sugarss' } }
  ]
}
```

#### `Syntax`

**webpack.config.js**
```js
{
  test: /\.css$/,
  use: [
    ...,
    { loader: 'postcss-loader', options: { syntax: 'sugarss' } }
  ]
}
```

#### `Stringifier`

**webpack.config.js**
```js
{
  test: /\.css$/,
  use: [
    ...,
    { loader: 'postcss-loader', options: { stringifier: 'midas' } }
  ]
}
```

### `SourceMap`

启用 source map 支持，`postcss-loader` 将使用之前其他 loader 生成的 source map 并相应地更新，如果在 `postcss-loader` 之前没有应用 loader，则 `postcss-loader` 将为您生成 source map。

> :警告：如果之前使用了像 `sass-loader` 之类的 loader 并且设置了 `sourceMap` 选项，但是 `postcss-loader` 中的 `sourceMap` 选项被省略，之前的 source map 将被 `postcss-loader` **完全**丢弃。

**webpack.config.js**
```js
{
  test: /\.css/,
  use: [
    { loader: 'style-loader', options: { sourceMap: true } },
    { loader: 'css-loader', options: { sourceMap: true } },
    { loader: 'postcss-loader', options: { sourceMap: true } },
    { loader: 'sass-loader', options: { sourceMap: true } }
  ]
}
```

#### `'inline'`

您可以设置 `sourceMap：'inline'` 选项，将改动用注释标注，并内嵌到 CSS 文件中。

**webpack.config.js**
```js
{
  loader: 'postcss-loader',
  options: {
    sourceMap: 'inline'
  }
}
```

```css
.class { color: red; }

/*# sourceMappingURL=data:application/json;base64, ... */
```

## Examples

### `Stylelint`

**webpack.config.js**
```js
{
  test: /\.css$/,
  use: [
    'style-loader',
    'css-loader',
    {
      loader: 'postcss-loader',
      options: {
        ident: 'postcss',
        plugins: [
          require('postcss-import')(),
          require('stylelint')(),
          ...,
        ]
      }
    }
  ]
}
```

### `CSS Modules`

由于 `css-loader` 处理文件导入的方式，`postcss-loader` [不能直接使用][CSS 模块]。若要使用，需要配置 css-loader 的[`importLoaders`]选项。

**webpack.config.js**
```js
{
  test: /\.css$/,
  use: [
    'style-loader',
    { loader: 'css-loader', options: { modules: true, importLoaders: 1 } },
    'postcss-loader'
  ]
}
```

或使用[postcss-modules]代替 `css-loader`。

[`importLoaders`]: https://github.com/webpack-contrib/css-loader#importloaders
[cannot be used]: https://github.com/webpack/css-loader/issues/137
[CSS Modules]: https://github.com/webpack/css-loader#css-modules
[postcss-modules]: https://github.com/outpunk/postcss-modules

### `CSS-in-JS`

如果要处理用 JavaScript 编写的样式，请使用[postcss-js]解析器。

[postcss-js]: https://github.com/postcss/postcss-js

```js
{
  test: /\.style.js$/,
  use: [
    'style-loader',
    { loader: 'css-loader', options: { importLoaders: 2 } },
    { loader: 'postcss-loader', options: { parser: 'postcss-js' } },
    'babel-loader'
  ]
}
```

因此，您将能够以下列方式编写样式

```js
import colors from './styles/colors'

export default {
    '.menu': {
      color: colors.main,
      height: 25,
      '&_link': {
      color: 'white'
    }
  }
}
```

> :警告：如果您使用 Babel，则需要执行以下操作才能使设置生效

> 1. 在配置中添加 [babel-plugin-add-module-exports]
> 2. 每个样式模块只能有一个**default**导出

[babel-plugin-add-module-exports]: https://github.com/59naga/babel-plugin-add-module-exports

### [Extract CSS][ExtractPlugin]

[ExtractPlugin]: https://github.com/webpack-contrib/extract-text-webpack-plugin

**webpack.config.js**
```js
const ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: [
            { loader: 'css-loader', options: { importLoaders: 1 } },
            'postcss-loader'
          ]
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin('[name].css')
  ]
}
```

## 维护人员

<table>
  <tbody>
    <tr>
      <td align="center">
        <a href="https://github.com/michael-ciniawsky">
          <img width="150" height="150" src="https://github.com/michael-ciniawsky.png?v=3&s=150">
          </br>
          Michael Ciniawsky
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/evilebottnawi">
          <img width="150" height="150" src="https://github.com/evilebottnawi.png?v=3&s=150">
          </br>
          Alexander Krasnoyarov
        </a>
      </td>
    </tr>
  <tbody>
</table>


[npm]: https://img.shields.io/npm/v/postcss-loader.svg
[npm-url]: https://npmjs.com/package/postcss-loader

[node]: https://img.shields.io/node/v/postcss-loader.svg
[node-url]: https://nodejs.org

[deps]: https://david-dm.org/postcss/postcss-loader.svg
[deps-url]: https://david-dm.org/postcss/postcss-loader

[tests]: http://img.shields.io/travis/postcss/postcss-loader.svg
[tests-url]: https://travis-ci.org/postcss/postcss-loader

[cover]: https://coveralls.io/repos/github/postcss/postcss-loader/badge.svg
[cover-url]: https://coveralls.io/github/postcss/postcss-loader

[chat]: https://badges.gitter.im/postcss/postcss.svg
[chat-url]: https://gitter.im/postcss/postcss
