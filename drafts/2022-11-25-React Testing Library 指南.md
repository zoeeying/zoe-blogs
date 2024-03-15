# React Testing Library 指南

React Testing Library（RTL）是由 Kent C. Dodds 开发的 React 组件测试框架，用于替代 Airbnb 的 Enzyme。

Enzyme 用于测试 React 组件的内部实现细节，而 React Testing Library 从 React 应用程序最终用户的角度来测试 React 组件，这样更能提升开发者对于 React 组件的信心。

这篇指南，主要介绍 React 组件单元测试和集成测试的必要步骤。

## 1. Jest vs. React Testing Library

React 初学者经常困惑于 React 的测试工具，比如 Jest 和 React Testing Library。React Testing Library 并不是 Jest 的替代方案，它们的关系是彼此依赖，同时又有不同的分工。

开发者想测试 React 项目，绕不开 Jest，因为 Jest 是最流行的 JavaScript 应用测试框架。开发者只需在 package.json 中设置 Jest 测试脚本，就可以通过 `npm run test` 来运行 Jest 测试。

Jest 提供以下的函数用于测试。

```javascript
describe('my function or component', () => {
  it('does the following', () => {

  });
});
```

describe 块区域 test suite（测试套件），it（可以用 test 替代 it）块区域是 test case（测试用例）。测试套件中可以有多个测试用例，而测试用例不一定需要写在测试套件中。

通常，在测试用例中，我们会加入 assertions（断言，Jest 中的 expect），结果可以是成功的（绿色表示成功），也可以是失败的（红色表示失败）。

下面看两个成功的断言。

```javascript
describe('true is truthy and false is falsy', () => {
  it('true is truthy', () => {
    expect(true).toBe(true);
  });

  it('false is falsy', () => {
    expect(false).toBe(false);
  });
});
```

默认情况下，当我们执行 `npm run test` 时，Jest 会自动匹配以 test.js 为后缀的文件（也可以在 Jest 配置文件中，设置匹配模式），并执行文件中的测试用例。

使用 create-react-app 创建的 React 应用默认安装了 Jest 和 React Testing Library。如果是自定义安装的 React 应用，需要手动安装 Jest 和 React Testing Library。

执行 `npm run test` 命令后，上面两个测试用例的执行结果如下。

```bash
 PASS  src/App.test.js
  true is truthy and false is falsy
    ✓ true is truthy (3ms)
    ✓ false is falsy

Test Suites: 1 passed, 1 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        2.999s
Ran all test suites related to changed files.

Watch Usage
 › Press a to run all tests.
 › Press f to run only failed tests.
 › Press q to quit watch mode.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press Enter to trigger a test run.
```

上面的测试执行完后，会看到所有的测试用例都是绿色的（表示用例是成功的）。Jest 也提供了命令行交互，通过交互，可以执行更多的指令。

如果修改了源码或者测试代码，Jest 会再次执行所有用例。

```javascript
function sum(x, y) {
  return x + y;
}

describe('sum', () => {
  it('sums up two values', () => {
    expect(sum(2, 4)).toBe(6);
  });
});
```

在实际的 JavaScript 项目中，通常想要测试的函数与测试代码并不是写在一起的。可以在测试文件中，把函数 import 进来。

```javascript
import sum from './math.js';

describe('sum', () => {
  it('sums up two values', () => {
    expect(sum(2, 4)).toBe(6);
  });
});
```

目前为止，我们可以发现，Jest 与 React 组件没有任何关系。

本质上，Jest 只是一个测试运行器，可以让我们通过命令行来执行测试。除此之外，Jest 还提供了一系列 API：测试套件（describe）、测试用例（it/describe）、断言（expect）。当然，Jest 提供的功能远不止这些，还包括 spies、mocks、stubs 等。

与 Jest 不同，React Testing Library 是一个用于测试 React 组件的库。上面提到的 Enzyme，也是测试 React 组件的库。

下面的章节将演示如何使用 React Testing Library 来测试 React 组件。

## 2. Vitest vs. React Testing Library

Vitest 与 Jest 一样，是一个测试运行器，包括测试套件（describe）、测试用例（it/describe）、断言（expect）。对于单页应用，如果是使用 Vite 而不是 create-react-app 搭建的，推荐使用 Vitest 作为测试运行器。

## 3. 渲染一个 React 组件

项目安装配置好 Jest 或 Vitest 后，可以使用 React Testing Library 来渲染一个 React 组件。

在 src/App.js 中，编写一个函数式组件 App。

