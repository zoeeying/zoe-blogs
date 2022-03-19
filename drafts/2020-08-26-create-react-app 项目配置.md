# create-react-app 项目配置

create-react-app 是 Facebook 的 React 团队出的一个构建 React 单页应用的脚手架工具，它本身集成了 Webpack，并配置了一系列的 loader 和默认的 npm 脚本，可以帮助我们实现零配置快速开发 React 应用。

但是，把 create-react-app 脚手架应用于实际项目，还是需要做一些额外的配置的。

## 1 配置 ESLint + Prettier

ESLint 是一个插件化的 JavaScript 代码检测工具，它可以用于检查常见的 JavaScript 代码错误，也可以进行代码风格检查，这样我们就可以根据自己的喜好制定一套 ESLint 配置，然后应用到所编写的项目上，从而实现辅助编码规范的执行，有效控制项目代码的质量。

对于**代码风格**问题，ESLint 做得并不全，我们还需要借助 Prettier。结合 ESLint 和 Prettier，我们就可以解决项目中的**代码质量**问题和**代码风格**问题，统一代码规范。

#### (1) 配置 ESLint

使用 create-react-app 脚手架搭建的项目，默认提供了一套 ESLint 配置，项目启动后，可以在终端和浏览器控制台中查看到 linter 输出。如果希望使用**自定义**的 ESLint 配置，并且在 VSCode 中以下划波浪线的形式展示不符合 ESLint 规则的代码，需要做一些额外的操作：

1、

