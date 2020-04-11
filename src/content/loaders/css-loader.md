---
title: css-loader
source: https://raw.githubusercontent.com/webpack-contrib/css-loader/master/README.md
edit: https://github.com/webpack-contrib/css-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/css-loader
---


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]



The `css-loader` interprets `@import` and `url()` like `import/require()` and will resolve them.

## 起步

To begin, you'll need to install `css-loader`:

```console
npm install --save-dev css-loader
```

Then add the plugin to your `webpack` config. For example:

**file.js**

```js
import css from 'file.css';
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
};
```

对于这些引用资源，你应该在配置中指定的，比较合适的 loader 是 [file-loader](/loaders/file-loader/) 和 [url-loader](/loaders/url-loader/)（查看[如下设置](https://github.com/webpack-contrib/css-loader#assets)）。

And run `webpack` via your preferred method.

### `toString`

你也可以直接将 css-loader 的结果作为字符串使用，例如 Angular 的组件样式。

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['to-string-loader', 'css-loader'],
      },
    ],
  },
};
```

或者

```js
const css = require('./test.css').toString();

console.log(css); // {String}
```

如果有 SourceMap，它们也将包含在字符串结果中。

如果由于某种原因，你需要将 CSS 提取为纯粹的字符串资源
（即不包含在 JS 模块中），
则可能需要查看 [extract-loader](https://github.com/peerigon/extract-loader)。
例如，当你需要将 CSS 作为字符串进行后处理时，这很有用。

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'handlebars-loader', // handlebars loader expects raw resource string
          'extract-loader',
          'css-loader',
        ],
      },
    ],
  },
};
```

## 选项

|名称|类型|默认值|描述|
|:--:|:--:|:-----:|:----------|
|**[`url`](#url)**|`{Boolean}`|`true`| 启用/禁用 `url()` 处理|
|**[`import`](#import)** |`{Boolean}`|`true`| 启用/禁用 @import 处理|
|**[`modules`](#modules)**|`{Boolean}`|`false`|启用/禁用 CSS 模块|
|**[`localIdentName`](#localidentname)**|`{String}`|`[hash:base64]`|配置生成的标识符(ident)|
|**[`sourceMap`](#sourcemap)**|`{Boolean}`|`false`|启用/禁用 Sourcemap|
|**[`camelCase`](#camelcase)**|`{Boolean\|String}`|`false`|以驼峰化式命名导出类名|
|**[`importLoaders`](#importloaders)**|`{Number}`|`0`|在 css-loader 前应用的 loader 的数量|

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          url: (url, resourcePath) => {
            // resourcePath - path to css file

            // `url()` with `img.png` stay untouched
            return url.includes('img.png');
          },
        },
      },
    ],
  },
};
```

### `import`

类型：`Boolean`
默认：`true`

Control `@import` resolving. Absolute urls in `@import` will be moved in runtime code.

Examples resolutions:

```
@import 'style.css' => require('./style.css')
@import url(style.css) => require('./style.css')
@import url('style.css') => require('./style.css')
@import './style.css' => require('./style.css')
@import url(./style.css) => require('./style.css')
@import url('./style.css') => require('./style.css')
@import url('http://dontwritehorriblecode.com/style.css') => @import url('http://dontwritehorriblecode.com/style.css') in runtime
```

### `alias`

用别名重写你的 URL，在难以改变输入文件的url 路径时，这会很有帮助，例如，当你使用另一个包(package)（如 bootstrap, ratchet, font-awesome 等）中一些 css/sass 文件。

`css-loader` 的别名，遵循与webpack 的 `resolve.alias` 相同的语法，你可以在[resolve 文档](https://webpack.docschina.org/configuration/resolve/#resolve-alias) 查看细节

**file.scss**
```css
@charset "UTF-8";
@import "bootstrap";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          import: (parsedImport, resourcePath) => {
            // parsedImport.url - url of `@import`
            // parsedImport.media - media query of `@import`
            // resourcePath - path to css file

            // `@import` with `style.css` stay untouched
            return parsedImport.url.includes('style.css');
          },
        },
      },
    ],
  },
};
```

### [`modules`](https://github.com/css-modules/css-modules)

类型：`Boolean|String`
默认：`false`

The `modules` option enables/disables the **CSS Modules** spec and setup basic behaviour.

|      Name      |    Type     | Description                                                                                                                      |
| :------------: | :---------: | :------------------------------------------------------------------------------------------------------------------------------- |
|   **`true`**   | `{Boolean}` | Enables local scoped CSS by default (use **local** mode by default)                                                              |
|  **`false`**   | `{Boolean}` | Disable the **CSS Modules** spec, all **CSS Modules** features (like `@value`, `:local`, `:global` and `composes`) will not work |
| **`'local'`**  | `{String}`  | Enables local scoped CSS by default (same as `true` value)                                                                       |
| **`'global'`** | `{String}`  | Enables global scoped CSS by default                                                                                             |

Using `false` value increase performance because we avoid parsing **CSS Modules** features, it will be useful for developers who use vanilla css or use other technologies.

You can read about **modes** below.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          modules: true,
        },
      },
    ],
  },
};
```

