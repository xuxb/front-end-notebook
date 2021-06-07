---
title: React Hooks
categories: React
tags: [React]
date: 2021-05-26 16:32:38
---

# React Hooks

Hooks 是辅助 Function Component 的一种工具，用于管理函数组件的状态、生命周期、副作用等

## 1. useState

### 1.1 使用方式

```js
const App = () => {
    const [count, setCount] = useState(0)
    const [name, setName] = useState('Jack')
    const [age, setAge] = useState(18)

    const onClick = () => {
        setCount(count + 1)
        setAge(age + 1)
    }

    return (
        <>
            <p>You clicked {count} times</p>
            <p>Your age is {age}</p>
            <p>Your name is {name}</p>
            <button onClick={onClick}>
                Click me
            </button>
        </>
    )
}

export default App
```

在 Class Component 中通过 this.state 方式读取 state，每次执行都会拿到最新的 state 引用，而 Function Component 中不是通过 this. 的方式获取到的 state，而是当时渲染闭包环境的 state 数据，所以取到的值依然是旧的渲染值

说明：每次调用 `setCount`、`setAge` 时都会重新执行整个函数式组件，包括初始化数据，调用 `useState`、`useEffect`，注册事件等，最后返回 `VNode` 对象（每次重新 `render` 组件时取出来的 `state` 是缓存中的值，即上一次 `state` 的值或初始值）

> useState 的初始值只在第一次渲染时有效，之后从 state 中取出的值都是缓存的值

### 1.2 实现原理
```js
// useState 基本实现原理
let memoizedState = [] // hooks 的值存放在这个数组里
let cursor = 0 // 当前 memoizedState 的索引

function useState(initialValue) {
    memoizedState[cursor] = memoizedState[cursor] || initialValue
    const currentCursor = cursor
    function setState(newState) {
        memoizedState[currentCursor] = newState
        cursor = 0
        render(<App />, document.getElementById('root'))
    }
    return [memoizedState[cursor++], setState] // 返回当前 state，并把 cursor 加 1
}
```

**useState 原理**
* 初次渲染时，每次 `useState` 执行时都会将对应的 `setState` 绑定到对应索引的位置（用于更新指定的 `state`），然后将初始 `state` 存入 `memoizedState` 中
* 触发 `setCount` 和 `setAge` 时，每个 `setState` 都有其对应索引的引用，因此触发对应的 `setState` 会改变对应位置的 `state` 的值
* 每次执行 `setState` 都有一次重新渲染的过程，将 `cursor` 重置为 `0`，重新按顺序取得 `state` 里面的值（已缓存上一次 `state` 里的值）

`Hooks` 需要确保 `Hook` 在每一次渲染中都按照同样的顺序被调用，因为 `memoizedState` 是按 `Hooks` 定义的顺序来放置数据的，如果 `Hooks` 的顺序变化，`memoizedState` 并不会感知到。所以最好每次只在最顶层使用 `Hook，不要在循环、条件、嵌套函数中调用` `Hooks。`


> `useState` 返回更新 `state` 的函数与 `class` 组件的 `this.setState` 不同，它不会把新的 `state` 和旧的 `state` 进行合并，而是直接替换，相当于直接返回一个新的对象

每次 setState 后都会重新渲染，渲染时的 state、props、useEffect、事件处理函数都是独立的，获取到的值是此次 rerender 时的数据

利用 useRef 就可以绕过 Capture Value 的特性。可以认为 ref 在所有 Render 过程中保持着唯一引用，因此所有对 ref 的赋值或取值，拿到的都只有一个最终状态，而不会在每个 Render 间存在隔离

## 2. useEffect
useEffect 在每次 render 执行完后再执行，用于处理数据副操作，用于在函数组件里实现 class 的生命周期

* useEffect(() => {}): 每次 render 后执行，类似于 `componentDidUpdate`
* useEffect(() => { return cleanUpFn }，[]): 挂载后执行一次，销毁时执行一次 cleanupFn，功能类似于 `componentDidMount` 和 `componentWillUnmount`
* useEffect(() => {}，[state1, state2]): 每次 state1 或 state2 变化后执行，功能类似于 Vue 中的 `watch`

> 在第一次渲染之后和每次更新之后都会执行，可以根据依赖项进行控制，执行顺序为 `setState` --> `render` --> `useEffect`
> useEffect 里面使用到的 state 的值, 固定在了 useEffect 内部（闭包环境），引用地址不会再变化

```js
useEffect(() => {
  const timer = setInterval(() => {
    console.log('---')
  }, 1000)

  return () => {
    clearInterval(timer)
  }
},[])

```

* 必须将 `函数` 写在 `useEffect` 内部，这样 `eslint-plugin-react-hooks` 才能通过静态分析补齐依赖项
* 所有函数都写在 useEffect 内部会导致难以维护，可以使用 `useCallback`

