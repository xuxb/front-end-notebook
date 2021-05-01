---
title: HTTP 协议
categories: HTTP
tags: [HTTP]
date: 2021-01-14 15:39:00
---

# HTTP 协议

### 1、定义
HTTP协议即超文本传送协议 (Hypertext Transfer Protocol )，是建立在TCP协议之上的一种应用。HTTP连接最显著的特点是客户端发送的每次请求都需要服务器回送响应，在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为 "一次连接"。

1. 在HTTP 1.0 中客户端的每次请求都要求建立一次单独的连接，在处理完本次请求后，就自动释放连接。
2. 在HTTP 1.1 中可以在一次连接中处理多个请求，并且多个请求可以重叠进行，不需要等待一个请求结束后再发送下一个请求。


由于 HTTP 在每次请求结束后都会主动释放连接，因此 HTTP 连接是一种"短连接"要保持客户端程序的在线状态，需要不断地向服务器发起连接请求。通常的做法是即时不需要获得任何数据，客户端也保持每隔一段固定的时间向服务器发送一次“保持连接”的请求，服务器在收到该请求后对客户端进行回复，表明知道客户端“在线”。若服务器长时间无法收到客户端的请求，则认为客户端“下线”，若客户端长时间无法收到服务器的回复，则认为网络已经断开。

**http报文是在http应用程序之间传递的数据块，这些数据描述了报文的内容和含义，以及数据部分**

### 2、组成

报文的组成：起始行、首部、主体

```
// http 请求报文
GET /home/index.html http/1.0 //起始行

Host: www.abc.com //首部
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8 //首部


// http 响应报文
HTTP/1.0 200 OK //起始行

Content-type:text/plain //首部
Content-length:19 //首部

Hi I'm a message! 主体
```


### 3、首部头信息

* 通用首部: 提供报文相关的最基本的信息，可在请求报文、响应报文中使用
    * Connection: 表示是否需要持久连接
    * Date: 表示消息发送的时间，服务器响应中要包含这个头部
    * Cache-Control: 指定请求和响应遵循的缓存机制
    * Via: 表示从请求到响应经过了哪些代理服务器