##### `Scope`

Using `local` value requires you to specify `:global` classes.
Using `global` value requires you to specify `:local` classes.

You can find more information [here](https://github.com/css-modules/css-modules).

Styles can be locally scoped to avoid globally scoping styles.

语法 `:local(.className)` 可以被用来在局部作用域中声明 `className`。局部的作用域标识符会以模块形式暴露出去。

使用 `:local`（无括号）可以为此选择器启用局部模式。
`:global(.className)` 可以用来声明一个明确的全局选择器。
使用 `:global`（无括号）可以将此选择器切换至全局模式。

loader 会用唯一的标识符(identifier)来替换局部选择器。所选择的唯一标识符以模块形式暴露出去。

```css
:local(.className) {
  background: red;
}
:local .className {
  color: green;
}
:local(.className .subClass) {
  color: green;
}
:local .className .subClass :global(.global-class-name) {
  color: blue;
}
```

```css
._23_aKvs-b8bW2Vg3fwHozO {
  background: red;
}
._23_aKvs-b8bW2Vg3fwHozO {
  color: green;
}
._23_aKvs-b8bW2Vg3fwHozO ._13LGdX8RMStbBE9w-t0gZ1 {
  color: green;
}
._23_aKvs-b8bW2Vg3fwHozO ._13LGdX8RMStbBE9w-t0gZ1 .global-class-name {
  color: blue;
}
```

> ℹ️ 标识符被导出

```js
exports.locals = {
  className: '_23_aKvs-b8bW2Vg3fwHozO',
  subClass: '_13LGdX8RMStbBE9w-t0gZ1',
};
```

建议局部选择器使用驼峰式。它们在导入 JS 模块中更容易使用。

你可以使用 `:local(#someId)`，但不推荐这种用法。推荐使用 class 代替 id。

#### `Composing`

当声明一个局部类名时，你可以与另一个局部类名组合为一个局部类。

```css
:local(.className) {
  background: red;
  color: yellow;
}

:local(.subClass) {
  composes: className;
  background: blue;
}
```

这不会导致 CSS 本身的任何更改，而是导出多个类名。

```js
exports.locals = {
  className: '_23_aKvs-b8bW2Vg3fwHozO',
  subClass: '_13LGdX8RMStbBE9w-t0gZ1 _23_aKvs-b8bW2Vg3fwHozO',
};
```

```css
._23_aKvs-b8bW2Vg3fwHozO {
  background: red;
  color: yellow;
}

._13LGdX8RMStbBE9w-t0gZ1 {
  background: blue;
}
```

##### `Importing`

从其他模块导入局部类名。

```css
:local(.continueButton) {
  composes: button from 'library/button.css';
  background: red;
}
```

```css
:local(.nameEdit) {
  composes: edit highlight from './edit.css';
  background: red;
}
```

要从多个模块导入，请使用多个 `composes:` 规则。

```css
:local(.className) {
  composes: edit hightlight from './edit.css';
  composes: button from 'module/button.css';
  composes: classFromThisModule;
  background: red;
}
```

### `localIdentName`

 你可以使用 localIdentName 查询参数来配置生成的 ident。 可以在 [loader-utils 文档](https://github.com/webpack/loader-utils#interpolatename) 查看更多信息。

 **webpack.config.js**
```js
{
  test: /\.css$/,
  use: [
    {
      loader: 'css-loader',
      options: {
        modules: true,
        localIdentName: '[path][name]__[local]--[hash:base64:5]'
      }
    }
  ]
}
```

你还可以通过自定义 getLocalIdent 函数来指定绝对路径，以根据不同的模式(schema)生成类名。这需要 webpack >= 2.2.1（options 对象支持传入函数）。

**webpack.config.js**

```js
{
  loader: 'css-loader',
  options: {
    modules: true,
    localIdentName: '[path][name]__[local]--[hash:base64:5]',
    getLocalIdent: (context, localIdentName, localName, options) => {
      return 'whatever_random_class_name'
    }
  }
}
```

> ℹ️ 对于使用 extract-text-webpack-plugin 预渲染，你应该在预渲染 bundle 中 使用 css-loader/locals 而不是 style-loader!css-loader 。它不会嵌入 CSS，但只导出标识符映射(identifier map)。

### `sourceMap`

类型：`Boolean`
默认：`false`

设置 `sourceMap` 选项查询参数来引入 source map。

例如，`mini-css-extract-plugin` 能够处理它们。

默认情况下不启用它们，因为它们会导致运行时的额外开销，并增加了 bundle 大小 (JS source map 不会)。此外，相对路径是错误的，你需要使用包含服务器 URL 的绝对公用路径。

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          sourceMap: true,
        },
      },
    ],
  },
};
```

### `camelCase`

类型：`Boolean|String`
默认：`false`

默认情况下，导出 JSON 键值对形式的类名。如果想要驼峰化(camelize)类名（有助于在 JS 中使用），通过设置 css-loader 的查询参数 `camelCase` 即可实现。

|名称|类型|描述|
|:--:|:--:|:----------|
|**`false`**|`{Boolean}`|Class names will be camelized, the original class name will not to be removed from the locals|
|**`true`**|`{Boolean}`|类名将被骆驼化|
|**`'dashes'`**|`{String}`|只有类名中的破折号将被骆驼化|
|**`'only'`** |`{String}`|在 `0.27.1` 中加入。类名将被骆驼化，初始类名将从局部移除|
|**`'dashesOnly'`**|`{String}`|在 `0.27.1` 中加入。类名中的破折号将被骆驼化，初始类名将从局部移除|

**file.css**

```css
.class-name {
}
```

**file.js**

```js
import { className } from 'file.css';
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          camelCase: true,
        },
      },
    ],
  },
};
```

### `importLoaders`

类型：`Number`
默认：`0`

查询参数 `importLoaders`，用于配置「`css-loader` 作用于 `@import` 的资源之前」有多少个 loader。

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          {
            loader: 'css-loader',
            options: {
              importLoaders: 2, // 0 => no loaders (default); 1 => postcss-loader; 2 => postcss-loader, sass-loader
            },
          },
          'postcss-loader',
          'sass-loader',
        ],
      },
    ],
  },
};
```