```jsx
import * as React from 'react';

const title = 'Hello React';

function App() {
  return <div>{title}</div>;
}

export default App;
```

在 src/App.test.js 中，对 App 组件进行测试。

```jsx
import * as React from 'react';
import { render } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);
  });
});
```

RTL 的 render 函数，可以渲染任意的 JSX 语法，得到 React 组件。

还可以使用 RTL 提供的 debug 函数进行调试。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    screen.debug();
  });
});
```

在命令行执行上面的测试后，可以看到 React 组件渲染后的 HTML 输出，便于对测试用例进行调试。

```html
<body>
  <div>
    <div>
      Hello React
    </div>
  </div>
</body>
```

事实上，React Testing Library 并不关心组件的实现细节，下面以一个复杂的可控组件为例（包括 useState、事件处理、props），观察 RTL 的 debug 输出。

```jsx
import * as React from 'react';

function App() {
  const [search, setSearch] = React.useState('');

  function handleChange(event) {
    setSearch(event.target.value);
  }

  return (
    <div>
      <Search value={search} onChange={handleChange}>
        Search:
      </Search>

      <p>Searches for {search ? search : '...'}</p>
    </div>
  );
}

function Search({ value, onChange, children }) {
  return (
    <div>
      <label htmlFor="search">{children}</label>
      <input
        id="search"
        type="text"
        value={value}
        onChange={onChange}
        />
    </div>
  );
}

export default App;
```

```html
<body>
  <div>
    <div>
      <div>
        <label
          for="search"
        >
          Search:
        </label>
        <input
          id="search"
          type="text"
          value=""
        />
      </div>
      <p>
        Searches for
        ...
      </p>
    </div>
  </div>
</body>
```

使用 React Testing Library 测试 React 组件，就像用户操作页面一样进行测试。用户通常看到的是 React 组件渲染后的 HTML，所以，在上面的 debug 输出中也是 HTML 结构，而不是 React 组件。

## 4. 查询元素

React Testing Library 除了提供了 render 函数用于渲染组件外，还提供了一系列 API（在 screen 对象上）用于查询元素。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    screen.getByText('Search:');
  });
});
```

查询到元素后，可以对这些元素进行交互或者断言。下面演示一个例子，断言元素是否在 DOM 上。

```javascript
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    expect(screen.getByText('Search:')).toBeInTheDocument();
  });
});
```

默认情况下，如果找不到元素，getByText 会抛出一个错误。在编写测试用例时，使用 getByText 的抛错特性，有利于开发者对测试用例进行调试。一些开发者会利用 getByText 的这个特性，来隐式断言元素是否存在于 DOM 中。但是，更推荐使用 expect 来做显式断言，因为 getByText 抛错会阻止测试程序继续执行下去。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    // 隐式断言，因为如果找不到元素，会抛错
    screen.getByText('Search:');

    // 显式断言，推荐！！！
    expect(screen.getByText('Search:')).toBeInTheDocument();
  });
});
```

getByText 函数的参数可以是字符串，也可以是正则表达式，如果是字符串，则表示精确匹配，如果是正则表达式，则表示模糊匹配。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    // fails
    expect(screen.getByText('Search')).toBeInTheDocument();

    // succeeds
    expect(screen.getByText('Search:')).toBeInTheDocument();

    // succeeds
    expect(screen.getByText(/Search/)).toBeInTheDocument();
  });
});
```

getByText 只是 React Testing Library 提供的其中一个查询元素 API，RTL 还提供了很多其它的查询元素 API。

## 5. 查询类型

在上一个章节中，我们使用 getByText 来查询元素，其中 text 是**查询类型**。getByText 是 React Testing Library 中比较常用的查询元素方法。

RTL 还提供了另一个强大的查询类型 role，对应的方法是 getByRole。getByRole 是基于元素的 [aria-label](https://developer.mozilla.org/en-US/search?q=Using%20the%20aria-label%20attribute) 属性来查询元素的，并且一些 HTML 元素也提供了隐式的 role 属性，比如 button 元素的 role 默认是 button。

使用 getByRole 的好处是，如果查询的 role 在渲染的组件 HTML 中不存在，控制台会提示所有可用于查询的 role。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    screen.getByRole('');
  });
});
```

上面的测试在命令行中执行后，会输出如下的结果。

```bash
Unable to find an accessible element with the role ""

Here are the accessible roles:

document:

Name "":
<body />

--------------------------------------------------
textbox:

Name "Search:":
<input
  id="search"
  type="text"
  value=""
/>

