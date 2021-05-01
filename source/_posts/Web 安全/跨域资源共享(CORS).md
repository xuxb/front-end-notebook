---
title: 跨域资源共享（CORS）
categories: Web 安全
tags: [web 安全]
date: 2021-01-14 15:43:00
---

# 跨域资源共享（CORS）

跨域资源共享（Cross-Origin Resource Sharing）

A 网站可以请求 B 网站的图片、字体、CSS文件、javascript文件等，该请求是一个跨域请求，但出于安全考虑，浏览器会限制 javascript 发起的跨域请求，即 XMLHttpRequest 和 Fetch 必须遵循同源策略。
	  
**跨域请求分为简单请求和复杂请求**
	  
1. 对于简单请求，可将请求直接发送到另一个域的服务器
2. 对于复杂请求，会首先发送一个预检请求到另一个域的服务器（使用 Options 方法），以此来获知服务器是否允许该实际请求
	* 如果预检请求返回结果中，服务器允许了实际请求，即响应头中包含了 `Access-Control-Allow-Origin`、`Access-Control-Allow-Headers` 等信息，则再发送一个实际请求
	* 如果预检请求失败，服务器不允许实际请求，如响应报文未包含了正确CORS响应头，那么实际请求会被浏览器拦截，并可在控制台中看到报错信息

> 所以跨域请求失败的原因，其实是从预检请求中得知另一个域的服务器不支持跨域，而导致真实的请求被浏览器拦截，因此浏览器肯定会发起至少一次跨站请求（**简单请求**、**预检请求**或者**预检请求 + 复杂请求**）

*   javascript 要实现 CORS 需要浏览器和服务器同时支持，（现代浏览器 IE10+）
*   对于现代浏览器，在发起跨域 ajax 请求时 ，会添加 Origin 字段，表明请求来自哪个源
*   对于服务器端，须在响应头中添加 Access-Control-Allow-Origin 字段，表示接受某一域名的请求

<br/>

*   相比 JSONP ，CORS 更加安全，可靠；
*   JSONP 只能实现 GET 请求，而 CORS 支持所有类型的 HTTP 请求；
*   CORS 比 JSONP 有更好的错误处理，并能携带更多的数据；

```
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/access-control/simpleXSInvocation.html
Origin: http://foo.example`
`HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2.0.61 
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml
```

> 某些请求不会触发 CORS 预检请求。本文称这样的请求为“简单请求”，请注意，该术语并不属于 Fetch （其中定义了 CORS）规范。若请求满足所有下述条件，则该请求可视为“简单请求”：

1. 使用下列方法之一： GET HEAD POST
2. Fetch 规范定义了对 CORS 安全的首部字段集合，不得人为设置该集合之外的其他首部字段。该集合为：Accept Accept-Language Content-Language Content-Type DPR Downlink Save-Data Viewport-Width Width
3. Content-Type 的值仅限于下列三者之一：text/plain multipart/form-data application/x-www-form-urlencoded
4. 请求中的任意XMLHttpRequestUpload 对象均没有注册任何事件监听器；XMLHttpRequestUpload 对象可以使用 XMLHttpRequest.upload 属性访问。
5. 请求中没有使用 ReadableStream 对象。