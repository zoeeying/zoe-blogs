# React 博客系统

* **博客前台：**用户使用、博客展现

* **接口中台：**数据接口、业务逻辑

* **后台管理：**文章类别管理、系统设置

## 1 前台搭建步骤

##### 1. 准备工作

```bash
cnpm install -g create-next-app
npx create-next-app blog # 前台目录名称是blog
cd blog
yarn add @zeit/next-css # 使用css
yarn add antd
yarn add babel-plugin-import # import按需加载
```

##### 2. 配置 next.config.js

在 blog 目录下新建文件 next.config.js，这是 Next.js 项目的配置文件。

```js
// 按照如下配置后，Next.js项目就支持css了
const withCss = require('@zeit/next-css')
if (typeof require !== 'undefined') {
  require.extensions['.css'] = file => { }
}
module.exports = withCss({})
```

##### 3. 配置 .babelrc

在 blog 目录下新建文件 .babelrc，这是 babel 的配置文件。

```js
{
  "presets": [
    "next/babel" // Next.js的总配置文件，相当于继承了它本身的所有babel配置
  ],
  // 增加新的插件，这个插件就是让antd可以按需引入，包括css
  "plugins": [
    [
      "import",
      {
        "libraryName": "antd"
      }
    ]
  ]
}
```

在 pages 目录下新建文件 _app.js 文件，这是所有页面的整体文件。

```js
// antd有个bug，不能配置css按需引入，需要做一个整体的css引入
import App from 'next/app'

import 'antd/dist/antd.css'

export default App
```