--------------------------------------------------
```

由于我们的 App 组件中有一个输入框（input 元素），且输入框隐式的 role 是 `textbox`，因此，可以通过 `textbox` 查询到该 input 元素。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    expect(screen.getByRole('textbox')).toBeInTheDocument();
  });
});
```

通常，没有必要为了测试而给 HTML 元素添加 aria role，因为一些 HTML 元素提供了隐式的 role。也正因此，getByRole 也是比较推荐的查询方法。

还有一些更特定于元素的查询类型。

- LabelText，getByLabelText，`<label for="search" />`
- PlaceholderText，getByPlaceholderText，`<input placeholder="Search" />`
- AltText，getByAltText，`<img alt="profile" />`
- DisplayValue，getByDisplayValue，`<input value="JavaScript" />`

React Testling Library 还提供了最后一个查询类型 TestId，对应的 API 是 getByTestId。使用该查询方法，需要在源码的 HTML 元素上添加 `data-testid` 属性。

最后，推荐在 RTL 中查询元素，推荐 getByText 和 getByRole。

下面列举了所有 React Testing Library 中的查询方法。

- getByText
- getByRole
- getByLabelText
- getByPlaceholderText
- getByAltText
- getByDisplayValue

## 6. 查询变体

React Testing Library 中还有个概念叫**查询变体**。其中一个我们比较熟悉的查询变体是 getBy，比如上文用到的查询方法 getByText、getByRole。getBy 也是我们在测试组件时默认使用的查询变体。

React Testling Library 提供的其它两个查询变体是 queryBy 和 findBy。对于上一节中的**查询类型**，queryBy 和 findBy 都有相应的查询方法。

queryBy 对应所有查询类型的方法如下。

- queryByText
- queryByRole
- queryByLabelText
- queryByPlaceholderText
- queryByAltText
- queryByDisplayValue

findBy 对应所有查询类型的方法如下。

- findByText
- findByRole
- findByLabelText
- findByPlaceholderText
- findByAltText
- findByDisplayValue

### getBy 与 queryBy 的区别

那么问题来了，何时使用 getBy？何时使用 queryBy、findBy？

上文中提到，getBy 要么返回查询到的元素，要么直接报错。报错也是 getBy 比较方便的一个特性，可以帮助开发者快速发现错误的测试用例写法。

然而，getBy 无法用于测试元素不存在的场景。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    screen.debug();

    // fails
    expect(screen.getByText(/Searches for JavaScript/)).toBeNull();
  });
});
```

上面用例中的断言写法是无效的，因为尽管 debug 输出的结果中并不存在 text 是 "Searches for JavaScript" 的元素，但是在我们断言之前，测试程序就报错了。

为了断言不存在的元素，可以使用 queryBy。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    expect(screen.queryByText(/Searches for JavaScript/)).toBeNull();
  });
});
```

所以，断言不存在的元素时，使用 queryBy，否则使用 getBy，那么什么时候使用 findBy？

### 何时使用 findBy

findBy 用于查询异步元素（一开始不存在，最终会显示的元素）。

比如有这样一个 React 组件：初始渲染完后，组件调用一个模拟的 user 接口，获取 user 对象，并调用 setState，触发组件更新和重新渲染，最后渲染出 "Signed in as ..."。

```jsx
const getUser = () => {
  return Promise.resolve({ id: '1', name: 'Robin' });
};

function App() {
  const [search, setSearch] = React.useState('');
  const [user, setUser] = React.useState(null);

  React.useEffect(() => {
    const loadUser = async () => {
      const user = await getUser();
      setUser(user);
    };

    loadUser();
  }, []);

  function handleChange(event) {
    setSearch(event.target.value);
  }

  return (
    <div>
      {user ? <p>Signed in as {user.name}</p> : null}

      <Search value={search} onChange={handleChange}>
        Search:
      </Search>

      <p>Searches for {search ? search : '...'}</p>
    </div>
  );
}

...
```

由于组件中调用了异步接口，组件在初次渲染后，会再次触发渲染。所以测试该组件的话，需要写一个异步测试来等待异步的 Promise resolve。换句话说，必须等待组件获取异步数据并重新渲染。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', async () => {
    render(<App />);

    expect(screen.queryByText(/Signed in as/)).toBeNull();

    expect(await screen.findByText(/Signed in as/)).toBeInTheDocument();
  });
});
```

组件初次渲染后，我们使用 queryByText 来断言 "Signed in as" 不存在。然后等待该 text 在 Promise resolve、组件重新渲染后最终被找到。可以加上两个 debug 方法，在命令行验证输出。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', async () => {
    render(<App />);

    expect(screen.queryByText(/Signed in as/)).toBeNull();

    screen.debug();

    expect(await screen.findByText(/Signed in as/)).toBeInTheDocument();

    screen.debug();
  });
});
```

