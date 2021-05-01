---
title: JS 链式调用与流程控制
categories: JavaScript 编程题
tags: [编程题]
date: 2021-03-12 15:32:28
---

# JS 链式调用与流程控制

* 能链式调用
* 能控制程序调用时机（休眠）
* 同步函数在同一个执行栈中执行

```js
const Person = function() {
  this.tasks = [];

  setTimeout(() => {
    this.next();
  }, 0);

  return this;
}
Person.prototype.next = function() {
  const fn = this.tasks.shift();
  fn && fn();
}
Person.prototype.eat = function() {
  const fn = () => {
    console.log('eat');
    this.next();
  };
  this.tasks.push(fn);
  return this;
}
Person.prototype.lunch = function() {
  const fn = () => {
    console.log('lunch');
    this.next();
  };
  this.tasks.push(fn);
  return this;
}
Person.prototype.sleep = function(seconds) {
  const fn = () => {
    console.log(`sleep ${seconds} seconds`);
    setTimeout(() => {
      this.next();
    }, seconds * 1000)
  };
  this.tasks.push(fn);
  return this;
}

const person = new Person();

person.eat().sleep(2).lunch().sleep(3).sleep(3)
```