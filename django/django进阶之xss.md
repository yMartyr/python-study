---
title: django进阶之xss
tags: 网络攻击
grammar_cjkRuby: true
---



#  xss攻击
## 1 什么是xss攻击
xss攻击，跨站脚本攻击（Cross-Site Scripting）为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。允许攻击者将恶意代码植入到提供给其它用户使用的页面中。XSS的攻击目标是为了盗取存储在客户端的cookie或者其他网站用于识别客户端身份的敏感信息。一旦获取到合法用户的信息后，攻击者甚至可以假冒合法用户与网站进行交互。

## 2 xss攻击的方式
通常的xss攻击手段：
1. 盗取cookie，获取敏感信息。
2. 植入Flash，通过crossdomain权限设置进一步获取更高权限；或者利用Java等得到类似的操作。

## 3 xss攻击的具体手段
假设一个评论网站没有防xss攻击的，
模拟一个最简单的xss攻击，在他的评论框写入：
```
<script>
for (int i=0;i<=999;i++){
    alert('123');
}
</script>
```
写入以后，浏览器进行渲染。就会一直弹框


## 4 防xss攻击

1. safe
```python
#abc.html中的代码
{{ abc|safe }}  <!-- 不加|safe，是以字符串显示html标签 ，只有保证是安全的，浏览器才会渲染-->

#views中的代码
def abc():
    return "<a href='#'>点我</a>"

def xss(request):
    return render(request,'abc.html',{'abc':abc})

```
2. mark_safe
```python
from django.utils.safestring import mark_safe 
def abc(self): 

       return mark_safe(”<a href=’/accounts/%s/’>%s</a>” %(self.user.id, self.user.username)) 
```
3. 过滤特殊字符
将用户所提供的内容进行过滤，如script等


链接：https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/09.3.md