最后做个总结：如果测试异步元素，使用 findBy；如果断言不存在的元素，使用 queryBy；否则，使用 findBy。

### 多个元素

我们已经学习了三个查询变体：getBy、queryBy、findBy，这些**查询变体**都可以与**查询类型**（比如 Text、Role、PlaceholderText、DisplayValue）组合成**查询方法**。但是这些查询方法只能返回单个元素，如果有多个元素（比如列表）的话，怎么处理呢？

所有的查询变体都可以加上 All，扩展成新的查询变体，并且这些查询变体也都可以与查询类型进行组合。

- getAllBy
- queryAllBy
- findAllBy

## 7 断言函数

断言函数是指断言语句右边的部分。在之前的测试中，我们已经使用了两个断言函数：toBeNull、toBeInTheDocument。这两个断言函数比较基础，在 React Testing Library 中用于检查元素存在与否。

Jest、Vitest、Chai 都原生提供这些断言函数。同时，React Testing Library 也扩展了这些 API，创建了自身的断言函数，比如 toBeInTheDocument。

扩展的断言函数都导出于 @testing-library/jest-dom 包，如果使用 create-react-app 创建项目，该包是默认安装的。

下面列举了所有的断言函数。

- toBeDisabled
- toBeEnabled
- toBeEmpty
- toBeEmptyDOMElement
- toBeInTheDocument
- toBeInvalid
- toBeRequired
- toBeValid
- toBeVisible
- toContainElement
- toContainHTML
- toHaveAttribute
- toHaveClass
- toHaveFocus
- toHaveFormValues
- toHaveStyle
- toHaveTextContent
- toHaveValue
- toHaveDisplayValue
- toBeChecked
- toBePartiallyChecked
- toHaveDescription

## 8 事件触发

目前为止，我们学习了使用查询函数 getBy/queryBy 测试组件中某个元素是否被渲染，使用查询函数 findBy 测试重新渲染后的组件中是否有预期的某个元素。真实的用户交互是什么样的呢？如果用户在输入框中进行输入，组件会触发重新渲染，输入框中会展示新的值。

可以使用 React Testing Library 提供的 fireEvent、waitFor 函数来模拟用户的交互。下面看看它们是如何使用的。

```jsx
import * as React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    screen.debug();

    fireEvent.change(screen.getByRole('textbox'), {
      target: { value: 'JavaScript' },
    });

    screen.debug();
  });
});
```

fireEvent 函数接收两个参数：元素（通过 textbox role 查出来的元素）、事件（value 值是 “JavaScript”），事件触发前后，调用 debug 函数输出 HTML 结构，最后可以看到输入框中的新值 “JavaScript” 被正确地渲染出来了。

注意，如果组件中有异步任务，比如上面的 App 组件中调用了接口获取 user 对象，对该组件进行测试后，可以在控制台看到警告 “*Warning: An update to App inside a test was not wrapped in act(...).*”。这意味着，有异步任务正在进行，因此必须确保在测试代码中对此进行处理。通常这种情况是在 React Testing Library 中的 act 函数中处理的，我们需要做的是使用异步查询函数进行等待即可。

```jsx
describe('App', () => {
  it('renders App component', async () => {
    render(<App />);

    // wait for the user to resolve
    await screen.findByText(/Signed in as/);

    screen.debug();

    fireEvent.change(screen.getByRole('textbox'), {
      target: { value: 'JavaScript' },
    });

    screen.debug();
  });
});
```

我们也可以在触发事件前后使用断言。

```jsx
describe('App', () => {
  it('renders App component', async () => {
    render(<App />);

    // wait for the user to resolve
    await screen.findByText(/Signed in as/);

    expect(screen.queryByText(/Searches for JavaScript/)).toBeNull();

    fireEvent.change(screen.getByRole('textbox'), {
      target: { value: 'JavaScript' },
    });

    expect(screen.getByText(/Searches for JavaScript/)).toBeInTheDocument();
  });
});
```

我们也可以使用 RTL 中的 waitFor 函数结合 getBy 变体等待异步更新（替代 findBy）。

```jsx
import * as React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders App component', () => {
    render(<App />);

    expect(screen.queryByText(/Searches for JavaScript/)).toBeNull();

    fireEvent.change(screen.getByRole('textbox'), {
      target: { value: 'JavaScript' },
    });

    waitFor(() =>
      expect(
        screen.getByText(/Searches for JavaScript/)
      ).toBeInTheDocument()
    );
  });
});
```

