# 实现简易的 Webpack

## 1 简易的 Webpack Loader

编写一个 Webpack Loader 小案例，用于替换源码里面的某个字符串。

1、新建并初始化项目

2、新建一个目录 loaders，在目录下新建文件 replaceLoader.js

```javascript
const loaderUtils = require('loader-utils')
// 这里不能用箭头函数，因为我们要使用原始的this指向
// source是我们引入的文件的源代码
// 把拿到的源代码进行变更，再返回回去
module.exports = function (source) {
  // options传递过来的参数通过this.query获取
  // return source.replace('Zoe', this.query.name)

  // 直接用this.query获取参数有时候会比较奇怪，可能得到的不是对象而是字符串，可以使用loader-utils来解决这个的问题
  // const options = loaderUtils.getOptions(this)
  // return source.replace('Zoe', options.name)

  // 上面的方法只能return一个参数（修改后的源代码）出去，可以使用this.callback返回多个参数，比如sourceMap等
  // this.callback中的参数err、content、sourceMap、meta，meta中放的是我们想返回的其它参数
  // this.callback相当于return
  // const options = loaderUtils.getOptions(this)
  // const result = source.replace('Zoe', options.name)
  // this.callback(null, result)

  // 如果我们想写异步的代码
  const options = loaderUtils.getOptions(this)
  const callback = this.async()
  setTimeout(() => {
    const result = source.replace('Zoe', options.name)
    callback(null, result)
  }, 1000)
}
```

3、配置 webpack.config.js

```javascript
const path = require('path')

module.exports = {
  mode: 'development',
  entry: {
    main: './src/index.js',
  },
  resolveLoader: {
    modules: ['node_modules', './loaders'],
  },
  module: {
    rules: [
      {
        test: /\.js/,
        use: [
          {
            // loader: path.resolve(__dirname, './loaders/replaceLoader.js'),
            // 如果上面配置了resolveLoader，这里的loader就可以直接写成replaceLoader
            loader: 'replaceLoader',
            options: {
              name: '小畅叙',
            },
          },
        ],
      },
    ],
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js',
  },
}
```

## 2 简易的 Webpack Plugin

Webpack Loader 的作用是在打包的时候处理模块。如果我们想在打包的某些时刻做某些事情，可以使用 Webpack Plugin。

Webpack Plugin 的核心机制是事件驱动或发布订阅的设计模式。

编写一个小案例，用于在打包结束后，在 dist 目录下生成一个版权信息文件 copyright.txt。

1、新建并初始化项目

2、新建一个目录 plugins，在目录下新建文件 copyright-webpack-plugin.js

```javascript
class CopyrightWebpackPlugin {
  // 在constructor中通过options接收传递过来的参数
  // constructor(options) {
  //   console.log(options)
  // }

  apply(compiler) {
    // compiler可以理解为Webpack的实例，存储了Webpack的打包过程、各种配置等各种内容
    // compiler中有很多hooks，表示不同的生命周期
    // emit表示把打包好的资源放到dist目录的那个时刻，是异步的hook
    compiler.hooks.emit.tapAsync('CopyrightWebpackPlugin', (compilation, callback) => {
      // compilation中存放的只是跟本次打包相关的内容
      // 打包后生成哪些内容是放在compilation.assets中的
      // 通过debugger来调试，可以看到compilation中有哪些内容，需要在package.json中做相应的配置
      debugger
      compilation.assets['copyright.txt'] = {
        source: function () {
          return 'copyright is reserved by zoe'
        },
        size: function () {
          return 28
        },
      }
      callback()
    })
    // 对于同步的hook，比如compile
    compiler.hooks.compile.tap('CopyrightWebpackPlugin', compilation => {
      console.log('compile是同步的hook')
    })
  }
}
module.exports = CopyrightWebpackPlugin
```

3、配置 webpack.config.js

```javascript
const path = require('path')
const CopyrightWebpackPlugin = require('./plugins/copyright-webpack-plugin')

module.exports = {
  mode: 'development',
  entry: {
    main: './src/index.js',
  },
  plugins: [
    // new CopyrightWebpackPlugin({ name: 'Zoe' }), // 可以给插件传递参数
    new CopyrightWebpackPlugin(),
  ],
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js',
  },
}
```

**补充：**使用 node 调试工具。

```json
"scripts": {
  "debug": "node --inspect --inspect-brk node_modules/webpack/bin/webpack.js",
  "build": "webpack"
}
```

