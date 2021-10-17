# 使用 React Testing Library 测试 React 组件

React Testing Library 是开源项目 Testing Library 的一部分，它是一个用于测试 React 组件的库。

React Testing Library 的测试原则是：

> [The more your tests resemble the way your software is used, the more confidence they can give you.](https://testing-library.com/docs/guiding-principles)

不同于 Enzyme，React Testing Library 并不会处理组件实例，也不关注组件的实现细节和状态变化，而是对组件的输出（真实 DOM）进行测试。其实，基于组件的输出编写的测试用例，更符合用户使用组件的习惯，因为用户根本不关心你的组件是怎么实现的，他们只关心组件的最后呈现以及如何与组件交互；而且，也能大大提升开发者对自己所开发的组件的信心。

同时，由于 React Testing Library 并不关心组件内部的实现细节，因此使用 React Testing Library 编写的测试用例，在组件修改或者重构（并不会改变组件的输出）后，无需进行大的变动。这一点是一直以来开发者使用 Enzyme 测试组件的痛点。

## 1 安装配置

#### (1) 安装

使用 create-react-app 创建的 React 项目默认安装了 React Testing Library 以及一些辅助使用的库；对于非 CRA 项目，需要手动安装这些库：

```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

@testing-library/jest-dom 扩展了一些 Jest 匹配器，用于断言，方便我们编写容易阅读和维护的测试用例；@testing-library/user-event 是基于 fireEvent 封装的，用于模拟用户交互。

#### (2) setupTests.js

对于 CRA 项目，src 目录中的 setupTests.js 文件默认被配置在了 Jest 配置文件的 setupFilesAfterEnv 选项中，它会在开始测试之前自动执行。我们可以把一些测试前的准备工作，比如全局的测试配置，放在 setupTests.js 中。

对于完全手动搭建测试环境的项目，可以自行在 src 目录中创建 setupTests.js 文件，然后手动配置在 Jest 配置文件中，如下：

```javascript
// jset.config.js
module.exports = {
  // ...
  setupFilesAfterEnv: [`<rootDir>/src/setupTests.js`]
  // ...
}
```

在 setupTests.js 中，可以全局引入 @testing-library/jest-dom，这样就不用在每个测试文件中重复引入了：

```javascript
// jest-dom adds custom jest matchers for asserting on DOM nodes

import '@testing-library/jest-dom'
```

#### (3) 配置测试命令

使用 CRA 创建的项目，可以在 package.json 中的 scripts 中增加如下脚本，用于测试：

```json
"scripts": {
  "test": "react-scripts test --watchAll=false",
  "test:watch": "react-scripts test",
  "test:report": "react-scripts test --watchAll=false --coverage"
}
```

执行如下命令，运行测试：

```bash
npm run test # 运行测试，执行所有测试用例
npm run test:watch # 运行测试，开启watch模式
npm run test:report # 运行测试，执行所有测试用例，并生成覆盖率报告
```

## 2 Mock Service Worker

在前端的组件测试中，并不会真正地调用后端接口，而是采用模拟接口的方式，React Testing Library 推荐使用 Mock Service Worker（MSW）来模拟请求接口。

下面介绍如何安装和配置 MSW。

首先需要安装 MSW：

```bash
npm install msw --save-dev
```

然后，推荐在 src 目录中新建 mock 目录，并在 mocks 中新建 handlers.js 和 server.js。

在 handlers.js 中定义所有模拟的接口；在 server.js 中调用 msw/node 提供的 setupServer 函数，传入 handlers.js，创建模拟的服务实例。

下面是 handlers.js 和 server.js  示例：

```javascript
// mocks/handlers.js
import { rest } from 'msw' // 用于模拟REST API

const handlers = [
  rest.get('/greeting', (req, res, ctx) => res(
    ctx.json({ greeting: 'Hello React Testing Library' })
  ))
]

export default handlers
```

```javascript
// mocks/server.js

import { setupServer } from 'msw/node'
import handlers from './hanlders'

export const server = setupServer(...handlers)
```

handlers.js 中的 `rest.get()` 的第二个参数是一个响应解析器，它是一个函数，可以接收捕获的请求，并返回模拟的响应结果。响应解析器函数接收三个参数：

1、req，包含捕获的请求信息，比如，可以通过 `req.url.searchParams.get('参数名')` 拿到 query 参数，通过 `req.body` 拿到请求体等；

2、res，用于创建模拟响应的函数；

3、ctx，用于设置模拟响应的响应码、响应头、响应体。

最后，我们需要在上一节说的 setupTests.js 中，全局设置模拟接口：

```javascript
import { server } from './mocks/server'

// Establish API mocking before all tests.
beforeAll(() => server.listen())

// Reset any request handlers that we may add during the tests,
// so they don't affect other tests.
afterEach(() => server.resetHandlers())

// Clean up after the tests are finished.
afterAll(() => server.close())

// 解决报错 TypeError:window.matchMedia is not a function
Object.defineProperty(window, 'matchMedia', {
  value: () => ({
    matches: false,
    addListener: () => {},
    removeListener: () => {},
  })
})
```

当然，我们也可以把上面的接口设置写在单独的测试文件中，但是不推荐。

## 3 自定义 render

如果在项目中使用了 Context、Redux 等，对 @testing-library/react 导出的 render 方法进行自定义，可以帮助我们减少在测试用例中编写重复代码。

推荐在 src/utils 中新建 test-utils.js 文件，导入 @testing-library/react 中的 render 方法并对其进行自定义，最后导出 @testing-library/react 中所有的 API，以及自定义的 render 方法。这样，写测试用例的时候，直接从 test-utils.js 中导入需要的 API 和自定义的 render 方法即可。

下面是我们项目中的 test-utils.js：

```react
import { render as rtlRender } from '@testing-library/react'
import { IntlProvider } from 'react-intl'
import { Provider } from 'react-redux'
import { HashRouter } from 'react-router-dom'
import store from '@/redux/store'
import { en } from '@/locales'

function render(ui, { locale = 'en', route = '/', ...renderOptions } = {}) {
  // 向当前浏览器会话的历史堆栈中添加一个状态
  window.history.pushState({}, 'Demo', route)

  function Wrapper({ children }) {
    return (
      <IntlProvider locale={locale} messages={en}>
        <Provider store={store}>
          <HashRouter>{children}</HashRouter>
        </Provider>
      </IntlProvider>
    )
  }

  return rtlRender(ui, { wrapper: Wrapper, ...renderOptions })
}

// 导出所有
export * from '@testing-library/react'

// 覆盖@testing-library/react中的render函数
export { render }
```

由于我们的项目中使用了 react-redux、react-intl 以及 react-router-dom，所以我们的 Wrapper 比较笨重。大家可以根据实际项目情况定义不同的 Wrapper。

## 4 测试用例

在前三节中，我们已经做了充分的测试准备工作，下面我们通过一个案例（官方案例），来学习如何使用 React Testing Library 编写测试用例。

有一个 Fetch 组件，





































#### 用户交互

#### 查询

#### 匹配器

#### 异步方法

- **waitFor** (Promise) retry the function within until it stops throwing or times out
- **waitForElementToBeRemoved** (Promise) retry the function until it no longer returns a DOM node

#### within

#### Fake Timers

#### 出现和消失



#### act 告警问题

#### 测试误区



















