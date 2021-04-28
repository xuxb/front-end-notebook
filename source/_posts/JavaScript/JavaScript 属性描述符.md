---
title: JavaScript 属性描述符
categories: JavaScript
tags: [JavaScript 基础]
date: 2021-04-27 20:24:48
---

# 属性描述符

用来描述属性的属性

* 数据属性
  * `value`：该属性对应的值
  * `writable`：该属性是否可写
  * `enumerable`：该属性是否能被枚举（ `for...in` 循环和 `Object.keys()` ）
  * `configurable`：该属性是否可配置（控制 `writable`、`enumerable`等属性）
* 访问器属性
  * `set()`：调用赋值运算符时，执行对应的方法
  * `get()`：调用.运算符时，执行对应的方法

**note：**
`value`、`writable` 不可与 `set()`、`get()` 同时存在

```js
var obj = {
  name: "xxb"
};

Object.defineProperty(obj, "age", {
  enumerable: true,
  configurable: true,
  get: function () {
    console.log("---");
    return 18;
  },
  set: function (newVal) {
    console.log("+++++");
  }
});
obj.age = 1231231;
```