debug 和 build 所做的事情是一样的，debug 中使用 node 显式执行 webpack.js 文件的时候，可以传递一些 node 参数进去，比如 `--inspect --inspect-brk`，`--inspect` 表示开启 node 调试工具，`--inspect-brk` 表示执行 webpack.js 文件时在第一行处打断点。然后我们就可以在我们自己编写的 plugin 代码中通过 `debugger` 打断点了。

但是我这里出了问题，断点没有出来，提示 `WebSockets request was expected`，不知道怎么解决？

## 3 编写一个 Bundler

我们编写一个类似于 Webpack 的打包工具 Bundler，来加深对 Webpack 的理解。

### 3.1 模块分析

1、安装必要的工具

```bash
cnpm install cli-highlight -g # 高亮显示在控制台打印出来的文件中的内容
cnpm install @babel/parser -D # 用于分析源码，生成抽象语法树
cnpm install @babel/traverse -D # 用于快速找到抽象语法树中的import节点
cnpm install @babel/core -D # babel核心模块
cnpm install @babel/preset-env -D # 可以把ES6转换为ES5的插件
```

2、新建项目 bundler

3、新建目录 src，在 src 目录下写源代码，index.js 是入口文件，用于测试我们编写的打包工具

4、新建文件 bundler.js

```javascript
const fs = require('fs')
const path = require('path')
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default
const babel = require('@babel/core')

const moduleAnalyser = filename => {
  const content = fs.readFileSync(filename, 'utf-8')
  // 可以借助cli-highlight来高亮显示打印出来的文件内容
  // 在项目目录下使用命令node bundler.js | highlight来执行该文件
  // console.log(content)

  // 抽象语法树
  const ast = parser.parse(content, {
    // 源代码中使用了ES Module，即import引入模块的方式
    sourceType: 'module',
  })

  // 所有的依赖
  const dependencies = {}
  traverse(ast, {
    // 只要抽象语法树中包含引入的语句，就会执行下面的ImportDeclaration函数
    ImportDeclaration({ node }) {
      const dirname = path.dirname(filename)
      // windows系统下，path.join出来的路径中的斜杆是向右的，需要替换成向左的
      let newFilePath = './' + path.join(dirname, node.source.value)
      newFilePath = newFilePath.replace('\\', '/')
      dependencies[node.source.value] = newFilePath
    },
  })

  // 把抽象语法树转换为浏览器可以执行的代码
  // code就是浏览器可以执行的代码
  const { code } = babel.transformFromAst(ast, null, {
    // 插件集合
    presets: ['@babel/preset-env'],
  })
  return {
    filename,
    dependencies,
    code,
  }
}
// 对入口文件的代码分析
const moduleInfo = moduleAnalyser('./src/index.js')
console.log(moduleInfo)
```

5、在项目目录下使用命令 `node bundler.js | highlight` 对入口文件（模块）进行代码分析

### 3.2 依赖图谱（Dependencies Graph）

修改 bundler.js，加上依赖图谱，即项目中所有模块代码分析的结果。

```javascript
// moduleAnalyser函数见上一节
const makeDependenciesGraph = entry => {
  const entryModule = moduleAnalyser(entry)
  // graphArray就是依赖图谱
  const graphArray = [entryModule]
  for (let i = 0; i < graphArray.length; i++) {
    const item = graphArray[i]
    const { dependencies } = item
    if (dependencies) {
      for (let j in dependencies) {
        graphArray.push(moduleAnalyser(dependencies[j]))
      }
    }
  }
  const graph = {}
  graphArray.forEach(item => {
    graph[item.filename] = {
      dependencies: item.dependencies,
      code: item.code,
    }
  })
  return graph
}

// 整个工程项目的依赖图谱
const graphInfo = makeDependenciesGraph('./src/index.js')
console.log(graphInfo)
```

### 3.2 生成浏览器可以执行的代码

借助依赖图谱，生成浏览器可以执行的代码。

```javascript
// makeDependenciesGraph函数见上一节
const generateCode = entry => {
  const graph = JSON.stringify(makeDependenciesGraph(entry))
  return `
    (function(graph){
      function require(module){
        function localRequire(relativePath){
          return require(graph[module].dependencies[relativePath])
        }
        var exports = {};
        (function(require, exports, code){
          eval(code)
        })(localRequire, exports, graph[module].code)
        return exports
      }
      require('${entry}')
    })(${graph})
  `
}

const code = generateCode('./src/index.js')
console.log(code)
```

**注意：**`var exports = {};` 这条语句后面的分号是必需的，不然生成的 code 用浏览器执行会报错。