* 请求首部
    * 信息性首部
        * Host：客户端指定自己想访问的WEB服务器的域名/IP 地址和端口号
        * Referer：浏览器向WEB 服务器表明自己是从哪个网页URL获得点击当前请求中的网址
        * Range: 指定第一个字节的位置和最后一个字节的位置
        * User-Agent：发送操作系统与浏览器的名称与版本号
    * Accept 首部
        * Accept：告诉WEB服务器自己接受什么介质类型，/ 表示任何类型，type/* 表示该类型下的所有子类型，type/sub-type
        * Accept-Chartset：浏览器告诉服务器自己能接收的字符集
        * Accept-Encoding：浏览器申明自己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate）
        * Accept-Language：浏览器申明自己接收的语言。语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等
    * 条件请求首部
        * If-Match：如果对象的 ETag 没有改变（对象没有改变），才执行请求的动作，获取文档。
        * If-None-Macth：如果对象的 ETag 改变了（对象也改变了），才执行请求的动作，获取文档。
        * If-Modified-Since：如果请求的对象在该头部指定的时间之后修改了，才执行请求的动作（比如返回对象），否则返回代码304，告诉浏览器该对象没有修改。
        * If-Unmodified-Since：如果请求的对象在该头部指定的时间之后没修改过，才执行请求的动作（比如返回对象）。
    * 安全请求首部
        * Authorization：当客户端接收到来自WEB服务器的 WWW-Authenticate 响应时，用该头部来回应自己的身份验证信息给WEB服务器
        * Cookie：由客户端发送，包含在HTTP请求的头部中。注意，只有cookie的domain和path与请求的URL匹配才会发送这个cookie。
    * 代理请求首部
        * Proxy-Authorization：浏览器响应代理服务器的身份验证请求，提供自己的身份信息。
* 响应首部
    * 信息性首部
        * Age：当代理服务器用自己缓存的实体去响应请求时，用该头部表明该实体从产生到现在经过多长时间了
        * Server：WEB 服务器表明自己是什么软件及版本等信息。例如：Server：Apache/2.0.61 (Unix)
        * Vary：WEB服务器用该头部的内容告诉 Cache 服务器，在什么条件下才能用本响应所返回的对象响应后续的请求。假如源WEB服务器在接到第一个请求消息时，其响应消息的头部为：Content-Encoding: gzip; Vary: Content-Encoding，那么Cache服务器会分析后续请求消息的头部，检查其Accept-Encoding，是否跟先前响应的Vary头部值一致，即是否使用相同的内容编码方法，这样就可以防止Cache服务器用自己Cache里面压缩后的实体响应给不具备解压能力的浏览器。例如：Vary：Accept-Encoding。
    * 安全响应首部
        * Set-Cookie：由服务器发送，它包含在响应请求的头部中。它用于在客户端创建一个Cookie。
        * Proxy-Authenticate：代理服务器响应浏览器，要求其提供代理身份验证信息。
        * Access-Control-Allow-Origin：origin 参数的值指定了允许访问该资源的外域 URI（Ajax 跨域资源共享）
* 实体首部
    * 信息性首部
        * Allow：服务器支持哪些请求方法（如GET、POST等）。
        * Location：表示客户应当到哪里去提取文档，用于将接收端定位到资源的位置（URL）上。Location通常不是直接设置的，而是通过HttpServletResponse的sendRedirect方法，该方法同时设置状态代码为302
    * 实体内容首部
        * Content-Encoding:告知客户端服务器对实体的主体部分选用的内容编码方式。（gzip/compress/deflate/identity)
        * Content-Language:告知客户端，实体主体使用的自然语言。（中文或英文等语言）
        * Content-Length：表明了实体主体部分的大小。
        * Content-Location:给出与报文主体返回资源对应的URI。
        * Content-MD5：是一串由MD5算法生成的值，其目的在于检查报文主体在传输过程中是否保持完整，以及确认传输到达。
        * Content-Range:针对范围请求，返回响应时使用的首部字段，能告知客户端作为相应返回的实体的哪个部分符合范围请求。
        * Content-Type:说明了实体主体内对象的媒体类型，该字段用type/subtype形式赋值。
    * 实体缓存首部
        * Etag：就是一个对象（比如URL）的标志值，就一个对象而言，比如一个html文件，如果被修改了，其Etag也会别修改，所以，ETag的作用跟Last-Modified的作用差不多，主要供WEB服务器判断一个对象是否改变了。比如前一次请求某个html文件时，获得了其 ETag，当这次又请求这个文件时，浏览器就会把先前获得ETag值发送给WEB服务器，然后WEB服务器会把这个ETag跟该文件的当前ETag进行对比，然后就知道这个文件有没有改变了。
        * Expires：WEB服务器表明该实体将在什么时候过期，对于过期了的对象，只有在跟WEB服务器验证了其有效性后，才能用来响应客户请求。是 HTTP/1.0 的头部。例如：Expires：Sat, 23 May 2009 10:02:12 GMT
        * Last-Modified：WEB服务器认为对象的最后修改时间，比如文件的最后修改时间，动态页面的最后产生时间等等。例如：Last-Modified：Tue, 06 May 2008 02:42:43 GMT
    * 实体编码首部
        * Accept-Encoding：客户端发送给服务器，表明自己可以接受哪些编码（gzip、compress、deflate、identity）
        * Content-Encoding: 服务器发送给客户端，告诉客户端以哪种方式解码 （gzip、compress、deflate、identity）

### 4、HTTP 状态码
* 100 Continue
* 200 OK
* 201 Created 请求已经被实现，而且有一个新的资源已经依据请求的需要而建立，且其 URI 已经随Location 头信息返回 (常规使用场景是作为 PUT 请求的返回值)
* 202 Accepted 服务器已接受请求，但尚未处理
* 204 No Content（无内容）
* 206 Partial Content（使用断点续传）
* 301 Moved Permanently
* 302 Move Temporarily
* 304 Not Modified
* 400 Bad Request（不合规范的请求）
* 401 Unauthorized（未授权）
* 403 Forbidden（禁止访问）
* 404 Not Found（文件找不到）
* 500 Internal Server Error（内部服务器错误）
* 503 Service Unavailable（服务不可用）
* 504 Gateway Timeout

### 5、Get 与 Post 请求的区别
* Get 请求能缓存，Post 不能
* Post 相对 Get 安全一点点，因为Get 请求都包含在 URL 里，且会被浏览器保存历史纪录，Post 不会，但是在抓包的情况下都是一样的。
* Post 可以通过 request body来传输比 Get 更多的数据，Get 没有这个技术
* URL有长度限制，会影响 Get 请求，但是这个长度限制是浏览器规定的，不是 RFC 规定的
* Post 支持更多的编码类型且不对数据类型限制