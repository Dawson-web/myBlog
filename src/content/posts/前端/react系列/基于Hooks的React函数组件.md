---
title: 基于Hooks的React函数组件
tags: [React系列]
category: React系列
published: 2024-9-12 21:22:25
draft: false 
image: /前端/react系列/基于Hooks的React函数组件/cover.webp

---

---

## 前言

自从16.8版本发布以来，React引入了Hooks，使得函数组件也可以拥有类组件的state和生命周期，并且更加简洁明了。
相比于类组件，函数组件拥有一下优点：

- 代码更加简洁，不需要定义类，不需要使用繁琐的`this`关键字
- 代码更加可读，函数组件的代码更加直观，易于理解
- 代码更加易于测试，函数组件的代码更加独立，易于测试
- 代码更加易于维护，函数组件的代码更加模块化，易于维护
- 大大提高了代码的复用性，减少了代码的冗余

但在16.8之前，由于未引入Hooks，函数组件无法拥有state和生命周期，因此只能用于展示组件，无法用于业务逻辑处理。而Hooks的出现，使得函数组件也可以拥有state和生命周期，从而可以用于业务逻辑处理。

## State

讨论之前我们需要先知道什么是 state ，state 是组件内部的状态，是React组件的记忆单元，用于保存组件的内部状态，状态的变化会触发组件的重新渲染已更新UI

但是函数组件没有state，因此我们需要使用Hooks来添加state，而Hooks添加的state是局部的，只在当前组件中有效，不会影响到其他组件。

以此也就引发了我的疑问，函数组件没有自己的state,是通过Hooks映入的，那么函数组件的state是如何确定私有性的呢以及存储在哪里呢？

先保留这个疑问，下一篇文章再做解答

## Hooks

### useState

`useState`是React提供的用于在函数组件中添加state的Hook。它接受一个初始state作为参数，并返回一个包含当前state和更新state的函数的数组。可以用来管理一个组件比较简单的一组状态

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
    Count: {count}   
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>    

  );
}
```
#### useState更新状态

对于 useState 的机制就不过多赘述了，而其中又涉及到了渲染相关的概念，此处简单总结一下：

- useState 是异步执行的（不会立即更新 state 的结果）
- 在同步模式下（或者说是 React 能够监听控制的情况下），React 会按照顺序执行所有的更新，但如果同时更新同一个状态 React 的 batchUpdate 机制会进行合并更新，React只会执行一次更新,以减少渲染开销。
- React 18 引入了 Concurrent Mode，可以监听到异步更新，因此也会进行 batchUpdate，但 React 18 仍无法监听到 Promise、Async/Await 等异步更新，因此仍会立即执行更新。
- 在某些异步的情况下如 SetTimeout、Promise、Async/Await 等，React 无法监听到，因此不会进行 batchUpdate，React 会立即执行更新，导致多次渲染。
- 出了上述的情况，也能使用 useEffect 绑定 state ，从而实现同步更新。

### useRef

useRef is a React Hook that lets you reference a value that's not needed for rendering.

源自官网的描述，除此之外可以用来管理一个组件比较复杂的一组状态，也可以用来保存一些不需要触发渲染的状态，如DOM元素、定时器等。

> 与 useState 不同的是 useRef 是同步更新的，且不会触发组件的重新渲染，因此可以用来保存一些不需要渲染的状态，如DOM元素、定时器等。

不过大家似乎更钟爱于使用 useRef 来绑定DOM元素

```jsx
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

### useReducer

`useReducer`是React提供的用于在函数组件中管理复杂state的Hook。它接受一个reducer函数和一个初始state作为参数，并返回一个包含当前state和dispatch函数的数组。可以用来管理一个组件比较复杂的一组状态。如果状态管理相对简单可以使用useReducer，但复杂度上升后建议使用Redux进行管理，不过我个人更喜欢使用react-query。

```jsx
import React, { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>Increment</button>
    </div>
  );
}

```

### useEffect

useEffect hook 用来实现一些副作用，可以在某些变量更新时重绘组件。

默认情况下，每次 state 有改变的时候，都会调用 useEffect 函数。如果需要更改触发的时机， 那么需要使用 useEffect 的第二个参数来指定监听的事件或者说状态。当第二个参数只使用一个空 数组 [] 的时候就只在组件加载的时候调用。数组的变量表示观察这些变量。

```jsx
import React, { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  },[count]);

  return (
    <div>
      Count: {count}
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### useContext

`useContext`是React提供的用于在函数组件中访问上下文的Hook。它接受一个上下文对象作为参数，并返回该上下文的当前值。可以用来管理全局状态，如主题、语言等。
其实就当作是Vue中的 Provider 和 inject 就差不多了
不过听我在字节的朋友说他们那边还是通过props传递的，所有的state全部放在顶层，看个人的使用习惯吧

```jsx
import { createContext, useContext } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  return (
    <ThemeContext.Provider value="dark">
      <Form />
    </ThemeContext.Provider>
  )
}

function Form() {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
      <Button>Log in</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className}>
      {children}
    </button>
  );
}

```


## 参考

https://yifei.me/note/2046
https://react.dev/reference/react/hooks

---