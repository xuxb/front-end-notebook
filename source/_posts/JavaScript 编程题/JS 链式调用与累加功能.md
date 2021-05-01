---
title: JS 链式调用与累加功能
categories: JavaScript 编程题
tags: [编程题]
date: 2021-03-12 15:32:28
---

# JS 链式调用与累加功能

## 1. 实现 add 函数使得满足以下条件

**需满足的条件：**
* add(1).sum() //=> 1
* add(1)(2).sum() //=> 2
* add(1)(2)(3).sum() //=> 6
* add(1)(2)(3)(4).sum() //=> 10

```js
function add(a) {
  let total = a
  const fn = function(b) {
    total += b
    return fn
  }
  fn.sum = function() {
    return total
  }

  return fn
}
add(1).sum() // => 1
add(1)(2).sum() //=> 3
add(1)(2)(3).sum() //=> 6
add(1)(2)(3)(4).sum() //=> 10
```

## 2. 实现 add 函数使得满足以下条件

**需满足的条件:**
* add(1) // => 1;
* add(1)(2) //=> 3;
* add(1)(2)(3) //=> 6;
* add(1)(2)(3)(4) //=> 10;

```js
function add(num1) {
  const sum = function(num2) {
    num1 += num2
    return sum
  }
  sum.toString = function() {
    return num1
  }

  return sum
}

add(1) // output: 1
add(1)(2) // output: 3
add(1)(2)(3) // output: 4
```

**需满足的条件：**
* add(1); // 1
* add(1)(2); // 3
* add(1)(2)(3); // 6
* add(1)(2, 3); // 6
* add(1, 2)(3); // 6
* add(1, 2, 3); // 6

```js
function add(...args1) {
  let total = args1.reduce((a, b) => a + b, 0)
  const sum = function(...args2) {
    total = args2.reduce((a, b) => a + b, total)
    return sum
  }

  sum.toString = function() {
    return total
  }

  return sum
}
add(1) // output: 1
add(1)(2) // output: 3
add(1)(2)(3) // output: 6
add(1)(2, 3) // output: 6
add(1, 2)(3) // output: 6
add(1, 2, 3) // output: 6
```

