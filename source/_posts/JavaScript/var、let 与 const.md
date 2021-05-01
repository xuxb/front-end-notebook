---
title: var、let 与 const
categories: JavaScript
tags: [JavaScript 基础]
date: 2021-04-27 14:24:48
---


# var、let 与 const 的区别

* 作用域
  * `var` 只有函数作用域
  * `let`、`const` 为块级作用域
* 变量提升
  *  `var` 会存在变量提升，提升至函数作用域的最上面，不包含赋值 
  *  `let`、`const` 不存在变量提升，在声明之前调用变量会报错
* 暂时性死区
  * `var` 声明不会有暂时性死区
  * `let`、`const` 声明会有暂时性死区，会报 `xxx is not defined`
* 重复声明
  * `var` 可以重复声明
  * `let`、`const` 不可以在同一作用域内重复声明
* 全局对象
  * `val` 声明的全局变量会挂在顶层对象(`window`、`global`)下面
  * `let`、`const` 声明的全局变量不会挂在顶层对象下

**暂时性死区的作用**：为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为

`const` 声明的变量需要注意的点：
1. `const` 需在初始化时赋值，且不可修改；
2. `const` 声明为变量时，其地址不可变，当变量为对象时，可修改属性；

**内存分配**

* `var`: 会直接在栈内存里预分配内存空间，然后在实际语句执行的时候，在存储对应的变量。如果是引用类型，那么会在堆内存里开开辟一个内存空间存储实际内容，栈内存会存储一个指向堆内存的指针。
* `let`: 不会在栈内存里预分配内存空间，而且在栈内存分配变量时，会做一个检查，如果已经有相同变量名存在，就会报错。

**变量提升**
给变量赋值的语句可以理解成三个步骤，创建、初始化、赋值

* `let`、`const` 的**创建**过程提升了，**初始化**过程没有提升，所以会产生**暂时性死区**
* `var` 的**创建**、**初始化**过程都提升了，所以在赋值前访问会得到 `undefined`
* `function` 的**创建**、**初始化**、**赋值**都被提升了

函数声明也会存在变量提升，且优先级比 `var` 高，函数赋值不会有变量提升

ES6 标准规定
* `var`、`function` 声明的全局变量， 是全局对象的属性
* `let`、`const`、`class` 声明的全局变量，不是全局对象的属性

```js
// var 声明会存在变量提升
(function() {
  console.log('name', name);
  console.log('typeof name', typeof name);

  if (typeof name === 'undefined') {
    var name = 'Jack'
    console.log('Goodbye ' + name);
  } else {
    console.log('Hello ' + name);
  }
})()

// ---- output -----
// name undefined
// typeof name undefined
// Goodbye Jack

// 说明： if 语句里的 var 声明没有块级作用域，会提升至函数的最上层
```