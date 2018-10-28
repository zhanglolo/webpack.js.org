---
title: CopyWebpackPlugin
source: https://raw.githubusercontent.com/webpack-contrib/copy-webpack-plugin/master/README.md
edit: https://github.com/webpack-contrib/copy-webpack-plugin/edit/master/README.md
repo: https://github.com/webpack-contrib/copy-webpack-plugin
---
复制单独文件或者整个文档到build目录下

## Install

```
npm install --save-dev copy-webpack-plugin
```

## Usage

`new CopyWebpackPlugin([patterns], options)`

A pattern looks like:
模式看起来像：
`{ from: 'source', to: 'dest' }`


或者，在只有带有默认目标的`from`的简单情况下，您可以使用字符串原语而不是对象：
`'source'`

###

| 名称 | 需求 | 默认     | 详情                                                 |
|------|----------|------------ |---------------------------------------------------------|
| `from` | Y        |             | _examples:_<br>'relative/file.txt'<br>'/absolute/file.txt'<br>'relative/dir'<br>'/absolute/dir'<br>'\*\*/\*'<br>{glob:'\*\*/\*', dot: true}<br><br>Globs 接受 [minimatch 选项](https://github.com/isaacs/minimatch) |
| `to`   | N        | output root if `from` is file or dir<br><br>resolved glob path if `from` is glob | _examples:_<br>'relative/file.txt'<br>'/absolute/file.txt'<br>'relative/dir'<br>'/absolute/dir'<br>'relative/[name].[ext]'<br>'/absolute/[name].[ext]'<br><br>模板是 [file-loader patterns](/loaders/file-loader/) |
| `toType` | N | **'file'** if `to` has extension or `from` is file<br><br>**'dir'** if `from` is directory, `to` has no extension or ends in '/'<br><br>**'template'** if `to` contains [a template pattern](/loaders/file-loader/) | |
| `context` | N | options.context \|\| compiler.options.context | 确定如何解释from路径的路径 |
| `flatten` | N | false | 删除所有目录引用并仅复制文件名<br> <br>如果文件具有相同的名称，则结果是不确定的 |
| `ignore` | N | [] | 额外的 globs 去忽视这个模式 |
| `transform` | N | function(content, path) {<br>&nbsp;&nbsp;return content;<br>} | 在写入webpack之前修改文件内容的函数 |
| `force` | N | false | 覆盖compilation.assets中已有的文件（通常由其他插件添加） |

#### Available options:

| Name | Default | Details |
| ---- | ------- | ------- |
| `context` | compiler.options.context | 确定如何解释所有模式共享的`from`路径的路径 |
| `ignore` | [] | 要忽略的数组（应用于`from`） |
| `copyUnmodified` | false | 使用watch或webpack-dev-server时，无论修改如何，都会复制文件。 无论此选项如何，所有文件都将在首次构建时复制。 |
| `debug` | **'warning'** | _options:_<br>**'warning'** - 只有警告<br>**'信息'** 或真 - 文件位置和浏览信息<br>**'调试'** - 非常详细的调试信息

### 例子

```javascript
const CopyWebpackPlugin = require('copy-webpack-plugin');
const path = require('path');

module.exports = {
    context: path.join(__dirname, 'app'),
    devServer: {
        // 对于旧版本的webpack-dev-server，这是必需的
        //如果你使用绝对路径'to'。 路径应该是
        //构建目标的绝对路径。
        outputPath: path.join(__dirname, 'build')
    },
    plugins: [
        new CopyWebpackPlugin([
            // {output}/file.txt
            { from: 'from/file.txt' },
            
            // 等价
            'from/file.txt',

            // {output}/to/file.txt
            { from: 'from/file.txt', to: 'to/file.txt' },
            
            // {output}/to/directory/file.txt
            { from: 'from/file.txt', to: 'to/directory' },

            // 复制目录内容到 {output}/
            { from: 'from/directory' },
            
            // 复制目录内容到 {output}/to/directory/
            { from: 'from/directory', to: 'to/directory' },
            
            // 复制glob结果到 /absolute/path/
            { from: 'from/directory/**/*', to: '/absolute/path' },

            // 复制glob结果到 (点文件) to /absolute/path/
            {
                from: {
                    glob:'from/directory/**/*',
                    dot: true
                },
                to: '/absolute/path'
            },

            // 复制glob结果到, 相关到内容中
            {
                context: 'from/directory',
                from: '**/*',
                to: '/absolute/path'
            },
            
            // {output}/file/without/extension
            {
                from: 'path/to/file.txt',
                to: 'file/without/extension',
                toType: 'file'
            },
            
            // {output}/directory/with/extension.ext/file.txt
            {
                from: 'path/to/file.txt',
                to: 'directory/with/extension.ext',
                toType: 'dir'
            }
        ], {
            ignore: [
                // 不复制任何带有txt扩展名的文件
                '*.txt',
                
                // 不复制任何文件，即使它们以点开头
                '**/*',

                // 不复制任何文件，除非它们以点开头
                { glob: '**/*', dot: false }
            ],

            // 默认情况下，我们只复制修改过的文件
            // a watch or webpack-dev-server build. Setting this
            // 当值为“真”翻译所有文件.
            copyUnmodified: true
        })
    ]
};
```

### 经常的问题与解答

#### “EMFILE：太多打开的文件”或“ENFILE：文件表溢出”

Globally patch fs with [graceful-fs](https://www.npmjs.com/package/graceful-fs)

`npm install graceful-fs --save-dev`

在webpack配置的顶部，插入此项

    const fs = require('fs');
    const gracefulFs = require('graceful-fs');
    gracefulFs.gracefulify(fs);

看[这个问题](https://github.com/kevlened/copy-webpack-plugin/issues/59#issuecomment-228563990)，获取更多结果

#### This doesn't copy my files with webpack-dev-server

从版本 [3.0.0](https://github.com/kevlened/copy-webpack-plugin/blob/master/CHANGELOG.md#300-may-14-2016), 我们停止使用fs将文件复制到文件系统并根据webpack启动 [in-memory filesystem](https://webpack.github.io/docs/webpack-dev-server.html#content-base):

>... webpack-dev-server将提供构建文件夹中的静态文件。 它会查看您的源文件以进行更改，并且在进行更改时，将重新编译该包。 **此修改的包在内存中以publicPath中指定的相对路径提供（请参阅API）**。 它不会写入配置的输出目录。

如果你必将使用webpack-dev-server写入输出目录，则可以使用[write-file-webpack-plugin](https://github.com/gajus/write-file-webpack-plugin).

## 维护者

<table>
  <tbody>
    <tr>
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
        <a href="https://github.com/evilebottnawi">
          <img width="150" height="150" src="https://github.com/evilebottnawi.png?v=3&s=150">
          </br>
          Alexander Krasnoyarov
        </a>
      </td>
    </tr>
  <tbody>
</table>


[npm]: https://img.shields.io/npm/v/copy-webpack-plugin.svg
[npm-url]: https://npmjs.com/package/copy-webpack-plugin

[node]: https://img.shields.io/node/v/copy-webpack-plugin.svg
[node-url]: https://nodejs.org

[deps]: https://david-dm.org/webpack-contrib/copy-webpack-plugin.svg
[deps-url]: https://david-dm.org/webpack-contrib/copy-webpack-plugin

[test]: https://secure.travis-ci.org/webpack-contrib/copy-webpack-plugin.svg
[test-url]: http://travis-ci.org/webpack-contrib/copy-webpack-plugin

[cover]: https://codecov.io/gh/webpack-contrib/copy-webpack-plugin/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/copy-webpack-plugin

[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack
