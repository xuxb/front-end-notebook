---
title: React 优化
categories: React
tags: [React]
date: 2021-05-26 16:32:38
---

# React 优化

#### 使用 PureComponent、React.memo 进行浅比较

在 React 工作流中，如果只有父组件发生状态更新，即使父组件传给子组件的所有 Props 都没有修改，也会引起子组件的 Render 过程。

如果子组件的 Props 和 State 都没有改变，那么其生成的 DOM 结构和副作用也不应该发生改变。当子组件符合声明式设计理念时，就可以忽略子组件本次的 Render 过程，PureComponent 是对类组件的 Props 和 State 进行浅比较，React.memo 是对函数组件的 Props 进行浅比较。

#### 使用 useMemo、useCallback 实现稳定的 Props 值

如果传给子组件的派生状态或函数，每次都是新的引用，那么 PureComponent 和 React.memo 优化就会失效。所以需要使用 useMemo 和 useCallback 来生成稳定值，并结合 PureComponent 或 React.memo 避免子组件重新 Render

> useCallback、useMemo 应配合 React.memo 使用

#### 使用 useMemo 减少组件 Render 过程耗时

useMemo 是一种缓存机制提速，当它的依赖未发生改变时，就不会触发重新计算，用于派生属性，类似于 Vue 的 computed 属性。

#### 避免使用内联对象或内联函数

函数式组件在每次 render 时都会重新的定义一个新的对象，对于 prop 的浅层比较始终返回false，导致组件一直重新渲染，包括内联样式的引用。可在组件外部定义一个对象，将该对象传递给子组件即可。
内联函数也有同样的问题，每次 render 都是不一样的

#### 列表项使用 key 属性
#### 组件合理的拆分与解耦
#### 按优先级更新，及时响应用户
#### debounce、throttle 优化频繁触发的回调
#### 延迟加载组件