在模块系统（即 webpack）支持原始 loader 匹配后，此功能可能在将来会发生变化。

### `exportOnlyLocals`

类型：`Boolean`
默认：`false`

Export only locals (**useful** when you use **css modules**).
For pre-rendering with `mini-css-extract-plugin` you should use this option instead of `style-loader!css-loader` **in the pre-rendering bundle**.
It doesn't embed CSS but only exports the identifier mappings.

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
        options: {
          exportOnlyLocals: true,
        },
      },
    ],
  },
};
```

## 示例

对于生产环境构建，建议从 bundle 中提取 CSS，以便之后可以并行加载 CSS/JS 资源。
可以通过使用 [extract-text-webpack-plugin](https://github.com/webpack-contrib/extract-text-webpack-plugin) 来实现，在生产环境模式运行中提取 CSS。

### 提取

对于生产环境构建，建议从 bundle 中提取 CSS，以便之后可以并行加载 CSS/JS 资源。

- 可以通过使用 [mini-css-extract-plugin](/plugins/mini-css-extract-plugin/) 来实现，在生产环境模式运行中提取 CSS。

- As an alternative, if seeking better development performance and css outputs that mimic production. [extract-css-chunks-webpack-plugin](https://github.com/faceyspacey/extract-css-chunks-webpack-plugin) offers a hot module reload friendly, extended version of mini-css-extract-plugin. HMR real CSS files in dev, works like mini-css in non-dev

## 贡献

Please take a moment to read our contributing guidelines if you haven't yet done so.

[贡献指南](https://raw.githubusercontent.com/webpack-contrib/css-loader/master/.github/CONTRIBUTING.md)

## License

[MIT](https://raw.githubusercontent.com/webpack-contrib/css-loader/master/LICENSE)

[npm]: https://img.shields.io/npm/v/css-loader.svg
[npm-url]: https://npmjs.com/package/css-loader
[node]: https://img.shields.io/node/v/css-loader.svg
[node-url]: https://nodejs.org
[deps]: https://david-dm.org/webpack-contrib/css-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/css-loader
[tests]: https://img.shields.io/circleci/project/github/webpack-contrib/css-loader.svg
[tests-url]: https://circleci.com/gh/webpack-contrib/css-loader
[cover]: https://codecov.io/gh/webpack-contrib/css-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/css-loader
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=css-loader
[size-url]: https://packagephobia.now.sh/result?p=css-loader
