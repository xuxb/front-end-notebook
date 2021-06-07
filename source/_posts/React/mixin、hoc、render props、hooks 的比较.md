---
title: mixin、hoc、render props、hooks 的比较
categories: React
tags: [React]
date: 2021-05-26 16:32:38
---

# mixin、hoc、render props、hooks 的比较

### mixin （已废弃）

本质上就是将对象复制到另一个对象上，类似于 `Object.assign()`

**缺点：**
* 组件与 Mixin 之间存在隐式依赖
* 不同 mixins 之间可能会有先后顺序甚至代码冲突覆盖的问题
* mixins 之间可能会有先后顺序甚至代码冲突覆盖的问题


### hoc

高阶组件（ higher-order component），类似于高阶组件接受一个组件作为参数，返回另一个组件

> 高阶函数是函数式编程中一个基本的概念，它描述了一种这样的函数：接受函数作为输入，或是返回一个函数，比如 map, reduce等都是高阶函数。

**缺点：**
* 需要在原组件上进行包裹和嵌套，如果大量使用 HOC，将会产生非常多的嵌套，使调试变得非常困难
* 固定的 props 可能会被覆盖.

### render props

说明：通过 props 接受一个返回 react element 的函数，来动态决定自己要渲染的结果

**缺点：**
* 嵌套过深
* 写法较繁琐

### hooks

**缺点：**
* 额外的学习成本
* 在闭包场景可能会引用到旧的state、props值
