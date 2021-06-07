---
title: React 生命周期
categories: React
tags: [React]
date: 2021-05-26 16:32:38
---

# React 生命周期

**旧版本生命周期**
![9913e024c3f2c4f65e3ef161b47ee06a.png](evernotecid://CE10AA85-EAB3-45B4-83E6-FF0E03D17746/appyinxiangcom/10250411/ENResource/p153)


> React 17 会添加异步渲染的功能，即一种可被打断的生命周期，而可以被打断的阶段正是实际 dom 挂载之前的虚拟 dom 构建阶段，也就是要被去掉的三个生命周期 componentWillMount，componentWillReceiveProps 和 componentWillUpdate，故不推荐使用

**新版本生命周期**
![ead00979613189c0c49222aa0bec56c9.png](evernotecid://CE10AA85-EAB3-45B4-83E6-FF0E03D17746/appyinxiangcom/10250411/ENResource/p151)

* 挂载阶段
    * `constructor(props)`: 构造函数会最先被执行，通常在构造函数里初始化 state 对象或者给自定义方法绑定 this
    * `static getDerivedStateFromProps(nextProps, prevState)`: 接收父组件传递过来的 props 和组件之前的状态，返回一个对象来更新 state ，或者返回 null 来表示接收到的 props 没有变化，不需要更新 state（配合 componentDidUpdate，可以覆盖 componentWillReceiveProps 的所有用法，在 React 16.4.0 版本中：在组件实例化、接收到新的 props 、组件状态更新时会被调用）
    * `render()`:  render 函数是纯函数，返回需要渲染的内容
    * `componentDidMount(prevProps, prevState, snapshot)`: 组件装载之后调用，此时我们可以获取到 DOM 节点并操作，比如对canvas，svg的操作，服务器请求，订阅都可以写在这个里面
* 更新阶段
    * `static getDerivedStateFromProps(nextProps, prevState)`: 此方法在更新和挂载阶段都会被调用
    * `shouldComponentUpdate(nextProps, nextState)`: 通常利用此生命周期来优化React程序性能，返回一个布尔值，true 表示会触发重新渲染，false 表示不会触发重新渲染，默认返回 true, 
    * `render()`: 更新阶段也会触发此生命周期
    * `getSnapshotBeforeUpdate(prevProps, prevState)`: 这个方法在 render 之后，componentDidUpdate 之前调用，有两个参数 prevProps 和 prevState，表示之前的 props 和之前的 state，这个函数有一个返回值，会作为第三个参数传给 componentDidUpdate，如果不想要返回值，可以返回null，此生命周期必须与 componentDidUpdate搭配使用
    * `componentDidUpdate(prevProps, prevState, snapshot)`: 该方法在 getSnapshotBeforeUpdate 方法之后被调用，有三个参数 prevProps，prevState，snapshot，表示之前的 props，之前的 state，和 snapshot。第三个参数是 getSnapshotBeforeUpdate 返回的，如果触发某些回调函数时需要用到 DOM 元素的状态，则将对比或计算的过程迁移至 getSnapshotBeforeUpdate，然后在 componentDidUpdate 中统一触发回调或更新状态。
* 卸载阶段
    * `componentWillUnmount()`:  当我们的组件被卸载或者销毁了就会调用，我们可以在这个函数里去清除一些定时器，取消网络请求，清理无效的DOM元素等垃圾清理工作

`componentDidUpdate` 钩子函数是在组件更新结束之后执行，在初始化 render 时不执行，如果在 componentDidMount 里面 setState 导致组件更新，组件更新后会执行 componentDidUpdate，此时在 componentDidUpdate 里面 setState 又会导致组件更新，造成成死循环了，如果要避免死循环，需要谨慎的在 componentDidUpdate 里面使用 setState，一般会在满足条件的情况下去使用 setState

> 最常见的误解就是 `getDerivedStateFromProps` 和 `componentWillReceiveProps` 只会在 props “改变”时才会调用。实际上**只要父组件重新渲染时，这两个生命周期函数就会重新调用，不管 props 有没有“变化”**

### 当外部的 props 改变时，如何再次执行请求数据？
* 使用 `componentWillReceiveProps` （不推荐使用）
* 使用 `getDerivedStateFromProps` + `componentDidUpdate`
* 使用 `key` 重新加载组件

### 当外部的 props 改变时，如何更改状态？
* 使用 `getDerivedStateFromProps`
* 使用 `componentDidUpdate`
* 使用 `key` 重新加载组件

### setState 是同步更新还是异步更新？

说明：调用 `setState()` 后，然后立即取 `state` 的值，**可能**并不能得到最新的值，看上去像是异步执行的。

答案: 有时表现出*异步*，有时表现出*同步*

调用 setState --> 放入延迟队列 --> 比较 props、state 并更新 DOM

> setState 的批量更新优化也是建立在“异步”（合成事件、钩子函数）之上的

在 React 的 `setState` 函数实现中，会根据一个变量 `isBatchingUpdates` 判断是直接更新 `this.state` 还是**放到队列中延时更新**，而 `isBatchingUpdates` 默认是 `false`，表示 `setState` 会同步更新 `this.state`；但是，有一个函数 `batchedUpdates`，该函数会把 `isBatchingUpdates` 修改为 `true`，而当 React 在调用事件处理函数(合成事件)之前就会先调用这个 `batchedUpdates` 将`isBatchingUpdates` 修改为 `true`，这样由 React 控制的事件处理过程 `setState` 不会同步更新 `this.state`。其他情况下，如原生事件和 `setTimeout` 中不会批量更新。

所以，在 React 应用中，如果在合成事件和钩子函数中调用 `setState` 会表现成异步，如果在原生事件和 `setTimeout` 中调用 `setState` 会表现成同步。

