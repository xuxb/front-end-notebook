---
title: Cookie
categories: Web 安全
tags: [web 安全, cookie]
date: 2021-01-14 15:45:00
---

# Cookie 

### Cookie
Cookie是网站为了辨别用户身份、进行 session 跟踪而储存在用户本地终端上的数据，是实现持久会话的最好方式

**cookie 属性**
| 选项 | 作用 |
| --- | --- |
| name | 设置 cookie 的名称 |
| value | 设置 cookie 的值 |
| domain | 设置可以访问此 cookie 的域名 |
| path | 设置可以访问此 cookie 的页面路径，比如domain是abc.com,path是/test，那么只有/test路径下的页面可以读取此cookie |
| expires/Max-Age | 设置字段为此cookie超时时间。若设置其值为一个时间，那么当到达此时间后，此cookie失效。不设置的话默认值是Session，意思是cookie会和session一起失效。当浏览器关闭(不是浏览器标签页，而是整个浏览器) 后，此cookie失效。 |
| size | 表示此 cookie 大小 |
| http | 设置 cookie 的 httponly 属性。若此属性为true，则只有在http请求头中会带有此cookie的信息，而不能通过document.cookie来访问此cookie。 |
| secure | 设置是否只能通过https来传递此条cookie |
| sameSite | 允许服务器要求某个cookie在跨站请求时不会被发送，从而可以阻止跨站请求伪造攻击（CSRF）|

**SameSite Cookies 可选字段**
* None: 浏览器会在同站请求、跨站请求下继续发送cookies，不区分大小写
* Lax: 在新版本浏览器中，为默认选项，Same-site cookies 将会为一些跨站子请求保留，如图片加载或者frames的调用，但只有当用户从外部站点导航到URL时才会发送。如 link链接，预加载请求，GET 表单
* Strict: 浏览器将只在访问相同站点时发送cookie。


**Cookie 的分类**
* 会话Cookie：不包含过期时间，该Cookie保存在内存中，当浏览器关闭时该Cookie失效
* 持久性Cookie：包含过期时间，该Cookie保存在硬盘内，当到达指定日期时，该Cookie失效


```
// 响应头中设置 Cookie
Set-Cookie: name1=value1 [ ;expires=date][ ;domain=domain][ ;path=path][ ;secure]
Set-Cookie: name2=value2 [ ;expires=date][ ;domain=domain][ ;path=path][ ;secure]
```

```
// 请求头中的 Cookie
Cookie: name1=value1; name2=value2;
```


```js
// js 操作 Cookie

/**
 * 根据cookie名称获取cookie值
 * @param  {String} name cookie名称
 * @return {[type]}      [description]
 */
function getCookie(name) {
  var arr = document.cookie.match(new RegExp("(^|\\W)" + name + "=([^;]*)(;|$)"));
  if (arr !== null) {
      return decodeURIComponent(arr[2]);
  }
  return '';
}

/**
 * @param {Object} options 参数配置
 * @param {String} options.name cookie的名称
 * @param {String} options.value cookie的值
 * @param {String} options.domain cookie访问权限域名，默认为当前域名
 * @param {String} options.path 默认为 /
 * @param {Date} options.expires 如果不设置或不是 Date 类型，则默认为会话 cookie
 * @return {scope}      this
 */
function setCookie(options) {
  var name = options.name;
  var value = options.value;
  var path = options.path || "/";
  var domain = options.domain;
  var expires = options.expires;
  var str = name + "=" + escape(value) + "; ";
  str += "path=" + path + "; ";
  if (domain) {
      str += "domain=" + domain + "; ";
  }
  if (expires instanceof Date) {
      str += "expires=" + expires.toGMTString() + "; ";
  }
  document.cookie = str;
  return this;
}

/**
* 删除 cookie
* @param  {String} name cookie 名称
* @param  {String} domain cookie 域
* @param  {String} path cookie 路径
* @return {scope}      this
*/
function deleteCookie(name, domain, path) {
  this.set({
      name: name,
      value: '',
      domain: domain,
      path: path,
      expires: new Date()
  });
  return this;
}
```


### Session
Session 是一种服务器端机制，具有保持连接的含义。当浏览器第一次访问时，服务器创建 Session，然后将 Session的 Id 以 Cookie 的形式发送回给浏览器，后续的访问会带上 Cookie，可用于识别用户，起到面向连接的作用。

Session 对象在一段时间（如20min）没被使用，就会被销毁，或手动销毁 Session

**注意事项**
* 用户访问某个网站，登录成功后，服务器后生成一个 Session 对象，并设置失效时间，用于跟踪用户的操作和状态，并将Session产生的唯一标识符保存在Cookie中（即通过 Set-Cookie 响应头返回给浏览器，假设为 SessionId ），在一段时间内，用户访问其他页面时，会将Cookie（包含SessionId）传回给服务器，以此模拟"登陆态"功能。
* 当用户关闭浏览器时，浏览器并不会发送任何信息到服务器，因此服务器端的 Session 依然有效，直到 Session 超过设置的时间，若这段时间内获取 SessionId 的值并传给服务器（如CSFR、XSS），依然可以保持为登陆状态，并获取该用户的所有权限。

> session 在服务器端用类似于 Map 结构的形式存储，当标签页或浏览器关闭时并不会通知到后端清除 session
> sessionStorage 用于存储前端数据，当标签页或浏览器关闭时 sessionStorage 中的数据会被清除
