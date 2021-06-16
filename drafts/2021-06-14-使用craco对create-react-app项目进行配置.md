## 使用 craco 对 create-react-app 项目进行配置

create-react-app（CRA）是 React 官方提供的用来快速搭建 React 项目的脚手架工具，可以满足大多数的创建项目需求。

使用 CRA 搭建的项目，会默认安装和配置 Webpack、Babel、ESLint、Jest 等工具，并且把这些工具的预配置信息封装到了 react-scripts 中。

如果想修改脚手架的默认配置，有四种方案：

1、通过执行命令 `npm run eject`，把配置文件暴露出来，然后按需修改相关的配置；

2、重写 react-scripts 包；

3、使用 react-app-rewired + customize-cra 覆盖默认配置；

4、使用 Craco 覆盖默认配置。

对于一般项目开发，不建议使用方案 1。因为 `eject` 操作不可逆，一旦 `eject`，项目就无法还原了，而且无法再享受 react-scripts 对于相关工具和配置的更新，必须自己手动维护，且维护成本略高。当然，如果想学习和研究 CRA 内部构建原理和过程，请大胆 `eject` 吧。

目前，比较流行的是使用 Craco 对 CRA 项目的默认配置进行覆盖，并且这也是 Ant Design 4 推荐的方案。

## 1 基础配置

1、使用 CRA 创建一个名为 my-config-app 的项目：

```
npx create-react-app my-config-app
```

2、安装 Craco：

```bash
npm install @craco/craco --save-dev
```

3、修改 package.json 中的 scripts：

```json
{
  "scripts": {
    "start": "craco start",
    "build": "craco build",
    "test": "craco test"
  }
}
```

4、在项目根目录创建 craco.config.js 文件，编写自定义配置：

```javascript
module.exports = {
  // ...
}
```

## 2 配置 Webpack alias

对于 Webpack alias（别名）配置

```

```

## 3 配置 Jest alias

## 4 配置 Less 和 antd 自定义主题

## 5 配置 antd 样式按需加载