```js
function Counter() {
  const [count, setCount] = useState(0);

  const getFetchUrl = useCallback(() => {
    return 'https://v?query=' + count;
  }, [count]);

  useEffect(() => {
    getFetchUrl();
  }, [getFetchUrl]);

  return <h1>{count}</h1>;
}
```

## 3. useRef
`useRef` 可以用于访问 DOM 节点、React 组件实例或作为容器保存可变变量

> useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。通过 current 属性可以访问到 DOM 节点

```js
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    inputEl.current.focus(); // `current` 指向已挂载到 DOM 上的文本输入元素
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```


```js
// 获取上一轮的 props 或 state
function usePrevious(value) {
  const ref = useRef(); // 此处的 ref 相当于是全局变量，re-render 时并不会被回收
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}
```


## 4.useMemo
用于缓存值，避免在每次渲染时都进行高开销的计算

`const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);`

用于缓存值，把 “创建” 函数和依赖项数组作为参数传入 `useMemo`，它仅会在某个依赖项改变时才重新计算 `memoized` 值。这种优化有助于避免在每次渲染时都进行高开销的计算。类似于 Vue 中的 `computed` 属性。

> 函数组件里的某一个 state 变化时，都会重新执行整个函数并渲染，逻辑从上到下重新执行一遍

```js
export default function WithMemo() {
	const [count, setCount] = useState(1);
	const [val, setValue] = useState('');

	// 缓存了昂贵的计算
	const expensive = useMemo(() => {
		console.log('compute');
		let sum = 0;
		for (let i = 0; i < count * 100; i++) {
			sum += i;
		}
		return sum;
	}, [count]);

	return (
		<div>
			<h4>{count}-{expensive}</h4>
			<div>
				<button onClick={() => setCount(count + 1)}>+c1</button>            <input value={val} onChange={event => setValue(event.target.value)} />
			</div>
		</div>
	);
}
```

## 5. useCallback

`const memoizedCb = useMemo(() => doSomething(a, b), [a, b]);`

用于**缓存函数**，返回一个 memoized 回调函数，根据依赖项来决定是否更新函数

在 class component 中通过 shouldComponentUpdate 或 getDerivedStateFromProps 中判断前后的 props 和 state，如果没有变化，则阻止更新，而 16.8 版本前的 function component 没有生命周期钩子用来优化性能，且函数组件每次调用都会执行其内部环境的所有逻辑，因此用 useMemo 和 useCallback 处理优化性能问题。

> 把内联回调函数及依赖项数组作为参数传入 useCallback，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用。


> React.memo 为高阶组件。它与 React.PureComponent 非常相似，但只适用于函数组件，而不适用 class 组件，能对props做浅比较，防止组件无效的重复渲染

有一个父组件，其中包含子组件，子组件接收一个函数作为 props；通常而言，如果父组件更新了，子组件也会执行更新；但是大多数场景下，更新是没有必要的，我们可以借助useCallback来返回函数，然后把这个函数作为props传递给子组件；这样，子组件就能避免不必要的更新。

例如：父组件传递一个函数给子组件的时候，由于父组件的更新会导致该函数重新生成从而传递给子组件的函数引用发生了变化，这就会导致子组件也会更新，而很多时候子组件的更新是没必要的，所以我们可以通过 `useCallback` 来缓存该函数，然后传递给子组件，用于减少子组件不必要的重新渲染。

**note:** useCallback 需与 memo 结合使用，否则没有意义


```js
function useFetch(count, step) {
  return useCallback(() => {
    const url = "https://v/search?query=" + count + "&step=" + step;
  }, [count, step]);
}
```

```js
// 父组件
const Parent = () => {
	const [count, setCount] = useState(1);
	const [val, setVal] = useState('');
	const callback = useCallback(() => {
		return count;
	}, [count]);

	return (
		<div>
			<h4>{count}</h4>
			<Child callback={callback} />
			<div>
				<button onClick={() => setCount(count + 1)}>+</button>
				<input value={val} onChange={event => setVal(event.target.value)} />
			</div>
		</div>
	);
}

// 子组件
const Child = ({ callback }) => {
	const [count, setCount] = useState(() => callback());
	useEffect(() => {
		setCount(callback());
	}, [callback]);
	return <div>{count}</div>
}
export default React.memo(Child) // 用React.memo包裹
```

### 5.1 何时使用 useMemo、useCallback
* 需要保持对象、函数的引用地址相等（核心）
* 对于组件内部用到的 object、array、函数等，如果用在了其他 Hook 的依赖数组中，或者作为 props 传递给了下游组件，应该使用 useMemo 和 useCallback
* 自定义 Hook 中暴露出来的 object、array、函数等，都应该使用useMemo 和 useCallback,以确保当值相同时，引用不发生变化

React.memo 和 React.PureComponent 类似， React.PureComponent 在类组件中使用，而 React.memo 在函数组件中使用

