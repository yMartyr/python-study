---
title:django进阶之csrf
tags: csrf
grammar_cjkRuby: true
---


# CSRF

## 1 什么是csrf
CSRF（Cross-site request forgery），即跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。**攻击者盗用你的身份，以你的名义发送恶意请求。**
如：django里面叫csrf，tornado里面叫xsrf。


## 2 csrf的危害？
CSRF能够做的事情包括：以你名义发送邮件，发消息，盗取你的账号，于购买商品，虚拟货币转账......
造成的问题：个人隐私泄露以及财产安全。




## 3 csrf原理
### 请求是get
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 攻击者：假设在你要访问的银行网站上，放置了一个图片链接，而这个图片链接是链接到他的账户的。
```html
<a href='http://www.mybank.com/?to=3233434&money=1000'><img...></a>
```

当受害者满足如下两个条件时：
1. 登录了银行网站，并在本地生产了cookie。
2. 在没有退出银行账号的情况下，访问了该有害的图片链接。
（只要两条不同成立，就不会受到csrf的攻击）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 而对于上面这个用get来进行csrf攻击的，可以直接用form来阻止。

### 请求是post
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 那么对于post，又是如何csrf攻击的
和上面差不多
```html
<form method="post" action="csrf1.html">
    <input type="text" value="3233434" name="to" style="display: none">
    <input type="text" value="1000" name="money" style="display: none">
    <a>点我</a>
</form>
```
当点击上面的a标签，就会将钱转到攻击者账户

### 携带随机字符串
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;为了防止上面的post方式下csrf攻击，银行规定，要访问服务器，不仅要post数据，还要服务器给发给浏览器的一个随机字符串。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;用户先发get请求，服务器给页面写入随机字符串。当浏览器访问服务器时，会携带随机字符串来访问。这样就能阻止一部分人的csrf攻击。这种给浏览器生成随机字符串的方式就叫csrf。
```html
<form method="post" action="/app01/csrf1/">
    用户：<input type="text"  name="user" >
    转账数：<input type="text" name="money" >
    <input type="submit" >
</form>
```
setting.py中的MIDDLEWARE：
```python
MIDDLEWARE = [
     'django.middleware.csrf.CsrfViewMiddleware',
]
```
服务端：
```python
def csrf1(request):
    if request.method=="GET":
        return render(request,'csrf1.html')
```
![django_csrf_forbidden][1]

可以看出，**csrf防御是发生在进入路由系统之前**，由于我们没有携带随机字符串，所以被阻止了。

 我们在form表单加入一行代码：
 ```html
 <form method="post" action="/app01/csrf1/">
    {% csrf_token %}    
	{{ csrf_token }}    <!-- 此时是以字符串显示在网页中的 -->
    用户：<input type="text"  name="user" >
    转账数：<input type="text" name="money" >
    <input type="submit" >
</form>
 ```
 此时通过审查元素可以看到：
  `{% csrf_token %}`  在网页中对应的内容是：
 ```html
 <input type="hidden" name="csrfmiddlewaretoken" value="mO6TC0UmyBaENlIpnffDq1N6qQtJaohNOyljD93vuulL3VyxXCCQaLNOKbAST3mq">
 ```
 








一般在表单提交POST才会加上这样一个随即字符串


手机短信验证，验证码
```html
<form>
{% csrf_token %}
</form>
```


ajax 


  [1]: http://orzm1jlhd.bkt.clouddn.com///170628/django_csrf_forbidden.png