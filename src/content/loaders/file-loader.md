---
title: file-loader
source: https://raw.githubusercontent.com/webpack-contrib/file-loader/master/README.md
edit: https://github.com/webpack-contrib/file-loader/edit/master/README.md
repo: https://github.com/webpack-contrib/file-loader
---

指示 webpack 将所需的对象作为文件发送并返回其公用 URL


[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]



The `file-loader` resolves `import`/`require()` on a file into a url and emits the file into the output directory.

## 起步

你需要预先安装 `file-loader`：

```console
$ npm install file-loader --save-dev
```

在一个 bundle 文件中 import（或 `require`）目标文件：

**file.js**

```js
import img from "./file.png";
```

然后，在 `webpack` 配置中添加 loader。例如：

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: "file-loader",
            options: {}
          }
        ]
      }
    ]
  }
};
```

然后，通过你偏爱的方式去运行 `webpack`。将 `file.png` 作为一个文件，生成到输出目录，
（如果指定了选项，则使用指定的命名约定）
并返回文件的 public URI。

> ℹ️ 默认情况下，生成文件的文件名，是文件内容的 MD5 哈希值，并会保留所引用资源的原始扩展名。

## 选项

|         名称          |         类型         |                                                       默认值                                                        | 描述                                                                                                                      |
| :-------------------: | :------------------: | :-----------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------ |
|      **`name`**       | `{String\|Function}` |                                                   `[hash].[ext]`                                                    | 为你的文件配置自定义文件名模板                                                                                            |
|     **`context`**     |      `{String}`      |                                               `this.options.context`                                                | 配置自定义文件 context，默认为 `webpack.config.js` [context](https://webpack.docschina.org/configuration/entry-context/#context) |
|   **`publicPath`**    | `{String\|Function}` | [`__webpack_public_path__`](https://webpack.docschina.org/api/module-variables/#__webpack_public_path__-webpack-specific-) | 为你的文件配置自定义 `public` 发布目录                                                                                    |
|   **`outputPath`**    | `{String\|Function}` |                                                    `'undefined'`                                                    | 为你的文件配置自定义 `output` 输出目录                                                                                    |
| **`useRelativePath`** |     `{Boolean}`      |                                                       `false`                                                       | 如果你希望为每个文件生成一个相对 url 的 `context` 时，应该将其设置为 `true`                                               |
|    **`emitFile`**     |     `{Boolean}`      |                                                       `true`                                                        | 默认情况下会生成文件，可以通过将此项设置为 false 来禁止（例如，使用了服务端的 packages）                                  |

类型：`String|Function`
默认：`'[hash].[ext]'`

您可以使用查询参数为您的文件配置自定义文件名模板 `name`。例如，要将文件从 `context` 目录复制到保留完整目录结构的输出目录中，可以使用

#### `String`

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[path][name].[ext]',
            },
          },
        ],
      },
    ],
  },
};
```

#### `Function`

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name(file) {
                if (process.env.NODE_ENV === 'development') {
                  return '[path][name].[ext]';
                }

                return '[hash].[ext]';
              },
            },
          },
        ],
      },
    ],
  },
};
```

> ℹ️ 默认情况下，文件会按照你指定的路径和名称输出同一目录中，且会使用相同的 URI 路径来访问文件。

|     名称     |    类型    |                             默认值                             | 描述                                         |
| :----------: | :--------: | :------------------------------------------------------------: | :------------------------------------------- |
| **`[ext]`**  | `{String}` |                         `file.extname`                         | 资源扩展名                                   |
| **`[name]`** | `{String}` |                        `file.basename`                         | 资源的基本名称                               |
| **`[path]`** | `{String}` |                         `file.dirname`                         | 资源相对于 `context`的路径                   |
| **`[hash]`** | `{String}` |                             `md5`                              | 内容的哈希值，下面的 hashes 配置中有更多信息 |
|  **`[N]`**   | `{Number}` | ``|当前文件名按照查询参数 `regExp` 匹配后获得到第 N 个匹配结果 |

类型：`String|Function`
默认：`undefined`

Specify a filesystem path where the target file(s) will be placed.

|       名称       |    类型    |  默认值  | 描述                                                                                  |
| :--------------: | :--------: | :------: | :------------------------------------------------------------------------------------ |
|  **`hashType`**  | `{String}` |  `md5`   | `sha1`, `md5`, `sha256`, `sha512`                                                     |
| **`digestType`** | `{String}` | `base64` | `hex`, `base26`, `base32`, `base36`, `base49`, `base52`, `base58`, `base62`, `base64` |
|   **`length`**   | `{Number}` |  `9999`  | 字符的长度                                                                            |

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              outputPath: 'images',
            },
          },
        ],
      },
    ],
  },
};
```

#### `Function`

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              outputPath: (url, resourcePath, context) => {
                // `resourcePath` is original absolute path to asset
                // `context` is directory where stored asset (`rootContext`) or `context` option

                // To get relative path you can use
                // const relativePath = path.relative(context, resourcePath);

                if (/my-custom-image\.png/.test(resourcePath)) {
                  return `other_output_path/${url}`;
                }

                if (/images/.test(context)) {
                  return `image_output_path/${url}`;
                }

                return `output_path/${url}`;
              },
            },
          },
        ],
      },
    ],
  },
};
```

### `publicPath`

类型：`String|Function`
默认：[`__webpack_public_path__`](https://webpack.js.org/api/module-variables/#__webpack_public_path__-webpack-specific-)

Specifies a custom public path for the target file(s).

#### `String`

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              publicPath: 'assets',
            },
          },
        ],
      },
    ],
  },
};
```