```js
// 父组件
const Parent = () => {
	const [count, setCount] = useState(1);
	const [val, setVal] = useState('');
	const callback = useCallback(() => {
		return count;
	}, [count]);

	return (
		<div>
			<h4>{count}</h4>
			<Child callback={callback} />
			<div>
				<button onClick={() => setCount(count + 1)}>+</button>
				<input value={val} onChange={event => setVal(event.target.value)} />
			</div>
		</div>
	);
}

// 子组件
const Child = ({ callback }) => {
	const [count, setCount] = useState(() => callback());
	useEffect(() => {
		setCount(callback());
	}, [callback]);
	return <div>{count}</div>);
}
export default React.memo(Child)
	// 用React.memo包裹

```


## 6. useContext

接收一个 `context` 对象（React.createContext 的返回值）并返回该 context 的当前值。

```js
// initContext 可以为基础数据类型、对象或者函数
const context = React.useContext(React.createContext(initContext))
```

> 当前的 context 值由上层组件中距离当前组件最近的 `<MyContext.Provider>` 的 `value` **prop** 决定，如果 `<MyContext.Provider>` 没有提供 `value` **prop**，则 `context` 中获取到的值为 `React.createContext(initContext)` 中的 `initContext` 值

> 当 `<MyContext.Provider>` 更新时，该 Hook 会触发重渲染，并使用最新传递给 `MyContext provider` 的 `context` **value** 值


## 7. useReducer

```js
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

在某些场景下，useReducer 会比 useState 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。并且，使用 useReducer 还能给那些会触发深更新的组件做性能优化，因为你可以向子组件传递 dispatch 而不是回调函数 

> React 会确保 dispatch 函数的标识是稳定的，并且不会在组件重新渲染时改变。这就是为什么可以安全地从 useEffect 或 useCallback 的依赖列表中省略 dispatch


```js
const reducer =(state = 0, {type})=>{
    switch (type) {
        case 'add':
            return state+1
        case 'delete':
            return state-1
        default:
            return state;
    }
}

const Hook =()=>{
    const [count, dispatch] = useReducer(reducer, 0)
    return(
        <div>
           count:{count}
           <button onClick={()=> dispatch({type:'add'})}>add</button>
            <button onClick={()=> dispatch({type:'delete'})}>delete</button>
        </div>
    )
}

export default Hook
```


## 8. 自定义 hooks

**当触发 resize 事件时，自动更新 width、height**
```js
import { useEffect, useState } from 'react';

export const useWindowSize = () => {
	const [width, setWidth] = useState()
	const [height, setHeight] = useState()

	useEffect(() => {
		const { clientWidth, clientHeight } = document.documentElement
		setWidth(clientWidth)
		setHeight(clientHeight)
	}, [])

	useEffect(() => {
		const handleWindowSize = () => {
			const { clientWidth, clientHeight } = document.documentElement
			setWidth(clientWidth)
			setHeight(clientHeight)
		};

		window.addEventListener('resize', handleWindowSize, false)

		return () => {
			window.removeEventListener('resize', handleWindowSize, false)
		}
	})

	return { width, height }
}
```


## 9. 全局状态管理（useContext + useReducer）

1. 定义 `reducer` 函数、初始 `state` 值，通过 `useReducer(reducer, state)` 生成 `state` 和 `dispatch`
2. 父组件通过 `<Context.Provider>` 将 `state` 数据、`dispatch` 函数提供给 context
3. 子孙组件通过 `useContext()` 获取到 `state`、`dispatch`
4. 子孙组件通过 `state` 数据绑定到 VNode、触发事件时调用 `dispatch` 函数

```js
// store.js
import React, { createContext, useContext, useReducer } from 'react';

const StoreContext = createContext();
const initialState = {count: 0, message: '};

const reducer = (state, action) => {
  switch(action.type) {
    case 'increment':
      return {
        count: state.count + 1,
        message: action.message
      }
    case 'decrement':
      return {
        count: state.count - 1,
        message: action.message
      }
      case 'reset':
        return {
          count: 0,
          message: action.message
        }
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

export const StoreProvider = ({ children }) => {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <StoreContext.Provider value={{state, dispatch}}>
      {children}
    </StoreContext.Provider>
  )
}

export const useStore = () => useContext(StoreContext);
```

```js
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { StoreProvider } from './store';
import { ChildComponent } from './childComponent';

function App() {
  return (
    <StoreProvider>
      <ChildComponent/>
    </StoreProvider>
  );
}

const rootElement = document.getElementById('root');
ReactDOM.render(<App />, rootElement);
```

```js
// childComponent.js
import React from 'react';
import { useStore } from './store';

export const ChildComponent = () => {
  const {state, dispatch} = useStore();

  return (
    <div>
      {state.count}
      <button onClick={() => dispatch({type: 'increment', message:'Incremented'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement', message: 'Decremented'})}>-</button>
      <button onClick={() => dispatch({type: 'reset', message: 'Reset'})}>Reset</button>
      {state.message}
    </div>
  )
}
```


