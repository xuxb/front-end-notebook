---
title: Performance API
categories: 浏览器
tags: [Performance, 性能]
date: 2021-04-27 17:21:48
---

# Performance API

## performance.timing

performance对象是全局的，它的 timing 属性是一个对象，它包含了各种与浏览器性能有关的时间数据，提供浏览器处理网页各个阶段的耗时。

![文档加载时间图](https://image-static.segmentfault.com/306/110/3061105775-56fde6dc11135_articlex)

![性能指标](https://pic1.zhimg.com/80/v2-e4e26420d6b681b2b15b9edf9db9d4d0_1440w.jpg)

performance.timing对象包含下列属性（全部只读）：
* **navigationStart**：当前浏览器窗口的前一个网页关闭，发生unload事件时的Unix毫秒时间戳。如果没有前一个网页，则等于fetchStart属性。
* **unloadEventStart**：如果前一个网页与当前网页属于同一个域名，则返回前一个网页的unload事件发生时的Unix毫秒时间戳。如果没有前一个网页，或者之前的网页跳转不是在同一个域名内，则返回值为0。
**unloadEventEnd**：如果前一个网页与当前网页属于同一个域名，则返回前一个网页unload事件的回调函数结束时的Unix毫秒时间戳。如果没有前一个网页，或者之前的网页跳转不是在同一个域名内，则返回值为0。
**redirectStart**：返回第一个HTTP跳转开始时的Unix毫秒时间戳。如果没有跳转，或者不是同一个域名内部的跳转，则返回值为0。
* **redirectEnd**：返回最后一个HTTP跳转结束时（即跳转回应的最后一个字节接受完成时）的Unix毫秒时间戳。如果没有跳转，或者不是同一个域名内部的跳转，则返回值为0。
* **fetchStart**：返回浏览器准备使用HTTP请求读取文档时的Unix毫秒时间戳。该事件在网页查询本地缓存之前发生。
* **domainLookupStart**：返回域名查询开始时的Unix毫秒时间戳。如果使用持久连接，或者信息是从本地缓存获取的，则返回值等同于fetchStart属性的值。
* **domainLookupEnd**：返回域名查询结束时的Unix毫秒时间戳。如果使用持久连接，或者信息是从本地缓存获取的，则返回值等同于fetchStart属性的值。
* **connectStart**：返回HTTP请求开始向服务器发送时的Unix毫秒时间戳。如果使用持久连接（persistent connection），则返回值等同于fetchStart属性的值。
* **connectEnd**：返回浏览器与服务器之间的连接建立时的Unix毫秒时间戳。如果建立的是持久连接，则返回值等同于fetchStart属性的值。连接建立指的是所有握手和认证过程全部结束。
* **secureConnectionStart**：返回浏览器与服务器开始安全链接的握手时的Unix毫秒时间戳。如果当前网页不要求安全连接，则返回0。
* **requestStart**：返回浏览器向服务器发出HTTP请求时（或开始读取本地缓存时）的Unix毫秒时间戳。
* **responseStart**：返回浏览器从服务器收到（或从本地缓存读取）第一个字节时的Unix毫秒时间戳。
* **responseEnd**：返回浏览器从服务器收到（或从本地缓存读取）最后一个字节时（如果在此之前HTTP连接已经关闭，则返回关闭时）的Unix毫秒时间戳。
* **domLoading**：返回当前网页DOM结构开始解析时（即Document.readyState属性变为“loading”、相应的readystatechange事件触发时）的Unix毫秒时间戳。
* **domInteractive**：返回当前网页DOM结构结束解析、开始加载内嵌资源时（即Document.readyState属性变为“interactive”、相应的readystatechange事件触发时）的Unix毫秒时间戳。
* **domContentLoadedEventStart**：返回当前网页DOMContentLoaded事件发生时（即DOM结构解析完毕、所有脚本开始运行时）的Unix毫秒时间戳。
* **domContentLoadedEventEnd**：返回当前网页所有需要执行的脚本执行完成时的Unix毫秒时间戳。
* **domComplete**：返回当前网页DOM结构生成时（即Document.readyState属性变为“complete”，以及相应的readystatechange事件发生时）的Unix毫秒时间戳。
* **loadEventStart**：返回当前网页load事件的回调函数开始时的Unix毫秒时间戳。如果该事件还没有发生，返回0。
* **loadEventEnd**：返回当前网页load事件的回调函数运行结束时的Unix毫秒时间戳。如果该事件还没有发生，返回0。


![加载流程](https://pic4.zhimg.com/80/v2-d80be1162b4d17d7c6ff265c26e30c3f_1440w.jpg)

关键指标数据如下：
* DNS解析时间：`domainLookupEnd - domainLookupStart`
* TCP建立连接：`connectEnd - connectStart`
* 发送请求：`responseStart - requestStart`
* 接收请求：`responseEnd - responseStart`
* TTFB：`responseStart - fetchStart`
* 白屏时间：`domLoading - fetchStart`
* 解析 DOM 树：`domInteractive - domLoading`
* 首次可交互时间：`domContentLoadedEventEnd - fetchStart` （粗略计算）
* DOM Ready 时间：`domComplete - fetchStart`
* 页面完全加载时间：`loadEventStart - fetchStart`
* DOMContentLoaded 事件耗时：`domContentLoadedEventEnd - domContentLoadedEventStart`
* DOMLoad 事件耗时：`loadEventEnd - loadEventStart`