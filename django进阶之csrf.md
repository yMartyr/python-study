---
title:django进阶之csrf
tags: csrf
grammar_cjkRuby: true
---


# CSRF

## 1 什么是csrf
CSRF（Cross-site request forgery），即跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。**攻击者盗用你的身份，以你的名义发送恶意请求。**
如：django里面叫csrf，tornado里面叫xsrf。

## 2 csrf可以做什么？
攻击者：假设在你要访问的银行网站上，放置了一个图片链接，而这个图片链接是链接到他的账户的。

当受害者满足如下两个条件时：
1. 登录了银行网站，并在本地生产了cookie。
2. 在没有退出银行账号的情况下，访问了有害的图片链接。


##  3 csrf原理



把一部分人拒之门外
用户先发get请求，服务器给页面写入随机字符串。当浏览器访问服务器时，会携带随即字符串来访问。


一般在表单提交POST才会加上这样一个随即字符串


手机短信验证，验证码
```html
<form>
{% csrf_token %}
</form>
```


ajax 