---
title: 变量提升、this 指向、运算符优先级问题
categories: JavaScript 编程题
tags: [编程题]
date: 2021-03-12 15:32:28
---

# 变量提升、this 指向、运算符优先级问题

```js
function Foo() {
    getName = function () { alert (1); };
    return this;
}
Foo.getName = function () { alert (2);};
Foo.prototype.getName = function () { alert (3);};
var getName = function () { alert (4);};
function getName() { alert (5);}

//请写出以下输出结果：
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```

**分析：**
* 第一问的 `Foo.getName` 指向 Foo 函数上存储的静态属性
* 第二问的 `getName` 指向的是 `function () { alert (4);}`
  * 通过 `var` 声明的变量挂载在 `window` 对象下
  * 函数 `function getName() { alert (5);}` 会被提升到最前面
  * 语句 `var getName = function () { alert (4);};` 会先进行变量提升，值为 `undefined`，然后执行到赋值语句时 `getName` 指向 `function () { alert (4);}`
* 第三问的 `Foo().getName()` 最终指向 `function () { alert (1); }`
  * `Foo()` 执行时会将 `getName` 赋值为 `function () { alert (1); }`
  * `Foo()` 执行完成后的返回值为 `this`，此时 `this` 指向 `window`
  * 相当于执行 `window.getName()`
* 第四问的 `getName`，相当于执行 `window.getName`
* 第五问的 `new Foo.getName()` 相当于是 `new (Foo.getName)()`
* 第六问的 `new Foo().getName()` 相当于是 `(new Foo()).getName()`
* 第七问的 `new new Foo().getName()` 相当于是 `new ((new Foo()).getName)()`

```js
// ---答案---

Foo.getName(); // 2
getName(); // 4
Foo().getName(); // 1
getName(); // 1
new Foo.getName(); // 2
new Foo().getName(); // 3
new new Foo().getName(); // 2
```

### 相关链接
1. [前端同学经常忽视的一个 JavaScript 面试题](https://mp.weixin.qq.com/s?__biz=MzAwNjI5MTYyMw==&mid=2651498460&idx=1&sn=ab7433e025580f16b15ba48f81e7e907&chksm=80f1ae14b78627029edc3da2b3c6f4e4a9e0d5f442450e05debe25d0b94a67c3c0e01ce7be00&mpshare=1&scene=1&srcid=&sharer_sharetime=1588991552981&sharer_shareid=2bb1f9aa633b7a93bef080397533d2b0&exportkey=A85BTrV0JxNfJ%2B%2BlzGb8tTU%3D&pass_ticket=LD4J1MD7GBXWreZj7%2Bi2eqbeE9x6ynqxeR6QPJbW8ptOZX7CKzTf6gl0R48lxB9a#rd)