在上面的测试中，在事件之前，我们使用了 queryBy 来断言元素不存在；在事件之后，使用了 getBy 来断言元素存在。有时候，也会看到一些开发者在事件之后使用 queryBy 来断言元素存在，因为如果元素必然存在的话，queryBy 和 getBy 是类似的。

在测试用例中，除了一些异步操作需要单独处理以外，RTL 的 fireEvent 函数可以直接使用，并且可以在之后进行断言。

### User Event

基于 fireEvent 的 API，React Testlint Library 封装了一个用户事件库。相对于 fireEvent API，userEvent API 可以更接近地模拟浏览器行为。

比如，`fireEvent.change()` 只能触发 change 事件，而 `uerEvent.type()` 除了能触发 change 事件以外，还触发了 keyDown、keyPress 和 keyUp 事件。

```jsx
import * as React from 'react';
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

import App from './App';

describe('App', () => {
  it('renders App component', async () => {
    render(<App />);

    // wait for the user to resolve
    await screen.findByText(/Signed in as/);

    expect(screen.queryByText(/Searches for JavaScript/)).toBeNull();

    await userEvent.type(screen.getByRole('textbox'), 'JavaScript');

    expect(
      screen.getByText(/Searches for JavaScript/)
    ).toBeInTheDocument();
  });
});
```

React Testing Library 中，尽可能使用 userEvent，而不是 fireEvent。目前来说，userEvent 并没有完全涵盖 fireEvent 的所有特性，但是未来一定会的。

## 9 回调函数

有时候，开发者会以单元测试的形式单独测试 React 组件。通常这些组件没有副作用、状态，只有输入（props）、输出（JSX、回调处理）。我们已经知道如何在给定组件和 props 的情况下测试渲染的 JSX。

现在，我们来测试 Search 组件的回调函数。

```jsx
function Search({ value, onChange, children }) {
  return (
    <div>
      <label htmlFor="search">{children}</label>
      <input
        id="search"
        type="text"
        value={value}
        onChange={onChange}
      />
    </div>
  );
}

export { Search };
```

使用 Vitest/Jest 提供的测试函数来模拟传递给组件的 onChange 函数。在文本框触发了用户交互后，我们可以断言 onChange 回调函数被调用了。

```jsx
describe('Search', () => {
  it('calls the onChange callback handler', () => {
    // Jest
    // const onChange = jest.fn();
    // Vitest
    const onChange = vi.fn();

    render(
      <Search value="" onChange={onChange}>
        Search:
      </Search>
    );

    fireEvent.change(screen.getByRole('textbox'), {
      target: { value: 'JavaScript' },
    });

    expect(onChange).toHaveBeenCalledTimes(1);
  });
});
```

通过这个用例，我们也可以证明，相较于 fireEvent，userEvent 能更近似地模拟用户行为：fireEvent 触发 change 事件，回调函数只会执行一次，而 userEvent 触发 change 事件，有多少次按键，回调函数就会执行多少次。

```jsx
describe('Search', () => {
  it('calls the onChange callback handler', async () => {
    // Jest
    // const onChange = jest.fn();
    // Vitest
    const onChange = vi.fn();

    render(
      <Search value="" onChange={onChange}>
        Search:
      </Search>
    );

    await userEvent.type(screen.getByRole('textbox'), 'JavaScript');

    expect(onChange).toHaveBeenCalledTimes(10);
  });
});
```

无论如何，React Testing Library 推荐开发者不要测试单独的组件，而尽量测试整合了其它组件的组件。只有这样，才能真正测试出 state 的变化会怎样影响 DOM，以及副作用是否生效了。

## 10 异步处理

这节通过一个小案例来测试异步获取数据。

下面是一个 React 组件，使用 Axios 请求一个远程 API 来获取数据。

```jsx
import * as React from 'react';
import axios from 'axios';

const URL = 'http://hn.algolia.com/api/v1/search';

function App() {
  const [stories, setStories] = React.useState([]);
  const [error, setError] = React.useState(null);

  async function handleFetch(event) {
    let result;

    try {
      result = await axios.get(`${URL}?query=React`);

      setStories(result.data.hits);
    } catch (error) {
      setError(error);
    }
  }

  return (
    <div>
      <button type="button" onClick={handleFetch}>
        Fetch Stories
      </button>

      {error && <span>Something went wrong ...</span>}

      <ul>
        {stories.map((story) => (
          <li key={story.objectID}>
            <a href={story.url}>{story.title}</a>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```
