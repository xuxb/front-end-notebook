---
title: JSONP 原理
categories: JavaScript
tags: [JSONP, 同源策略]
date: 2021-04-27 17:24:48
---

# JSONP 原理

由于同源策略的原因，浏览器限制了 Ajax 请求必须在同一个源中，即**统一协议、域名和端口号**

> 说明： 跨域的 Ajax 请求可以发出，但是必须在服务器端响应头中设置  Access-Control-Allow-Origin：“*”属性，否则在跨域请求的预检请求阶段（`OPTIONS`请求会携带 `Access-Control-Request-Method`、`Access-Control-Request-Headers`等头信息），如果后端不允许此次预检请求，则不会再发送后面真正的请求


以下标签可以跨域：
* <script src="..."></script>
* <img src="..." />
* <link rel="stylesheet" href="...">
* <iframe></iframe>

**JSONP （JSON with Padding）**
说明: 在浏览器端的 js 中声明回调函数之后，通过 `<script>` 标签向服务器跨域请求数据，服务器将请求结果包裹在回调函数中，并动态执行回调函数。
安全性： 是一种不安全的通信方式。 `<script>` 会执行跨域服务器中返回的任意 js 代码，只适用于可信的第三方脚本。
优点： 配置简单，支持旧版浏览器，但不安全，不支持 POST


示例：
* 浏览器端声明 `callbackfunction (data) { console.log(data); }`
* 动态创建 `<script>` 标签，并将 src 指向跨域地址 http://www.runoob.com/ajax?jsonp=callbackFunction
* 服务器端返回 `callbackFunction(["customername1","customername2"])`;
* 请求结束后，`<script>` 标签内的 js 代码会自动执行，jsonp 流程结束


```js
// jsonp 实现方式

function jsonp ({url, param, callback}) {
  return new Promise((resolve, reject) => {
    var script = document.createElement('script')
    window.callback = function (data) {
      resolve(data)
      document.body.removeChild(script)
    }
    var param = {...param, callback}
    var arr = []
    for (let key in param) {
      arr.push(`${key}=${param[key]}`)
    }
    script.src = `${url}?${arr.join('&')}`
    document.body.appendChild(script)
  })
}
```