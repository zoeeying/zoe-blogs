# Fix the "not wrapped in act(...)" warning

*There are a few reasons you're getting this warning. Here's how you fix it.*

Imagine you have a component like this:

Username

Submit

Here's the code for that (if you were to do it with class components, don't worry a function version is coming later in the post):

```jsx
1class UsernameFormClass extends React.Component {
2  state = {status: 'idle', error: null}
3  handleSubmit = async event => {
4    event.preventDefault()
5    const newUsername = event.target.elements.username.value
6    this.setState({status: 'pending'})
7    try {
8      await this.props.updateUsername(newUsername)
9      this.setState({status: 'fulfilled'})
10    } catch (e) {
11      this.setState({status: 'rejected', error: e})
12    }
13  }
14  render() {
15    const {error, status} = this.state
16
17    return (
18      <form onSubmit={this.handleSubmit}>
19        <label htmlFor="username">Username</label>
20        <input id="username" />
21        <button type="submit">Submit</button>
22        <span>{status === 'pending' ? 'Saving...' : null}</span>
23        <span>{status === 'rejected' ? error.message : null}</span>
24      </form>
25    )
26  }
27}
```

So, let's imagine we want to write a test for this. Here's what you might write to verify that the component is working properly:

```jsx
1import * as React from 'react'
2import user from '@testing-library/user-event'
3import {render, screen} from '@testing-library/react'
4
5test('calls updateUsername with the new username', async () => {
6  const handleUpdateUsername = jest.fn()
7  const fakeUsername = 'sonicthehedgehog'
8
9  render(<UsernameForm updateUsername={handleUpdateUsername} />)
10
11  const usernameInput = screen.getByLabelText(/username/i)
12  await user.type(usernameInput, fakeUsername)
13  user.click(screen.getByText(/submit/i))
14
15  expect(handleUpdateUsername).toHaveBeenCalledWith(fakeUsername)
16})
```

Great! So now, if we make some sort of typo and not call the `updateUsername` function or we forget to call it with the new username then our test will fail and it provides us value.

But, what if we rewrite this to a function component with hooks? Let's try that:

```jsx
1function UsernameForm({updateUsername}) {
2  const [{status, error}, setState] = React.useState({
3    status: 'idle',
4    error: null,
5  })
6
7  async function handleSubmit(event) {
8    event.preventDefault()
9    const newUsername = event.target.elements.username.value
10    setState({status: 'pending'})
11    try {
12      await updateUsername(newUsername)
13      setState({status: 'fulfilled'})
14    } catch (e) {
15      setState({status: 'rejected', error: e})
16    }
17  }
18
19  return (
20    <form onSubmit={handleSubmit}>
21      <label htmlFor="username">Username</label>
22      <input id="username" />
23      <button type="submit">Submit</button>
24      <span>{status === 'pending' ? 'Saving...' : null}</span>
25      <span>{status === 'rejected' ? error.message : null}</span>
26    </form>
27  )
28}
```

And the cool thing about React Testing Library is because it's free of implementation details we can run those exact same tests with this refactored version of our component.

## The dreaded `act(...)` warning

Unfortunately, if we try that, we'll get this really annoying warning in the console that looks like this:

```
1console.error node_modules/react-dom/cjs/react-dom.development.js:530
2  Warning: An update to UsernameForm inside a test was not wrapped in act(...).
3
4  When testing, code that causes React state updates should be wrapped into act(...):
5
6  act(() => {
7    /* fire events that update state */
8  });
9  /* assert on the output */
10
11  This ensures that you're testing the behavior the user would see in the browser. Learn more at https://fb.me/react-wrap-tests-with-act
12      in UsernameForm
```

What the!? So what's going on here? Well, you might not have noticed, but our test didn't test our component's happy-path fully and left one important aspect vulnerable to regressions. Let's go back to the class version and comment out an important line of code:

```jsx
1class UsernameFormClass extends React.Component {
2  state = {status: 'idle', error: null}
3  handleSubmit = async event => {
4    event.preventDefault()
5    const newUsername = event.target.elements.username.value
6    this.setState({status: 'pending'})
7    try {
8      await this.props.updateUsername(newUsername)
9      // this.setState({status: 'fulfilled'})
10    } catch (e) {
11      this.setState({status: 'rejected', error: e})
12    }
13  }
14  render() {
15    const {error, status} = this.state
16
17    return (
18      <form onSubmit={this.handleSubmit}>
19        <label htmlFor="username">Username</label>
20        <input id="username" />
21        <button type="submit">Submit</button>
22        <span>{status === 'pending' ? 'Saving...' : null}</span>
23        <span>{status === 'rejected' ? error.message : null}</span>
24      </form>
25    )
26  }
27}
```

