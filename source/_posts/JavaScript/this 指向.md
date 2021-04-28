---
title: this 指向
categories: JavaScript
tags: [JavaScript 原理]
date: 2021-04-27 17:24:48
---

# this 指向

### 1、默认绑定

独立函数调用时应用了 `this` 的默认绑定，`this` 指向全局对象


> 如果使用严格模式（`use strict;`），那么全局对象将无法使用默认绑定，因此 `this` 会绑定到 `undefined`
> node 环境中，每个模块被 `function (exports, require, module, __filename, __dirname) {...}` 包裹，所以 `this` 是 `{}` 对象，而函数中的 `this` 是 `global` 对象，而且通过 `var` 声明的变量不会挂载在 `global` 对象下


```js
// ---- 浏览器环境中 非严格模式------
var a = 2;
console.log(this); // window 对象
function foo () {
    console.log(this); // window 对象
    console.log(this.a); // 2
}
foo(); // 2

```

```js
// ---- 浏览器环境中 严格模式------
"use strict";
var a = 2;
console.log(this); // window 对象
function foo () {
    console.log(this); // undefined
    console.log(this.a); // TypeError
}
foo(); // 2

```

```js
// ---- node 环境中 ------
var a = 2;
console.log(this); // {}
function foo () {
    console.log(this); // global 对象
    console.log(this.a); // undefined
}
foo(); // 2
```

### 2、隐式绑定

调用位置是否有上下文对象（即该方法是否被某个对象所拥有），隐式绑定规则会把函数调用中的 `this` 绑定到这个上下文对象。

> 无论是直接在 obj 中定义还是先定义再添加为引用属性，这个函数严格来说都不属于 obj 对象（可以赋值给其他对象，改变上下文），调用位置会使用 obj 上下文来引用函数，因此可以说函数被调用时obj 对象“拥有”它

```js
// 对象属性引用链中只有最后一层会影响调用位置
function foo() {
    console.log(this.a);
}

var obj2 = {
    a: 42,
    foo
};
var obj1 = {
    a: 2,
    obj2
};

obj1.obj2.foo(); // 42

```

```js
//  ** 隐式丢失 **
//  被隐式绑定的函数丢失了绑定对象，则这个换上会应用默认绑定，从而把 this 绑定到全局对象或者 undefined 
//  `bar` 是 `obj.foo` 的一个引用，但实际上，它引用的是 `foo` 函数本身，此时的 `bar()` 其实是一个不带任何修饰的函数调用，应用了默认绑定。
var obj = {
    a: 2,
    foo: function () {
        console.log(this.a);
    }
};
var bar = obj.foo; // 函数别名！
var a = "oops, global"; // a 是全局对象的属性
bar(); // "oops, global"

```

> 回调函数会使 `this` 丢失或被修改

### 3、显式绑定

使用 `call()`、`apply()`、`bind()` 方法绑定 `this` 对象，动态切换上下文。

```js
// obj 对象绑定到 foo 函数
function foo() {
    console.log( this.a );
}
var obj = {
    a: 2
};
foo.call( obj ); // 2

```

> `bind()` 会返回一个硬编码的新函数，它会把参数设置为 `this` 的上下文并调用原始函数

### 4、new 绑定

发生函数调用时，会进行以下步骤：

1.  创建（或者说构造）一个全新的对象。
2.  这个新对象会被执行 [[ 原型 ]] 连接。
3.  这个新对象会绑定到函数调用的 this 。
4.  如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

```js
function foo(a) {
    this.a = a;
}
var bar = new foo(2);
console.log( bar.a ); // 2

```

### 5、箭头函数

箭头函数不使用 `this` 的四种标准规则，而是根据外层的词法作用域（函数或全局）来决定 `this` 。

```js
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}
var id = 21;
foo.call({ id: 42 }); // output ------> 42

// ------ 使用 traceur 编译后的代码如下： -----
function foo() {
  var $__1 = this;
  setTimeout(function() {
    console.log('id:', $__1.id);
  }, 100);
}
var id = 21;
foo({id: 42});  // output ------> 42

```

### 6、常见 this 指向总结
1.  在全局环境下，即在任何函数体外，`this` 指向全局对象（`window` 、`global`）；
2.  函数直接调用时，`this` 指向全局对象，如执行函数中嵌套的函数；
3.  对象方法中的 `this` ，与调用该方法的对象绑定；
4.  构造函数中的 `this` ，与即将被创建的新对象绑定；
5.  通过 `call` 和 `apply` 调用，函数中的 `this` 被动态绑定到第一个元素 ；
6.  通过 `bind` 方法传入对象，函数中`this` 将永久地被绑定到了 `bind` 的第一个参数；
7.  事件处理函数中的 `this`，指向监听器所在的 `DOM` 元素，与 `event.currentTarget` 一致；


### 7、判断 this 指向的步骤 
1. 首先查看函数调用的位置
2. 看这个函数是否被显示的使用 `call`、 `apply`、 `bind` 等显示绑定，如果是，则会在代码中明确说明 `this` 的指向，否则进行下一步
3. 看这个函数是被谁（对象、数组、函数等）调用，如果是用 `.` 操作符调用，则 `this` 指向该对象，否则进行下一步
4. 是否使用 `new` 关键字调用了该函数，如果是，`this` 指向由 JavaScript 解释器创建的新创建的对象，否则进行下一步
5. 是否在箭头函数中使用 `this`，如果是，则 `this` 指向父作用域中的上下文对象，否则进行下一步
6. 如果是使用严格模式，则 `this` 关键字是未定义的，否则 `this` 指向全局对象 `window` 或 `global`


### 8、注意事项
1. 函数的执行过程中调用位置如何决定 `this` 的绑定对象
2. 如果把 `null` 或者 `undefined` 作为 `this` 的绑定对象传入 `call` 、 `apply` 或者 `bind` ，这些值在调用时会被忽略，实际应用的是默认绑定规则
3. 绑定优先级：new 绑定 > 显式绑定 > 隐身绑定 > 默认绑定