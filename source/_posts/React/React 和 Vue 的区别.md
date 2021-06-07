---
title: React 和 Vue 的区别
categories: React
tags: [React]
date: 2021-05-26 16:32:38
---

# React 和 Vue 的区别

* 数据是否可变
  * React 整体是函数式的思想，把组件设计成纯组件，状态和逻辑通过参数传入，所以在 React中，是单向数据流，推崇结合 immutable 来实现数据不可变
  * Vue 的思想是响应式的，也就是基于是数据可变的，通过对每一个属性建立 Watcher 来监听，当属性变化的时候，响应式的更新对应的虚拟dom。
  * 总之，React 的性能优化需要手动去做，而 Vue 的性能优化是自动的，但是Vue的响应式机制也有问题，就是当 state 特别多的时候，Watcher 也会很多，会导致卡顿，所以大型应用（状态特别多的）一般用 React，更加可控。
* 通过 js 来操作一切，还是用各自的处理方式: 
  * React 的思路是all in js，通过js来生成html，所以设计了jsx，还有通过js来操作css，社区的 styled-component、jss等; 
  * Vue 是把html，css，js组合到一起，用各自的处理方式，Vue有单文件组件，可以把html、css、js写到一个文件中，html提供了模板引擎来处理。
* 类式的组件写法，还是声明式的写法: 
  * React是类式的写法，api 很少; 
  * Vue 是声明式的写法，通过传入各种 options，api 和参数都很多。
  * 所以 React 结合 typescript 更容易一起写，Vue 稍微复杂。
* 扩展不同: 
  * React 可以通过高阶组件（Higher Order Components--HOC）来扩展，
  * Vue 需要通过 mixins 来扩展。
* 功能内置: 
  * React做的事情很少，很多都交给社区去做
  * Vue很多东西都是内置的，写起来确实方便一些