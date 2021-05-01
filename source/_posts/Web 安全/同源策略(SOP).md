---
title: 同源策略（SOP）
categories: Web 安全
tags: [web 安全]
date: 2021-01-14 15:44:00
---

# 同源策略（SOP）

同源策略（Same Origin Policy）

定义：除非所处的两个页面的协议、DNS域名、端口都完全一致，否则两个独立的 JavaScript 运行环境不能访问彼此的 DOM，其他任何跨文档的 JavaScript DOM 访问也会失败。

说明：两个 URL 的协议、域名和端口相同，则表示他们同源。对于不同源的 **document** 或脚本，对当前 **document** 读取或设置某些属性。

> `协议`、`元素`、`端口`三者组成的算法叫做 **源**。

> 同源策略开始时只是用于现在对 DOM 结构的访问，之后扩展为保护 JavaScript 对跟对象下的某些敏感数据，如 `cookie`、`localStoreage`、Ajax 请求等，但同源策略机制并非包含所有，如非同源的脚本对任意窗口调用 `location.assign()` 、`location.replace()`。

作用：防止跨域读写某些资源，为浏览器提供沙箱环境，使进程在一个相对独立的空间运行，能在一定程度上保护浏览器安全。

> 同源策略无法和全局身份认证、SSL 状态、网络上下文环境以及众多有浏览器管理涉及安全的其他参数进行同步
> A 网站载入 B 网站的脚本，则该脚本的源为 A 网站，只有和当前页文档同源的脚本才会被执行
> 在浏览器中，`<script>`、`<img>`、`<iframe>`、`<link>` 等标签都可以加载跨域资源，而不受同源限制
> XMLHttpRequest 受到同源策略的约束，不能够跨域访问资源，可通过目标域返回的 HTTP 头来授权是否允许跨域访问

### 1 跨文档通信的限制（相关网站间通信）
1. 设置 `document.domain`（比较复杂、同时也有安全问题，不推荐使用）
2. 使用 `windown.postMessage`、`window.onmessage`


### 2 XMLHttpRequest 的同源策略
老版本的 **XMLHttpRequest Level 1**，有如下几个缺点：
* 只支持文本数据的传送，无法用来读取和上传二进制文件。
* 传送和接收数据时，没有进度信息，只能提存在 没有完成。
* 受到"同域限制"（Same Origin: Origin  Po时，licy），只能向同一域名的服务器请求数据。

新版本的 **XMLHttpRequest Level 2**，新增了如下几个功能：
* 可以设置HTTP请求的时限（超时时间）
* 可以使用 FormData 对象管理表单数据
* 可以上传文件（二进制数据）
* 可以请求不同域名下的数据（跨域请求）
* 可以获取服务器端的二进制数据
* 可以获得数据传输的进度信息

> 跨域资源共享（CORS）是针对 XMLHttpRequrest 的意向建设性扩展，用这种方式发送跨域 HTTP 请求时，由于 XMLHttpRequest 受到同源策略的约束，请求结果会被浏览器拦截，当响应头存在 `Access-Control-Allow-Origin: Origin` 字段时，XMLHttpRequest 才能访问响应结果

### 3 Web Storeage 的同源策略

localStorage 对象实现的是与站点源性相关的持久存储，关闭浏览器之后 localStorage 任然有效，而 sessionStorage 则绑定了当前浏览器窗口，提供的是临时的缓存机制，在浏览回话结束之后被清除掉。

* localStorage 的大小大约为 5M，而 Cookie 的单个大小为 4K
* localStorage 的值类型限定为 string类型，在读取数据时需要进行一些转换
* localStorage 在浏览器的隐私模式下面是不可读取的
* localStorage 本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡
* localStorage 不能被爬虫抓取到


### 4 Cookie 的安全策略

Cookie 的出现要早于同源策略，所以两者在某些方面可能会存在一些问题。

网站服务器对 Cookie 提供了两个特殊的可单独设置的标记： **httpOnly** 和 **secure** 。
* **httpOnly** 标签可以禁止使用 **document.cookle** API 来访问页面的 Cookie ，这样即使页面真的被注入了恶意代码，也无法简单地用这个 API 复制出用户的身份信息。
* **secure** 标签使得 Cookie 无法被用在非加密协议的通道里，这样就必须使用 HTTPS 服务所以能防御主动攻击。

当依赖于 HTTP Cookies 做身份认证时：
* 使用 **httpOnly** 标签；设计 Web 应用时谨记不要让 Javascript 直接访问到身份授权的 Cookie 信息。需要把敏感 Cookie 的有效范围设呈得越小越好，最好不要指定 Cookie 的 domain 值。
* 如果应用是完全墓于 HTTPS 的，那 Cookie 就应该标记为 **secure** ，而且必须时 Cookie 注入有完善的处理（ HTTP 运行环境可能导致标记为 **secure** 的 Cookie 被覆盖，即使无法被读取）对 Cookie 进行加密签名也许能防范这种超出控制的墓改行为，但也仍然无法阻止受害者的 Cookie 被替换成另一组由合法方式获取的 Cookie 身份。