#### `Function`

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              publicPath: (url, resourcePath, context) => {
                // `resourcePath` is original absolute path to asset
                // `context` is directory where stored asset (`rootContext`) or `context` option

                // To get relative path you can use
                // const relativePath = path.relative(context, resourcePath);

                if (/my-custom-image\.png/.test(resourcePath)) {
                  return `other_public_path/${url}`;
                }

                if (/images/.test(context)) {
                  return `image_output_path/${url}`;
                }

                return `public_path/${url}`;
              },
            },
          },
        ],
      },
    ],
  },
};
```

### `context`

类型：`String`
默认：[`context`](https://webpack.js.org/configuration/entry-context/#context)

Specifies a custom file context.

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              context: 'project',
            },
          },
        ],
      },
    ],
  },
};
```

### `emitFile`

类型：`Boolean`
默认：`true`

如果是 true，生成一个文件（向文件系统写入一个文件）。
如果是 false，loader 会返回 public URI，但**不会**生成文件。
对于服务器端 package，禁用此选项通常很有用。

**file.js**

```js
import img from "./file.png";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              emitFile: false,
            },
          },
        ],
      },
    ],
  },
};
```

> ⚠️ 返回 public URL 但**不会**生成文件

Specifies a Regular Expression to one or many parts of the target file path.
The capture groups can be reused in the `name` property using `[N]`
[placeholder](https://github.com/webpack-contrib/file-loader#placeholders).

**file.js**

```js
import img from './customer01/file.png';
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              regExp: /\/([a-z0-9]+)\/[a-z0-9]+\.png$/,
              name: '[1]-[name].[ext]',
            },
          },
        ],
      },
    ],
  },
};
```

> ℹ️ If `[0]` is used, it will be replaced by the entire tested string, whereas `[1]` will contain the first capturing parenthesis of your regex and so on...

## placeholders

Full information about placeholders you can find [here](https://github.com/webpack/loader-utils#interpolatename).

### `[ext]`

类型：`String`
默认：`file.extname`

目标文件/资源的文件扩展名。

### `[name]`

类型：`String`
默认：`file.basename`

文件/资源的基本名称。

### `[path]`

类型：`String`
默认：`file.directory`

The path of the resource relative to the webpack/config `context`.

### `[folder]`

类型：`String`
默认：`file.folder`

The folder of the resource is in.

### `[emoji]`

类型：`String`
默认：`undefined`

A random emoji representation of `content`.

### `[emoji:<length>]`

类型：`String`
默认：`undefined`

Same as above, but with a customizable number of emojis

### `[hash]`

类型：`String`
默认：`md5`

指定生成文件内容哈希值的哈希方法。

### `[<hashType>:hash:<digestType>:<length>]`

类型：`String`

The hash of options.content (Buffer) (by default it's the hex digest of the hash).

#### `digestType`

类型：`String`
默认：`'hex'`

The [digest](https://en.wikipedia.org/wiki/Cryptographic_hash_function) that the
hash function should use. Valid values include: base26, base32, base36,
base49, base52, base58, base62, base64, and hex.

#### `hashType`

类型：`String`
默认：`'md5'`

The type of hash that the has function should use. Valid values include: `md5`,
`sha1`, `sha256`, and `sha512`.

#### `length`

类型：`Number`
默认：`undefined`

Users may also specify a length for the computed hash.

### `[N]`

类型：`String`
默认：`undefined`

The n-th match obtained from matching the current file name against the `regExp`.

## 示例

```js
import png from "image.png";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: 'dirname/[hash].[ext]',
            },
          }
        ],
      },
    ],
  },
};
```

结果：

```bash
# result
dirname/0dcbbaa701328ae351f.png
```

**webpack.config.js**

```js
import png from './image.png';
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[sha512:hash:base64:7].[ext]',
            },
          },
        ],
      },
    ],
  },
};
```

结果：

```bash
# result
gdyb21L.png
```

---

**file.js**

```js
import png from "path/to/file.png";
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[path][name].[ext]?[hash]',
            },
          },
        ],
      },
    ],
  },
};
```

结果：

```bash
# result
path/to/file.png?e43b20c069c4a01867c31e98cbce33c9
```

## 贡献

如果你从未阅读过我们的贡献指南，请在上面花点时间。

[贡献指南](https://raw.githubusercontent.com/webpack-contrib/file-loader/master/.github/CONTRIBUTING.md)

[npm]: https://img.shields.io/npm/v/file-loader.svg
[npm-url]: https://npmjs.com/package/file-loader
[node]: https://img.shields.io/node/v/file-loader.svg
[node-url]: https://nodejs.org
[deps]: https://david-dm.org/webpack-contrib/file-loader.svg
[deps-url]: https://david-dm.org/webpack-contrib/file-loader
[tests]: http://img.shields.io/travis/webpack-contrib/file-loader.svg
[tests-url]: https://travis-ci.org/webpack-contrib/file-loader
[cover]: https://img.shields.io/codecov/c/github/webpack-contrib/file-loader.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/file-loader
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=file-loader
[size-url]: https://packagephobia.now.sh/result?p=file-loader