Guess what! Our tests still pass! But, the `Saving...` loading indicator next to our submit button never goes away:

Username

Submit

Oh no! It sure would've been nice if we'd had some warning that we weren't testing all that our component was doing so we could've written a good test from the start. Wait... Is that what `act` was yelling at us about? YES IT WAS!

So the `act` warning from React is there to tell us that something happened to our component when we weren't expecting anything to happen. So you're supposed to wrap every interaction you make with your component in `act` to let React know that we expect our component to perform some updates and when you don't do that and there are updates, React will warn us that unexpected updates happened. This helps us avoid bugs like the one described above.

Luckily for you and me, React automatically handles this for any of your code that's running within the React callstack (like click events where React calls into your event handler code which updates the component), but it cannot handle this for any code running outside of it's own callstack (like asynchronous code that runs as a result of a resolved promise you are managing or if you're using jest fake timers). With those kinds of situations you typically need to wrap that in `act(...)` or `async act(...)` yourself. BUT, React Testing Library has async utilities that are wrapped in `act` automatically!

## How to fix the `act(...)` warning

So, let's first thank the React team for letting us know that we didn't test everything that's happening in our component (thanks React team! 🙏) and then let's add an assertion to our test to cover the use case we forgot the first time around:

```jsx
1test('calls updateUsername with the new username', async () => {
2  const handleUpdateUsername = jest.fn()
3  const fakeUsername = 'sonicthehedgehog'
4
5  render(<UsernameForm updateUsername={handleUpdateUsername} />)
6
7  const usernameInput = screen.getByLabelText(/username/i)
8  await user.type(usernameInput, fakeUsername)
9  user.click(screen.getByText(/submit/i))
10
11  expect(handleUpdateUsername).toHaveBeenCalledWith(fakeUsername)
12  await waitForElementToBeRemoved(() => screen.getByText(/saving/i))
13})
```

Great, so now we're waiting for the saving text to be removed and that ensures that the saving text appears in the first place *and* is removed when saving is complete.

You'll notice that this test passes whether we're using class components or function components (+1 point for avoiding [Testing Implementation Details](https://kentcdodds.com/blog/testing-implementation-details)), but without that extra line we only get the warning with function components not class components. The reason for this is because the React team couldn't reasonably add this warning for class components without creating a TON of new warnings for people and their existing tests. So while the warning would probably help people find and fix bugs like this, the decision was made to only apply it to hooks so people would get the warning as they develop and test new components (+1 point for using function components over class components).

You can read more about React's `act` utility [here](https://reactjs.org/docs/test-utils.html#act), but again, you shouldn't have to use it very often. It's built-into React Testing Library. There are very few times you should have to use it directly if you're using [React Testing Library's async utilities](https://testing-library.com/docs/react-testing-library/cheatsheet#async).

If you're still experiencing the `act` warning, then the most likely reason is something is happening **after your test completes** for which you should be waiting (like in our earlier examples).

## An Alternative: waiting for the mocked promise

There's one alternative I want to show you that isn't quite as good for this use case, but could be useful, especially if there's no visual indication of the async task completing.

Because our code waits for the `updateUsername` promise to resolve before continuing, we could return a promise from our fake version and use an async act to await that promise resolution:

```jsx
1test('calls updateUsername with the new username', async () => {
2  const promise = Promise.resolve()
3  const handleUpdateUsername = jest.fn(() => promise)
4  const fakeUsername = 'sonicthehedgehog'
5
6  render(<UsernameForm updateUsername={handleUpdateUsername} />)
7
8  const usernameInput = screen.getByLabelText(/username/i)
9  await user.type(usernameInput, fakeUsername)
10  user.click(screen.getByText(/submit/i))
11
12  expect(handleUpdateUsername).toHaveBeenCalledWith(fakeUsername)
13  await act(() => promise)
14})
```

Note that we're manually calling `act` here and you can get that from `react-dom/test-utils` or React Testing Library re-exports it so you can get grab it from the import you already have. You're welcome.

This isn't preferable because it's still not going to catch the bug we demonstrated earlier by commenting out that `setState` call, but it does make the warning go away properly. I'd just recommend making additional assertions about the way things look after the async action has completed.

## Other use cases for manually calling `act(...)`

If you're using all the React Testing Library async utilities and are waiting for your component to settle before finishing your test and you're *still* getting act warnings, then you may need to use `act` manually. Here are a few examples:

### 1. When using `jest.useFakeTimers()`

Let's say you have a component that's checking against an API on an interval:

```jsx
1function OrderStatus({orderId}) {
2  const [{status, data, error}, setState] = React.useReducer(
3    (s, a) => ({...s, ...a}),
4    {status: 'idle', data: null, error: null},
5  )
6
7  React.useEffect(() => {
8    let current = true
9    function tick() {
10      setState({status: 'pending'})
11      checkStatus(orderId).then(
12        d => {
13          if (current) setState({status: 'fulfilled', data: d})
14        },
15        e => {
16          if (current) setState({status: 'rejected', error: e})
17        },
18      )
19    }
20    const id = setInterval(tick, 1000)
21    return () => {
22      current = false
23      clearInterval(id)
24    }
25  }, [orderId])
26
27  return (
28    <div>
29      Order Status:{' '}
30      <span>
31        {status === 'idle' || status === 'pending'
32          ? '...'
33          : status === 'error'
34          ? error.message
35          : status === 'fulfilled'
36          ? data.orderStatus
37          : null}
38      </span>
39    </div>
40  )
41}
```

So we've handled all the edge cases and cleaned up after ourselves nicely, but when we write our test for it, we're going to get the `act` warning again:

```jsx
1import * as React from 'react'
2import {render, screen} from '@testing-library/react'
3import {checkStatus} from '../api'
4
5jest.mock('../api')
6
7beforeAll(() => {
8  // we're using fake timers because we don't want to
9  // wait a full second for this test to run.
10  jest.useFakeTimers()
11})
12
13afterAll(() => {
14  jest.useRealTimers()
15})
16
17test('polling backend on an interval', async () => {
18  const orderId = 'abc123'
19  const orderStatus = 'Order Received'
20  checkStatus.mockResolvedValue({orderStatus})
21
22  render(<OrderStatus orderId={orderId} />)
23
24  expect(screen.getByText(/\.\.\./i)).toBeInTheDocument()
25  expect(checkStatus).toHaveBeenCalledTimes(0)
26
27  // advance the timers by a second to kick off the first request
28  jest.advanceTimersByTime(1000)
29
30  expect(await screen.findByText(orderStatus)).toBeInTheDocument()
31
32  expect(checkStatus).toHaveBeenCalledWith(orderId)
33  expect(checkStatus).toHaveBeenCalledTimes(1)
34})
```

The `act` warning here is happening because of this line:

```jsx
1// ...
2let current = true
3function tick() {
4  setState({status: 'pending'})
5  checkStatus(orderId).then(
6    d => {
7// ...
```

The `tick` function is happening outside of React's callstack, so it's unsure whether this interaction with the component is properly tested. React Testing Library does not have a utility for jest fake timers and so we need to wrap the timer advancement in `act` ourselves, like this:

```jsx
1import * as React from 'react'
2import {render, screen, act} from '@testing-library/react'
3import {checkStatus} from '../api'
4
5jest.mock('../api')
6
7test('polling backend on an interval', async () => {
8  const orderId = 'abc123'
9  const orderStatus = 'Order Received'
10  checkStatus.mockResolvedValue({orderStatus})
11
12  render(<OrderStatus orderId={orderId} />)
13
14  expect(screen.getByText(/\.\.\./i)).toBeInTheDocument()
15  expect(checkStatus).toHaveBeenCalledTimes(0)
16
17  // advance the timers by a second to kick off the first request
18  act(() => jest.advanceTimersByTime(1000))
19
20  expect(await screen.findByText(orderStatus)).toBeInTheDocument()
21
22  expect(checkStatus).toHaveBeenCalledWith(orderId)
23  expect(checkStatus).toHaveBeenCalledTimes(1)
24})
```

And now the React `act(...)` warning goes away!

Notice, that we can pull React DOM's `act` testing utility directly from `@testing-library/react` because it's simply re-exported by `@testing-library/react` (cool right!?).

### 2. When testing custom hooks

You'll get an `act(...)` warning with custom hooks when you call functions that are returned from your custom hook which result in state updates. Here's a simple example of that:

```javascript
1import * as React from 'react'
2
3function useCount() {
4  const [count, setCount] = React.useState(0)
5  const increment = () => setCount(c => c + 1)
6  const decrement = () => setCount(c => c - 1)
7  return {count, increment, decrement}
8}
9
10export default useCount
```

Here, we'll use `@testing-library/react-hooks` to validate our hook works:

```javascript
1import * as React from 'react'
2import {renderHook} from '@testing-library/react-hooks'
3import useCount from '../use-count'
4
5test('increment and decrement updates the count', () => {
6  const {result} = renderHook(() => useCount())
7  expect(result.current.count).toBe(0)
8
9  result.current.increment()
10  expect(result.current.count).toBe(1)
11
12  result.current.decrement()
13  expect(result.current.count).toBe(0)
14})
```

When we call the `increment` and `decrement` functions from our hook, that triggers a state update and because we're not in the React callstack we're going to get an `act(...)` warning. So, let's wrap that in `act(...)`!

```javascript
1import * as React from 'react'
2import {renderHook, act} from '@testing-library/react-hooks'
3import useCount from '../use-count'
4
5test('increment and decrement updates the count', () => {
6  const {result} = renderHook(() => useCount())
7  expect(result.current.count).toBe(0)
8
9  act(() => result.current.increment())
10  expect(result.current.count).toBe(1)
11
12  act(() => result.current.decrement())
13  expect(result.current.count).toBe(0)
14})
```

You'll notice that we're pulling `act` from `@testing-library/react-hooks` and that's because it simply re-exports the `act` function from `react-test-renderer` so you don't need to add an additional export. Nice right!?

### 3. When using `useImperativeHandle`

You may not have even heard of this hook, and if you have you may not have run into this problem. That's because you only run into this if you're calling methods directly on a component which do internal state updates and you're outside of React's callstack. Let's read through an example of act warnings popping up when testing components that use this hook.

So here's a pretty contrived example, but you should be double-thinking your decisions any time you use this hook anyway:

```jsx
1function ImperativeCounter(props, ref) {
2  const [count, setCount] = React.useState(0)
3  React.useImperativeHandle(ref, () => ({
4    increment: () => setCount(c => c + 1),
5    decrement: () => setCount(c => c - 1),
6  }))
7  return <div>The count is: {count}</div>
8}
9ImperativeCounter = React.forwardRef(ImperativeCounter)
```

Here's how you might test this:

```jsx
1import * as React from 'react'
2import {render, screen, act} from '@testing-library/react'
3import ImperativeCounter from '../imperative-counter'
4
5test('can call imperative methods on counter component', () => {
6  const counterRef = React.createRef()
7  render(<ImperativeCounter ref={counterRef} />)
8  expect(screen.getByText('The count is: 0')).toBeInTheDocument()
9
10  counterRef.current.increment()
11  expect(screen.getByText('The count is: 1')).toBeInTheDocument()
12
13  counterRef.current.decrement()
14  expect(screen.getByText('The count is: 0')).toBeInTheDocument()
15})
```

You'll get an `act` warning on those `increment` and `decrement` calls because they're happening outside the React callstack. So, let's wrap them in `act`:

```jsx
1import * as React from 'react'
2import {render, screen, act} from '@testing-library/react'
3import ImperativeCounter from '../imperative-counter'
4
5test('can call imperative methods on counter component', () => {
6  const counterRef = React.createRef()
7  render(<ImperativeCounter ref={counterRef} />)
8  expect(screen.getByText('The count is: 0')).toBeInTheDocument()
9
10  act(() => counterRef.current.increment())
11  expect(screen.getByText('The count is: 1')).toBeInTheDocument()
12
13  act(() => counterRef.current.decrement())
14  expect(screen.getByText('The count is: 0')).toBeInTheDocument()
15})
```

## Conclusion

Hopefully you have a better understanding (and appreciation) for React's (sometimes annoying but always right) `act(...)` testing utility and the next time you get a warning you're able to identify the source of the problem and fix it more quickly.

Again, most of the time you shouldn't actually have to use `act` directly (thanks to [React Testing Library's async utilities](https://testing-library.com/docs/dom-testing-library/api-async)), but when you do, you'll know why it's needed and fixes the problem.

Good luck!

[2020-02-03](https://github.com/kentcdodds/kentcdodds.com/commits/main/content/blog/fix-the-not-wrapped-in-act-warning/index.mdx)

[Discuss on Twitter](https://twitter.com/search?q=https%3A%2F%2Fkentcdodds.com%2Fblog%2Ffix-the-not-wrapped-in-act-warning) • [Edit post on GitHub](https://github.com/kentcdodds/kentcdodds.com/edit/main/content/blog/fix-the-not-wrapped-in-act-warning/index.mdx)