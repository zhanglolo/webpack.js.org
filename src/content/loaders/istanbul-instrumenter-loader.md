---
title: istanbul-instrumenter-loader
source: https://raw.githubusercontent.com/webpack-contrib/istanbul-instrumenter-loader/master/README.md
edit: https://github.com/webpack-contrib/istanbul-instrumenter-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/istanbul-instrumenter-loader
---

Instrument JS files with [istanbul-lib-instrument](https://github.com/istanbuljs/istanbuljs/tree/master/packages/istanbul-lib-instrument) for subsequent code coverage reporting

使用 [istanbul-lib-instrument](https://github.com/istanbuljs/istanbuljs/tree/master/packages/istanbul-lib-instrument) 可以检测 JS 文件随后的代码覆盖率报告

## 安装

```bash
npm i -D istanbul-instrumenter-loader
```

## <a href="https://webpack.js.org/concepts/loaders">用法</a>

##

- [karma-webpack](https://github.com/webpack/karma-webpack)
- [karma-coverage-istanbul-reporter](https://github.com/mattlewis92/karma-coverage-istanbul-reporter)

### `结构`

```
├─ src
│ |– components
│ | |– bar
│ | │ |─ index.js
│ | |– foo/
│     |– index.js
|– test
| |– src
| | |– components
| | | |– foo
| | | | |– index.js
```

为生成所有组件（包括你没写测试的那些）的代码覆盖率报告，你需要 require 所有**业务**和**测试**的代码。相关内容在 [karma-webpack 其他用法](https://github.com/webpack/karma-webpack#alternative-usage)中有涉及

**test/index.js**

```js
// requires 所有在 `project/test/src/components/**/index.js` 中的测试
const tests = require.context("./src/components/", true, /index\.js$/);

tests.keys().forEach(tests);

// requires 所有在 `project/src/components/**/index.js` 中的组件
const components = require.context("../src/components/", true, /index\.js$/);

components.keys().forEach(components);
```

> ℹ️ 以下为 `karma` 的唯一`入口`起点文件

**karma.conf.js**

```js
config.set({
  ...
  files: [
    'test/index.js'
  ],
  preprocessors: {
    'test/index.js': 'webpack'
  },
  webpack: {
    ...
    module: {
      rules: [
        // 用 Istanbul 只监测业务代码
        {
          test: /\.js$/,
          use: { loader: 'istanbul-instrumenter-loader' },
          include: path.resolve('src/components/')
        }
      ]
    }
    ...
  },
  reporters: [ 'progress', 'coverage-istanbul' ],
  coverageIstanbulReporter: {
    reports: [ 'text-summary' ],
    fixWebpackSourcePaths: true
  }
  ...
});
```

### 使用 `Babel`

须将该检测作为后续步骤运行

**webpack.config.js**

```js
{
  test: /\.js$|\.jsx$/,
  use: {
    loader: 'istanbul-instrumenter-loader',
    options: { esModules: true }
  },
  enforce: 'post',
  exclude: /node_modules|\.spec\.js$/,
}
```

## <a href="https://github.com/istanbuljs/istanbuljs/blob/master/packages/istanbul-lib-instrument/api.md#instrumenter">Options</a>

此 loader 支持 `istanbul-lib-instrument` 的所有配置选项

|            Name            |     Type     |    Default     | Description                                                                      |
| :------------------------: | :----------: | :------------: | :------------------------------------------------------------------------------- |
|        **`debug`**         | `{Boolean}`  |    `false`     | 打开调试模式                                                                     |
|       **`compact`**        | `{Boolean}`  |     `true`     |                                                                                  |
|       **`autoWrap`**       | `{Boolean}`  |    `false`     | 生成紧凑的代码                                                                   |
|      **`esModules`**       | `{Boolean}`  |    `false`     | 设置为 `true` 以检测 ES2015 模块                                                 |
|   **`coverageVariable`**   |  `{String}`  | `__coverage__` | 全局覆盖变量的名称                                                               |
|   **`preserveComments`**   | `{Boolean}`  |    `false`     | 保留 `输出` 中的注释                                                             |
|   **`produceSourceMap`**   | `{Boolean}`  |    `false`     | 设置为 `true` 以生成已检测代码的 source map                                      |
| **`sourceMapUrlCallback`** | `{Function}` |     `null`     | 在原始代码中找到源映射 URL 时调用的回调函数，使用源文件名和源映射 URL 调用此函数 |

**webpack.config.js**

```js
{
  test: /\.js$/,
  use: {
    loader: 'istanbul-instrumenter-loader',
    options: {...options}
  }
}
```

## 维护人员

<table>
  <tbody>
    <tr>
      <td align="center">
        <img width="150" height="150"
        src="https://avatars.githubusercontent.com/u/266822?v=3&s=150">
        </br>
        <a href="https://github.com/deepsweet">Kir Belevich</a>
      </td>
      <td align="center">
        <a href="https://github.com/bebraw">
          <img width="150" height="150" src="https://github.com/bebraw.png?v=3&s=150">
          </br>
          Juho Vepsäläinen
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/d3viant0ne">
          <img width="150" height="150" src="https://github.com/d3viant0ne.png?v=3&s=150">
          </br>
          Joshua Wiens
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/michael-ciniawsky">
          <img width="150" height="150" src="https://github.com/michael-ciniawsky.png?v=3&s=150">
          </br>
          Michael Ciniawsky
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/mattlewis92">
          <img width="150" height="150" src="https://github.com/mattlewis92.png?v=3&s=150">
          </br>
          Matt Lewis
        </a>
      </td>
    </tr>
  <tbody>
</table>

[npm]: https://img.shields.io/npm/v/istanbul-instrumenter-loader.svg
[npm-url]: https://npmjs.com/package/istanbul-instrumenter-loader
[node]: https://img.shields.io/node/v/istanbul-instrumenter-loader.svg
[node-url]: https://nodejs.org
[deps]: https://david-dm.org/webpack-contrib/istanbul-instrumenter-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/istanbul-instrumenter-loader
[tests]: http://img.shields.io/travis/webpack-contrib/istanbul-instrumenter-loader.svg
[tests-url]: https://travis-ci.org/webpack-contrib/istanbul-instrumenter-loader
[cover]: https://codecov.io/gh/webpack-contrib/istanbul-instrumenter-loader/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/istanbul-instrumenter-loader
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
