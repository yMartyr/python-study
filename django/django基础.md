---

title: django基础 
tags: 

---


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; python中的Python的WEB框架有Django、Tornado、Flask 等多种，Django相较与其他WEB框架其优势为：大而全，框架本身集成了ORM、模型绑定、模板引擎、缓存、Session等诸多功能。

```
Django：
- 路由
- 视图
- 模板
- ORM（类-表；对象-行； pymysql连接数据库）（自带ORM，但需要使用第三方如pymysql连接数据库）
Torando:
- 路由
- 视图
- 模板
- 自由：pymysql;SqlAchemy(需要使用第三方)
Flask:
- 路由
- 视图
- 模板(第三方的组件)
- 自由：pymysql;SqlAchemy(需要使用第三方)
```

<br>

# 1 django安装、创建及基本配置

## 1 安装django

> pip3 install django
windows：django会被安装到python/Scripts下，可以将django-admin.exe放入环境变量。

## 2 创建django

- 方式一
```
终端命令：
# 创建Django程序
django-admin startproject project_path
# 进入程序目录
cd project_path
# 启动socket服务端，等待用户发送请求
python manage.py runserver 127.0.0.1:8080 (默认是8000监听端口)
```

- 方式二
使用pycharm创建File->New Project->Django
我们可以看到一个“More Settings”，可以在里面指定创建项目时，是否同时创建一个app01(名字自己指定)。
如果要再添加一个app02
```
在pycharm下面的Terminal输入：
python manage.py starapp app02
```
同时还需要去settings.py中注册app02
```
INSTALLED_APPS = [
'django.contrib.admin',
'django.contrib.auth',
'django.contrib.contenttypes',
'django.contrib.sessions',
'django.contrib.messages',
'django.contrib.staticfiles',
# 'app01.apps.App01Config',
'app01', #注册app
'app02',
]
```
注意：上面这些自带的注册都是有用的。



## 3 目录介绍
![django目录结构](http://orzm1jlhd.bkt.clouddn.com///170623/django_program_catalog.png)
- app01、app02...
将多个功能模块分离。如：“汽车之家”网站，有很多个功能模块：经销商、二手车等，当功能很多时，我们可以把它划分成很多小的模块。
- migrations
记录数据表的迁移和变化的记录数据表的迁移和变化

- admin.py 用来管理django后台相关的配置
```
urlpatterns = [
url(r'^admin/', admin.site.urls),
]
```
- apps.py
相关的配置文件
- models.py
用来写类，根据类创建数据库表
- tests.py
单元测试
- urls.py
用来配置路由。
- views.py
将请求函数放在里面，当业务比较多时,我们可以把它删除，再创建一个views的文件夹

- day70_dj
该文件和项目同名
- settings.py
Django配置文件，如templates、静态文件路径的配置、要使用django自带的orm连接数据库，都在此处配置
- url.py
路由系统：url->函数
- wsgi.py
用于定义Django用socket, Django默认的是wsgiref，但性能比较低。生产环境我没你会用uwsgi
- templates
存放**静态网页**（当然也可以是其他文件的格式后缀，只需要内容是html标签写的，能被浏览器渲染通过就行，甚至随便写个后缀名也是可以的）
- db.sqlite3
django自带的数据库时sqlite3。
- manage.py
用于管理Django站点。启动django需要用到它。

<br>

## 4 setting.py详解
### 1 配置静态文件
- 配置静态文件路径：
```python
STATIC_URL = '/static/'
STATICFILES_DIRS = ( #是一个元组
os.path.join(BASE_DIR,'static_'),
)
```
要想页面能被渲染，必须要这么写，此时这样写：
```
<link rel="stylesheet" href="/static/css/classes.css">
```
到django寻找css样式时，找到/static/，会去setting.py寻找到`STATIC_URL = '/static/'`，再去寻找`STATICFILES_DIRS`，此时会查找`os.path.join(BASE_DIR,'static_')`路径，就能找到真实路径中的`/static_/css/classes.css`。（也可以将上面的`static_`修改为`static`）
不要主观的认为在html静态文件中直接导入js或css，以如下的方式会生效:
```
<link rel="stylesheet" href="/static_/css/classes.css"> <!-- 这样写的样式是无效的，django是不能渲染到该样式的 -->
```
- 配置好静态文件，就需要在根目录创建`static_`文件夹，用来存放css，js和第三方的js、css。如果js和css过多，我们可以创建一个js文件夹、一个css文件夹和一个plugins文件夹。

### 2 配置数据库
我们这里使用mysql。
找到setting.py中sqlite3的配置信息：
```python
DATABASES = {
'default': {
'ENGINE': 'django.db.backends.sqlite3',
'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
}
}
```
将以上信息修改（注释）为：
```python
DATABASES={
'default': {
'ENGINE': 'django.db.backends.mysql',
'NAME':'dbname',
'USER': 'xxx',
'PASSWORD': 'xxx',
'HOST': '', #默认不写，是localhost
'PORT': , # 端口号，mysql默认是3306
}
}
```
除了修改setting.py中的DATABASES，还需要设置与project同名的配置的 \_\_init\_\_.py文件中：
```python
import pymysql
pymysql.install_as_MySQLdb()
```

### 3 配置模板路径
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 如果使用pycharm创建项目，会自动帮我们完成此项的配置。但如果我们使用命令行创建项目，则需要我们手动完成配置的。
```
TEMPLATE = (
os.path.join(BASE_DIR,'templates'),
)
```
## 5 常用manage.py命令汇总
```
python manage.py runserver 0.0.0.0 #用xxx.xxx.xxx.xxx：xxxx的ip和端口启动项目
python manage.py startapp appname #创建新的app
python manage.py syncdb
python manage.py makemigrations [appname] #实现表的变化，生成变化后的表实现。根据app中models.py文件中的代码,在同一个app下的migrations文件夹中生成关于ORM操作的记录和日志。不加appname将所有存在于setting.py中INSTALLED_APPS下的app全部实现一遍
python manage.py migrate [appname] #根据app中migrations文件夹下的记录和日志，对数据库里的数据表和数据行进行增删改查的操作。不加appname是对所有的app执行操作
python manage.py createsuperuser
python manage.py inspectdb   #以sql语句的方式导出数据库中的表
python manage.py shell    #打开django自带的shell
```
PS:查看django的帮助
```
python manage.py [--help|- h]
```
<br>

# 2 路由系统
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; urls.py文件，当浏览器发送url请求时，提交的get和post请求都需要通过该路由系统再去调用对应views中的函数。
路由匹配的特点：由上到下匹配，直到找到符合规则的字符串为止，就不再向下匹配。
分类：
1. 静态路由（一对一）
2. 动态路由（正则表达式）
3. 路由分发

## 1 一对一路由(静态路由)
要获取get方式传过来的值，必须用request.GET.get(参数名)
```python
#浏览器中的get请求：
<a href='/index/？nid=123'> #http://127.0.0.1:8000/index/?nid=123

# urls中的路由信息
url('^index$',views.index), #以index开始，并以index结束

# views中的index函数定义
def index(request):
nid=request.GET.get('nid') #获取get请求中的nid的值
```

## 2 基于正则的路由(动态路由)
只需要在路由调用的对应方法中在函数定义时，加入额外的参数，通过这些参数来获取。获取的方式有两种：
### 1 根据位置参数来取值

```python
#浏览器中的get请求：
<a href="/edit/{{ id }}/"> #http://127.0.0.1:8000/edit/123

#urls中的路由信息
url('^edit/(\w*)'，views.edit)

#views中的edit函数定义
def edit(request,a1): #如果有多个位置参数，像这样依次a1，a2...去取
print(a1) #不需要再通过request.GET来获取,直接就可以拿到a1的值。（a1可以随便取名字）
```

### 2 根据关键字参数来取值
在路由命名分组里的名称必须和路由调用的函数里的参数名是一样的。（不一样的就是位置参数）
```python
#浏览器中的get请求：
http://127.0.0.1:8000/edit/123/456/

#urls中的路由信息
url('^edit/(?P<a1>\w*)/(?P<a2>\w*)/'，views.edit)

#views中的edit函数定义
def edit(request,a2,a1): #如果有多个位置参数，像这样依次a1，a2...去取
print(a1) #不需要再通过request.GET来获取,直接就可以拿到a1的值。（a1可以随便取名字）
print(a2)

result:
123
456
```
PS: **这里并不像python那样，可以位置参数和关键字参数混搭这使用，这里会报错**。如下：
```
#html请求：
http://127.0.0.1:8000/index/123/abc

#urls路由：
url(r'^index/(\w*)/(?P<a2>\w*)',views.index),

#views中的index函数：
def index(request,a1,a2):
#def index(request,*args,**kwargs): #依然会报错
print(a1,a2)
return render(request,'index.html')
```
错误信息：
```
TypeError at /index/123/abc
index() missing 1 required positional argument: 'a1'
```
PS:**参数要么是按位置参数，要么是关键字参数。必须统一**

### 3 添加额外的参数
该参数并不需要在导航栏中输入，当浏览器向服务端发出请求时，请求数据到达路由时，此时才会携带上额外添加的参数。所以在请求头（GET方式访问的）中是找不到该参数的。
同时在views中定义函数时，额外参数和关键字参数获取和使用是一样的。
注意：**当有路由映射名称时，路由映射名必须放在额外参数的后面。**
```
#html请求：
http://127.0.0.1:8000/login/123abc

#urls路由：
url(r'^login/(?P<name>\w*)', views.login,{'id':333}),

#views中的index函数：
def index(request,name,id):
print(name) #123abc
print(id) #333
return HttpReponse(11111)
```


### 4 为路由映射名称
相当于给路由系统中的url取一个别名，而**这个别名实际上只是映射了正则匹配中前面那一部分固定的url，后面正则匹配的部分是不被映射的**。
使用时，需要注意：
1. 在url调用的函数里面，通过reverse('url-mapping-name',args...)来使用路由映射关系，且args的个数必须和路由url中的正则匹配这一部分的参数个数一致。不然会报错：
```python
NoReverseMatch at /login2/123
Reverse for 'n2' with no arguments not found. 1 pattern(s) tried: ['login2/(?P<a2>\w+)']
```
2. 可以在html模板中使用{% url 'url-mapping-name' args... %}，参数args的个数也必须和路由url中正则匹配这一部分的参数个数一致。否则也会报和上面一样的错误。
3.无论是在html模板中，还是在views中的函数都需要添加“''”引号把url-mapping-name引起来。

PS：**给路由映射名称，是django特有的，其他框架没有**

```python
#浏览器中的代码：
{% url 'n1' %} #最终显示效果 /login/

#urls路由：
url(r'^login/',views.login,name='n1'),

#views中的login函数：
from django.urls import reverse
def login(request):
v=reverse('n1') #根据名字‘n1’通过reverse方法来反向生成url。
print(v) #/login/
return render(request,'login.html')
```
作用：当给用户设置权限时，限制每个用户只能访问某些网页。此时用映射名称的方法，看起来更简洁


#### 1 反向生成url
路由映射的一种特殊用法
通过路由映射名称来获取url的方式，叫做反向生成url。
##### 1 urls中的路由为位置参数
```
#url地址
http://127.0.0.1:8000/login2/123

#浏览器中的代码：
{% url 'n2' 2017 %} #/login2/2017
{% url 'n2' 'a1' %} #/login2/a1
<!-- {% url 'n2' %} --> #此时会报错
#错误信息：NoReverseMatch at /login2/123
# Reverse for 'n2' with no arguments not found. 1 pattern(s) tried: ['login3/(?P<a3>\w+)']

#urls路由：
url (r'^login2/(\w+)', views.login2, name='n2'),

#views中的login2函数：
from django.urls import reverse
def login2(request,a1):
v=reverse('n2',args=(0000)) #根据名字‘n2’通过reverse方法来反向生成url。
print(v) #/login/0000
print(a1) #123
return render(request,'login2.html')
```
通过urls.py中取的名字，来反向生成url。如果urls.py中的url有参数，我们可以通过reverse方法来让它反向生成我们所想要的ur。此时生成的url和我们在导航栏中输入的url没有关系。相当于我们在服务端修改了url。
注意：**如果我们在reverse中设置了参数（位置参数和关键字参数），此时在html模板中的{% url '路由映射名' 'xxx' %}，且xxx不能为空。**

##### 2 urls路由中的参数是位置参数
```
#url地址
http://127.0.0.1:8000/login2/123

#浏览器中的代码：
{% url 'n2' 2017 %} #/login2/2017


#urls路由：
url (r'^login2/(？P<a2>\w+)', views.login2，name='n2'),

#views中的login2函数：
from django.urls import reverse
def login2(request,a2):
#print(reverse('n2')) #此时会报错
#错误信息：NoReverseMatch at /login2/123
#Reverse for 'n2' with no arguments not found. 1 pattern(s) tried: ['login2/(?P<a2>\w+)']
v=reverse('n2',args=(0000)) #根据名字‘n2’通过reverse方法来反向生成url。
print(v) #/login/0000
print(a1) #123
return render(request,'login2.html')
```
PS:'n2'是路由映射名称，不要认为进行`n2=reverse('n2',args=(0000))`的操作，在网页上显示的会变成`/login/0000/2017`
****

#### 2 路由映射名称中使用的方法
在模板中使用：
```
{% url 'url-mapping-name' args... %} #后面args的参数个数必须和路由url中正则匹配的参数的个数一致
```
在views定义函数中：
```
from django.urls import reverse
reverse('url-mapping-name',args...) #后面args的参数个数必须和路由url中正则匹配的参数的个数一致
```


### 5 路由映射名称的应用

1. 在模板中如form：
```
<form method='post' action='{% url 'n2' %'>
```

2. 多个用户都访问不同的url
```
{% for i in user_list %}
<li>{{ i }}|<a href="/edit/{% url 'n2' i %}.html/">编辑</a></li>
{% endfor %}
```

### 6 resolver_match

> request.resolver_match
获取**匹配成功**时，**返回的信息**。

```python
#url地址
http://127.0.0.1:8000/login3/123

#urls路由：
url (r'^login3/(?P<a3>\w+)', views.login3, name='n3'),

#views中的login2函数：
from django.urls import reverse
def login2(request,a3):
print(rquest.resolver_match)
#v=reverse('n2',kwargs={'a3':'abc'}) #根据名字‘n2’通过reverse方法来反向生成url。
#print(v) #/login/abc
return render(request,'login2.html')

```
结果：
```
ResolverMatch(func=app01.views.login3, args=(), kwargs={'a3': '123'}, url_name=n3, app_names=[], namespaces=[])
```

### 7 在model中获取URL
自定义方法get_absolute_url()方法
```
class NewType(models.Model):
caption = models.CharField(max_length=16)

def get_absolute_url(self):
"""
为每个对象生成一个URL
应用：在对象列表中生成查看详细的URL，使用此方法即可！！！
:return:
"""
# return '/%s/%s' % (self._meta.db_table, self.id)
# 或
from django.urls import reverse
return reverse('NewType.Detail', kwargs={'nid': self.id})
```

<br>

## 3 路由分发(二级路由)
当进行协同开发时，让每一个app都有自己的url，不和别人的重复。此时需要用到二级路由。
a. 在与项目同名的文件夹下的urls.py中：

```python
from django.conf.urls import include #在urls路由配置文件里，所以是django.conf.urls，这样来理解便于记忆

urlpattern=[
url(r'^app01/',inclue('app01.urls'))
]
```

b. 再在app01下的urls.py中添加：

```python
from django.conf.urls import url
from app01 import views

urlpattern=[
url(r'^login/',views.login),
]
```

c. 访问网址：

```
http://localhost:8000/app01/login/ #就能访问login
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 当我们输入上面的网站时，会优先在与项目同名的文件夹中的urls.py匹配，当它匹配到
`^app01/`,此时会进入到`app01.urls`中去匹配。当它匹配到`'^login/'`时，会执行
views.login函数。

### 路由分发,render和redirect的使用
```
#一级路由，即与项目同名的目录下的settings文件中
url('^app01/',include('app01.urls'))

#二级路由，app01下的urls文件
url('^login.html$',views.login),
url('^classes.html$',views.classes),
url('^add_class.html$',views.add_class),

#app01下的views：
def login(request):
    ...
    return render(request,'classes.html')

def classes(request):
    ...
    return render(request,'add_class.html',{...})

def add_class(request):
    ...
    return redirect('/app01/classes.html')    #此时必须加上/app01/
    
```
注意render和redirect的区别：
render：渲染时，只是将替换了页面内容，但不会修改url，所以适合定向到对应的页面中。一般会生成302状态码。
redirect：是请求一个全新的页面。

#render和redirect http://www.cnblogs.com/liuvzzvuil/p/6596548.html

### 用户输入错误时跳转到某个网页
当用户输入错误时，我们可以让他访问某个网页
```
urlpattern=[
url(r'^',views.login), 也可以写成url(r'^.*',views.login)
]
```


### 网页SEO权重和伪静态：
```
<a href='/index/?nid={{ i }}'>
```
//这种写法,以get请求，百度会认为你的 127.0.0.1/?nid=1 ,nid后面的内容会经常变，SEO权重会很低。不利于百度爬虫收录网页
推荐使用：**127.0.0.1/ffff **
**比上面这种更美观，同时让百度收录优先级更高。**
而像我们如果写成：
```
url(r'^edit/(\w+).html',views.edit), //伪静态

```
- 伪静态
就是以.html结尾的，看上去想静态html，实际上只是把url伪装成静态html。像这种SEO权重会更高。更利于百度等搜索引擎的收录。
(静态网站比动态网站访问快)
- 终止符
推荐写法：
> url(r'^edit/(\w+)\.html$'),



# 3 ORM操作

## 1 什么是ORM
ORM：Object Relationship Model，即对象关系映射。
ORM的功能：通过对类和对象的操作来实现对数据表和数据行的操作。（不能操作数据库）

ORM分为两种：
1. DB First 先有数据库，再通过数据表结构来生成类。根据类和对像的操作来操作数据表和数据行
2. Code First 先有代码，执行代码来生成数据表结构。

主流的ORM都是code first。django的ORM也还是Code First。

django中http请求：
> url请求-> 视图（模板+数据）

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 其中的数据部分，django用自带的ORM框架来完成的。
PS：django的ORM虽然能操作数据表和数据行，但并不能连接数据库。django内置了mysqlDB模块用来连接内置的sqlite3。但我们这里用的是mysql数据库，连接数据库用的是pymysql。

orm中django中的作用：
1. 操作数据表
创建数据表、修改数据表
2. 操作数据行


## 2 django使用ORM框架的步骤

### 1 创建数据库
django通过ORM能创建表能修改表，但不能创建数据库。这里需要先手动创建数据库

### 2 配置数据库
使用mysql数据库，需要先修改与项目同名的文件夹下的setting.py中DATABASES的内容：

```python
DATABASES = {
'default': {
'ENGINE': 'django.db.backends.sqlite3',
'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
}
}
```
将以上信息修改（注释）为：
```python
DATABASES={
'default': {
'ENGINE': 'django.db.backends.mysql',
'NAME':'dbname',
'USER': 'xxx',
'PASSWORD': 'xxx',
'HOST': '', #默认不写，是localhost
'PORT': , # 端口号，mysql默认是3306
}
}
```
PS：全局变量要全大写
博主写的时候把‘USER’写成“User”，报出如下错误：
```
"Access denied for user 'Dimension'@'localhost' (using password: YES)
```
其中'Dimension'是计算机用户名。


### 3 利用pymysql第三方工具连接数据库
项目启动时，会优先加载配置文件。而对pymysql的连接的配置是写在与项目同名的文件夹下的\_\_init\_\_.py中：
```python
import pymysql
pymysql.install_as_MySQLdb()
```

### 4 编写app下models.py中的类
此过程下面具体介绍

### 5 注册app
修改与项目同名的文件夹下的setting.py的INSTALLED_APPS中加入：
```python
INSTALLED_APPS = [
'django.contrib.admin',
'django.contrib.auth',
'django.contrib.contenttypes',
'django.contrib.sessions',
'django.contrib.messages',
'django.contrib.staticfiles',
'app01',
]
```
上面自带的app，每一个都有自己的models.py
### 6 生成数据库表
```cmd
python manage.py makemigrations [appname] #实现表的变化，生成变化后的表实现。根据app中models.py文件中的代码,在同一个app下的migrations文件夹中生成关于ORM操作的记录和日志。不加appname将所有存在于setting.py中INSTALLED_APPS下的app全部实现一遍
python manage.py migrate [appname] #根据app中migrations文件夹下的记录和日志，对数据库里的数据表和数据行进行增删改查的操作。不加appname是对所有的app执行操作
```
所以当我们在命令行执行上面这两条命令时，django自带app也会生成相应的数据库表。
其中生成的表命名是：appname\_classname。如：app01\_class。

### 7 使用默认的sqlite数据库
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 要使用默认的sqlite数据库，就不需要修改setting.py里的DATABASES属性和\_\_init\_\_。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 如果是第一次使用sqlite，点击pycharm右侧的Database，可以看到一个扳手图标“data source properties”，安装sqlite的插件。再将sqlite3数据库从django项目拖入Database即可。

## 3 Django ORM 操作数据表
编写app下的model.py文件：
### 1 创建数据表
```python
from django.db import models

class Class(models.Model): #必须继承models.Model 类名会映射成表名
cid=models.BigAutoField(primary_key=True) #设置主键自增字段，long类型
#如果是AutoField，就是int类型自增
#如果不写此行，django默认会帮我们创建一个id字段：主键自增int类型
cname=models.CharField(max_length=16) #设置字段cname，最大长度为16

```
### 2 生成数据表
在当前项目名下执行cmd命令：
```cmd
python manage.py makemigrations
python manage.py migrate
```
PS：这里的manage.py是项目下的。
执行`python manage.py makemigrations`在app01\migrations\下生成的0001\_initial.py中的内容：
![0001\_initial.py](http://orzm1jlhd.bkt.clouddn.com///170624/C6ahkCGIJe.png)

执行`python manage.py migrate`会生成如下表：
此时是将所有注册过的app都生成数据表
```cmd
+----------------------------+
| Tables_in_db_manage |
+----------------------------+
| app01_class |
| auth_group |
| auth_group_permissions |
| auth_permission |
| auth_user |
| auth_user_groups |
| auth_user_user_permissions |
| django_admin_log |
| django_content_type |
| django_migrations |
| django_session |
+----------------------------+
11 rows in set (0.00 sec)
```
其中生成的表命名是：appname\_classname。如：app01\_class。
```cmd
mysql> show create table app01_class;
+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table | Create Table |
+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| app01_class | CREATE TABLE `app01_class` (
`cid` bigint(20) NOT NULL AUTO_INCREMENT,
`cname` varchar(16) NOT NULL,
PRIMARY KEY (`cid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)
```


### 3 修改、添加和删除数据表
只需要直接修改、添加和删除类中的属性和对应的属性值即可。
```python
class Class(models.Model): #必须继承models.Model 类名会映射成表名
class_id=models.BigAutoField(primary_key=True)
#cname=models.CharField(max_length=32) #设置字段cname，最大长度为16
#age=models.SmallIntegerField()
age = models.SmallIntegerField (null=True) #可以为空
```
ps：当我们添加或修改已经创建好的数据表时，如果我们在括号里面不写任何内容，如`age=models.SmallIntegerField()`，此时会报如下错误：
```
You are trying to add a non-nullable field 'age' to class without a default; we can't do that (the database needs something to populate existing rows).
Please select a fix:
1) Provide a one-off default now (will be set on all existing rows with a null value for this column)
2) Quit, and let me add a default in models.py
```
意思是数据库里面可能有内容，此时添加或修改后的列不能为空，解决方法：
1. 括号里面写defalut=1，设置默认值
2. 写null=True，可以为空


- 相比上面创建的类，我们做了如下修改：
1. 修改了cid为class_id
2. 删除了cname字段
3. 添加了age字段

- 执行makemigrations命令
此时的migrations会额外生成一个.py文件，这里生成的是`0002_auto_20170624_1927.py`，而原来的0001\_initial.py文件里的内容并没有发送变化。让我们看看新生成的文件里的内容：
![mark](http://orzm1jlhd.bkt.clouddn.com///170624/0002_auto_20170624_1927.py.png)
同时pycharm下的终端：

```
D:\devtools\workspace\python\dj_manage>python manage.py makemigrations app01
Did you rename class.cid to class.class_id (a BigAutoField)? [y/N] y
Migrations for 'app01':
app01\migrations\0002_auto_20170624_1927.py
- Rename field cid on class to class_id
- Remove field cname from class
- Add field age to class

```
- 执行migrate命令
```
D:\devtools\workspace\python\dj_manage>python manage.py migrate
```
cmd命令行，我们可以看到数据表结构如下：
```
mysql> show create table app01_class;
+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table | Create Table |
+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| app01_class | CREATE TABLE `app01_class` (
`class_id` bigint(20) NOT NULL AUTO_INCREMENT,
`age` smallint(6) DEFAULT NULL,
PRIMARY KEY (`class_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```


### 4 字段类型

```
models.AutoField　　自增列 = int(11)
　　如果没有的话，默认会生成一个名称为 id 的列，如果要显示的自定义一个自增列，必须将给列设置为主键 primary_key=True。
2、models.CharField　　字符串字段
　　必须 max_length 参数
3、models.BooleanField　　布尔类型=tinyint(1)
　　不能为空，Blank=True
4、models.ComaSeparatedIntegerField　　用逗号分割的数字=varchar
　　继承CharField，所以必须 max_lenght 参数
5、models.DateField　　日期类型 date
　　对于参数，auto_now = True 则每次更新都会更新这个时间；auto_now_add 则只是第一次创建添加，之后的更新不再改变。
6、models.DateTimeField　　日期类型 datetime
　　同DateField的参数
7、models.Decimal　　十进制小数类型 = decimal
　　必须指定整数位max_digits和小数位decimal_places
8、models.EmailField　　字符串类型（正则表达式邮箱） =varchar
　　对字符串进行正则表达式
9、models.FloatField　　浮点类型 = double
10、models.IntegerField　　整形
11、models.BigIntegerField　　长整形
　　integer_field_ranges = {
　　　　'SmallIntegerField': (-32768, 32767),
　　　　'IntegerField': (-2147483648, 2147483647),
　　　　'BigIntegerField': (-9223372036854775808, 9223372036854775807),
　　　　'PositiveSmallIntegerField': (0, 32767),
　　　　'PositiveIntegerField': (0, 2147483647),
　　}
12、models.IPAddressField　　字符串类型（ip4正则表达式）(已弃用，用13、)
13、models.GenericIPAddressField　　字符串类型（ip4和ip6是可选的）
　　参数protocol可以是：both、ipv4、ipv6
　　验证时，会根据设置报错
14、models.NullBooleanField　　允许为空的布尔类型
15、models.PositiveIntegerFiel　　正Integer
16、models.PositiveSmallIntegerField　　正smallInteger
17、models.SlugField　　减号、下划线、字母、数字
18、models.SmallIntegerField　　数字
　　数据库中的字段有：tinyint、smallint、int、bigint
19、models.TextField　　字符串=longtext
20、models.TimeField　　时间 HH:MM[:ss[.uuuuuu]]
21、models.URLField　　字符串，地址正则表达式
22、models.BinaryField　　二进制
23、models.ImageField 图片
24、models.FilePathField 文件
```

大致可分为： 字符串、数字、时间、二进制、自增（primary_key=True）几类。大致分为 字符串、数字、时间、二进制、自增（primary_key=True）几类。

### 5 字段参数
```
null # 是否可以为空
default # 默认值
primary_key # 主键
db_column # 列名
db_index # 索引(db_index=True)
unique # 唯一索引(unique=True)
unique_for_date # 只对日期索引
unique_for_month # 只对月份索引
unique_for_year # 只对年做索引
auto_now # 创建时，自动生成时间
auto_now_add # 更新时，自动更新为当前时间
```

### 6 外键操作
```python
class UserGroup(models.Model):
gid=models.BigAutoField(primary_key=True)
title=models.CharField(max_length=32)

class UserInfo(models.Model):
uid=models.BigAutoField(primary_key=True)
username = models.CharField(max_length=32)
password = models.CharField(max_length=60)
user_group = models.ForeignKey("UserGroup",to_field='gid',null=True)
```
此时创建的最后一个字段并不叫`user_group`,而是叫`user_group_id`。
其实这里我们**不加`to_field`参数，也能帮我们找到关联表中的主键。且该字段名也是一样的命名。**

关于django中orm数据类型http://blog.csdn.net/fgf00/article/details/53678205


## 4 Django ORM操作数据行
### 1 增

```python
def orm(request):
    #方式一：
    models.UserGroup.objects.create(title='管理部')
    models.UserGroup.objects.create (title='公关部')
    abc=models.UserInfo.objects.create(username='egg',password='123',user_group_id=1)
    print(abc)                 #UserInfo Object
    print(type(abc))               #<class 'app01.models.UserInfo'>
    
    #方式二：
    obj=models.UserInfo(username='boo',password='123',user_group_id='2')  #此时在内存里面
    obj.save()   #用save将内存里的数据保存到数据库
    
    #方式三：
    dic={'title':'流水部'}
    models.UserGroup.objects.create(**dic)
    
    return HttpResponse(123)       
```
每执行一次上述代码，就会在数据库中写入一次，不要反复执行上述代码。


### 2 查
#### 1 全表查询
> models.UserInfo.objects.all()  #返回一个QuerySet列表，数据表中的每一行数据在QuerySet列表中都是一个字典。all()是返回UserInfo表中的所有数据。
```python
user_list=models.UserInfo.objects.all()
    print(user_list)           #<QuerySet [<UserInfo: UserInfo object>, <UserInfo: UserInfo object>]>
    print(isinstance(user_list,Iterable))    #True
    return HttpResponse (123)
```
可以使用for来遍历QuerySet：
```python
       for row in user_list:
                print(row.username,row.password,row.user_group_id,row.user_group.title,)
```
 user_list获取的是QuerySet，内部元素是UserInfo对象，每一个对象代指一行数据，对象中如果包含ForeignKey，则代指与其关联表的一行数据。
此时要访问外键关联的另一张表直接用：row.user_group.title


#### 2 返回查询到的第一个
> first()    #返回查询到的第一个对象。如果查询结果为空，则返回None

```python

user_list=models.UserInfo.objects.all().first()   #UserInfo object
# 上面这种all()可以不写，结果是一样的
```


#### 2 条件查询
orm中可以用filter(condition)里表示where条件查询
> models.UserInfo.objects.filter(uid=3)
> models.UserInfo.objects.filter(uid\_\_gt=1)  等价于filter(id>1)

```python
def orm(request):
      models.UserInfo.objects.filter(uid=3)   #<QuerySet [<UserInfo: UserInfo object>]>
      result = models.UserInfo.objects.filter(username="fgf",password="666")  #表示username="fgf"并且password="666"
```

### 3 修改
修改需要先查找到相应的queryset对像，再进行修改
```python
user_group=models.UserGroup.objects.filter(gid=1).update(title='管理部111')
```
`
### 4 删除
删除和查找一样，需要先查找到要删除的行，再进行删除操作
```
models.UserInfo.objects.filter(id=2).delete()
```


PS:我们除了可以使用pycharm写函数。也可以在cmd下执行
```
D:\devtools\workspace\python\day70_dj>python manage.py shell
Python 3.6.0 (v3.6.0:41df79263a11, Dec 23 2016, 08:06:12) [MSC v.1900 64 bit (AMD64)]
Type 'copyright', 'credits' or 'license' for more information
IPython 6.1.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from app01.models import UserInfo,UserGroup

In [2]: UserInfo.objects.all()
Out[2]: <QuerySet [<UserInfo: UserInfo object>, <UserInfo: UserInfo object>, <UserInfo: UserInfo object>]>
```


## 5 orm其他操作
除了增删改查以外的补充
为了方便查看我们查看打印后的结果，我们可以做如下处理：
```python
#在models中class里面写上重写__str__()方法
class UserInfo(models.Model):
    uid=models.AutoField(primary_key=True)
    username=models.CharField(max_length=32)
    age=models.SmallIntegerField(null=True)
    ut=models.ForeignKey('UserType')

    def __str__(self):
        return '%s--%s'%(self.uid,self.username)

#在views中：
res = models.UserInfo.objects.all ()
print(res)

#打印结果：
<QuerySet [<UserInfo: 1--zhige>, <UserInfo: 2--haoge>, <UserInfo: 3--haoge2>, <UserInfo: 4--aaa>, <UserInfo: 5--wan>]>
```

```python
models.UserInfo.objects.all()
models.UserInfo.objects.filter(uid=2,username=2)
models.UserInfo.objects.all().first()
models.UserInfo.objects.all().count()    #统计数量
models.UserInfo.objects.all().update()
models.UserInfo.objects.all().dalete()
models.UserInfo.objects.all()[1:20]  #从queryset列表对像取值
```

### 一般orm操作
```python
models.UserInfo.objects.filter(uid__gt=2)    #大于2
models.UserInfo.objects.filter(uid__gte=2)   #大于等于2
models.UserInfo.objects.filter(uid__lt=2)   #小于2
models.UserInfo.objects.filter(uid__in=[1,2,3])   #sql中的in
models.UserInfo.objects.filter(uid__range=[2,5])    #sql中between 2 and 5
models.UserInfo.objects.filter(username__startswith='h')    #以什么开始
models.UserInfo.objects.filter(username__istartswith='h')    #忽略大小写以什么开始
models.UserInfo.objects.filter(username__endswith='h')    #以什么结束
models.UserInfo.objects.filter(username__iendswith='h')    #忽略大小写以什么结束
models.UserInfo.objects.filter(username__contains='a')    #sql中的like
models.UserInfo.objects.exclude(uid=1)   #exclude是“不包含的意思”。相当于“非”。
models.UserInfo.objects.filter (age__isnull=True)   #选出age为空的
models.UserInfo.objects. none()     # 空QuerySet对象

models.UserInfo.objects.count()    #获取个数
models.UserInfo.objects.get(uid=1)   # 找不到和找到多个都会抛异常

models.UserInfo.objects.first()   #获取queryset中的第一个对象
models.UserInfo.objects.last()    #获取queryset中最后一个

def exists(self):
    #是否存在

def get_or_create(self, defaults=None, **kwargs):
    # 如果存在，则获取，否则，创建
    # defaults 指定创建时，其他字段的值
    obj, created = models.UserInfo.objects.get_or_create(username='root1', defaults={'email': '1111111','u_id': 2, 't_id': 2})

def update_or_create(self, defaults=None, **kwargs):
    # 如果存在，则更新，否则，创建
    # defaults 指定创建时或更新时的其他字段
    obj, created = models.UserInfo.objects.update_or_create(username='root1', defaults={'email': '1111111','u_id': 2, 't_id': 1})
	
def in_bulk(self, id_list=None):
   # 根据主键ID进行查找
   id_list = [11,21,31]
   models.DDD.objects.in_bulk(id_list)
  
def bulk_create(self, objs, batch_size=None):
   # 批量插入
   # batch_size表示一次插入的个数
   objs = [
       models.DDD(name='r11'),
       models.DDD(name='r22')
   ]
   models.DDD.objects.bulk_create(objs, 10)
```

### 排序order_by
```python
models.UserInfo.objects.all().order_by('age')   #以age升序排列
models.UserInfo.objects.all().order_by('-age')   #以age降序排列
models.UserInfo.objects.all().order_by('-age','name')  #以age降序并以name升序排列
```

### 分组annotate
实现group by。但是注意annotate的特殊用法，它是以前面values()里面映射的字段来作为分组的。annotate后面的参数也会作为一个映射
```python
from django.db.models import Count,Sum,Avg,Max,Min

# res = models.UserInfo.objects.values ('uid','ut_id',).annotate (xx=Count ('ut_id'))
    # SELECT "app01_userinfo"."uid", "app01_userinfo"."ut_id", COUNT("app01_userinfo"."ut_id") AS "xx" FROM "app01_userinfo" GROUP BY "app01_userinfo"."uid", "app01_userinfo"."ut_id"

    res = models.UserInfo.objects.values ('ut_id',).annotate (xx=Count ('ut_id'))
    #SELECT "app01_userinfo"."ut_id", COUNT("app01_userinfo"."ut_id") AS "xx" FROM "app01_userinfo" GROUP BY "app01_userinfo"."ut_id"
	    for user in res:
             print(user['ut_id'],user['xx'])

#打印结果：
1 2
2 2
3 1
```
聚合查询，接条件having
```python
res = models.UserInfo.objects.values ('ut_id').annotate (xx=Count ('ut_id')).filter (xx__gt=1)
#SELECT 
#"app01_userinfo"."ut_id",
#COUNT("app01_userinfo"."ut_id") AS "xx" 
#FROM "app01_userinfo" GROUP BY "app01_userinfo"."ut_id" 
#HAVING COUNT("app01_userinfo"."ut_id") > 1
```

**在集合查询后的对像调用filter()，此时的filter()相当于having的作用。**

```python
#使用distinct去重
res = models.UserInfo.objects.values ('ut_id').annotate (xx=Count ('ut_id')).filter (xx__gt=1)

# SELECT
# "app01_userinfo".
# "ut_id", COUNT (DISTINCT "app01_userinfo"."ut_id") AS "xx"
# FROM "app01_userinfo"
# GROUP BY "app01_userinfo". "ut_id"
# HAVING COUNT (DISTINCT "app01_userinfo". "ut_id") > 1
```

**分组是以values里的元素进行分组的。**


### 限制(分页)limit、offset
```python
models.UserInfo.objects.add()[10:20]
```

### date
```python
# date
        #
        # Entry.objects.filter(pub_date__date=datetime.date(2005, 1, 1))
        # Entry.objects.filter(pub_date__date__gt=datetime.date(2005, 1, 1))

        # year
        #
        # Entry.objects.filter(pub_date__year=2005)
        # Entry.objects.filter(pub_date__year__gte=2005)

        # month
        #
        # Entry.objects.filter(pub_date__month=12)
        # Entry.objects.filter(pub_date__month__gte=6)

        # day
        #
        # Entry.objects.filter(pub_date__day=3)
        # Entry.objects.filter(pub_date__day__gte=3)

        # week_day
        #
        # Entry.objects.filter(pub_date__week_day=2)
        # Entry.objects.filter(pub_date__week_day__gte=2)

        # hour
        #
        # Event.objects.filter(timestamp__hour=23)
        # Event.objects.filter(time__hour=5)
        # Event.objects.filter(timestamp__hour__gte=12)

        # minute
        #
        # Event.objects.filter(timestamp__minute=29)
        # Event.objects.filter(time__minute=46)
        # Event.objects.filter(timestamp__minute__gte=29)

        # second
        #
        # Event.objects.filter(timestamp__second=31)
        # Event.objects.filter(time__second=2)
        # Event.objects.filter(timestamp__second__gte=31)
```

### F
获取数据库里原来的数据或上一次的数据
```python
from django.db.models import F
models.UserInfo.objects.all().update(age=F('age')+1)   #将数据库总age字段值全加1
```

### Q
用来写条件语句的
```
from django.db.models import Q
方式一：
models.UserInfo.objects.filter(Q(id__gt=2)|Q(name="aa"))
models.UserInfo.objects.filter(Q(id=8) & Q(id=2))   #只是举个例子，但是结果是取不到值的

方式二：

con=Q()
q1=Q()
q1.connector("AND")
q1.children.append('uid',1)
q1.children.append('age__gt',30)

q2=Q()
q2.connector('OR')
q2.children.append('c1',10)
q2.children.append('c2',9)

con.add(q1,"AND")
con.add(q2,'AND')

models.UserInfo.objects.filter(con)
```


### filter
django中表条件判断使用filter。在queryset对像后面跟filter()，此时相当于where。在annaotate聚合函数后面，相当于having。

### extra
> extra(self, select=None, where=None, params=None, tables=None, order_by=None, select_params=None)

extra结果介绍：
```python
# a. 映射
	# select 
	# select_params=None
	# select 此处 from 表
				
# b. 条件
	# where=None
	# params=None,
	# select * from 表 where 此处

# c. 表
	# tables
	# select * from 表,此处

# c. 排序
	# order_by=None
	# select * from 表 order by 此处
```

eg：
```python
res=models.UserInfo.objects.extra(
     select={'new_id':'select id from app01_usertype where id>%s'},
	 select_params=[2,]
)

#SELECT 
#(select id from app01_usertype where id>2) AS "new_id", #"app01_userinfo"."uid", 
#"app01_userinfo"."username", 
#"app01_userinfo"."age", 
#"app01_userinfo"."ut_id" 
#FROM "app01_userinfo"


res=models.UserInfo.objects.filter(uid__gte=2).extra(select={'new_id':'select id from app01_usertype where id>%s'},select_params=[1,],
                                                         where=["age>%s",], 
                                                         params=[20,],
                                                         order_by=['-age',],
                                                         tables=['app01_usertype',]  
                                                         )
#SELECT (select id from app01_usertype where id>1) AS "new_id", 
# "app01_userinfo"."uid", 
# "app01_userinfo"."username", 
# "app01_userinfo"."age", 
# "app01_userinfo"."ut_id" FROM "app01_userinfo" , 
# "app01_usertype" 
# WHERE ("app01_userinfo"."uid" >= 2 AND (age>20)) ORDER BY "app01_userinfo"."age" DESC
```
字典和列表里都可以添加一个或多个元素。如果元素是字符串，注意这些字符串(除了order\_by)是原生sql，不要写\_\_gt之类的语法。

### distinct
```python
#在mysql、sqlite里面，distinct()要写在values()后面才行
models.UserInfo.objects.values('ut_id').distinct()
#select distinct uid from userinfo
# 而在PostgreSQL中需要像下面这样写：
#models.UserInfo.objects.distinct('ut_id')
```

### reverse
reverse(),只有前面是order by（）才有用
没有order by，加不加reverse都是一样的

### defer
映射中排除某些字段属性 （即sql语句中select后面跟着的字段名）
```python
models.UserInfo.objects.defer('username','uid')    #除‘username’和'uid'之外的字段
```

### only
映射中只包含only的参数，和values()有点像。但是only()返回的是queryset里面包含的是对象类型。
```python
result=models.UserInfo.objects.all().only('id','name')
此时返回的是obj，只是它里面的元素要比\*要少。
for i in result:
    print(i.id,i.name,i.age)    #此时能取age，但是每次取age的值，都会去数据库里查询一次。
```

### using
```python
models.UserInfo.objects.all().using('数据库')
去指定数据库拿数据，不写，就是默认的
```

### 原生sql语句

可以写原生sql语句：

1. extra
2. 原生sql语句
3. raw

- 使用django封装过的pymsql

```python
from django.db import connection,connections

cursor=connection.cursor()    #connection=default 默认数据库
cursor=connections['db2'].cursor()    #使用connections连接指定数据库

cursor.execute("""SELECT * from auth_user where id = %s""", [1])

row =cursor.fetchone()
row=cursor.fetchall()
cursor.close()   #这里只需要关闭cursor，不需要关闭connection连接
```

- raw

```python
#def raw(self, raw_query, params=None, translations=None, using=None):

#当raw参数里查询的表与前面调用的表名是一样时
res=models.UserInfo.objects.raw("select * from app01_userinfo")  ##<RawQuerySet: select * from userinfo>

#SQL是其他表时，必须将名字设置为当前UserInfo对象的主键列名
models.UserInfo.objects.raw("select id as uid,title from app01_usertype")  #如果不把名字设置成和前面对象的主键列名一致，会报错。但是后面改不改都不会报错

# 为原生SQL设置参数
    models.UserInfo.objects.raw('select id as nid from userinfo where nid>%s', params=[12,])

 # 将获取的到列名转换为指定列名
name_map = {'first': 'first_name', 'last': 'last_name', 'bd': 'birth_date', 'pk': 'id'}
Person.objects.raw('SELECT * FROM some_other_table', translations=name_map)

 # 指定数据库
models.UserInfo.objects.raw('select * from userinfo', using="default")
```


http://www.cnblogs.com/wupeiqi/articles/6216618.html


<br>


# 4 Cookie
cookie就是保存在浏览器中的键值对。但对于敏感信息，服务器是不放在cookie中的，防止被修改。
当浏览器第一次向服务器发起请求时，服务器给浏览器发送响应时，会将cookie随响应一起发给浏览器。之后，浏览器每次发起请求都会携带cookie来访问服务器。

1. 获取Cookie：
```
方式一：
 request.COOLIES.get('key')     #或者request.COOKIES['key'] 

方式二：
request.get_signed_cookie(key, default=RAISE_ERROR, salt='', max_age=None)
    参数：
        default: 默认值
           salt: 加密盐
        max_age: 后台控制过期时间
```
2、设置Cookie：
```python
rep = HttpResponse(...) 或 rep ＝ render(request, ...)
 
rep.set_cookie(key,value,...)
rep.set_signed_cookie(key,value,salt='加密盐',...)
    参数：
        key,              键
        value='',         值
        max_age=None,     超时时间
        expires=None,     超时时间(IE requires expires, so set it if hasn't been already.)
        path='/',         Cookie生效的路径，/ 表示根路径，特殊的：跟路径的cookie可以被任何url的页面访问
        domain=None,      Cookie生效的域名
        secure=False,     https传输
        httponly=False    只能http协议传输，无法被JavaScript获取（不是绝对，底层抓包可以获取到也可以被覆盖）
由于cookie保存在客户端的电脑上，所以，JavaScript和jquery也可以操作cookie。
return obj
```
3. 通过js来操作cookie
```javascript
方式一：
document.cookie    //返回的当前页面的cookie，格式："xxx=xxxxxx;xxx=xxxxx;"

方式二:
<script src='/static/js/jquery-3.2.1.js'></script>
<script src='/static/js/jquery.cookie.js'></script>
$.cookie("list_pager_num", 30,{ path: '/' });
```


# 5 CBV&FBV

FBV: function basic views
我们之前写的都是FBV，这里就不再赘述。
CBV：class basic views


```python
# app01下的urls：
url(r'^login.html$',views.Login.as_view()),   #Login是一个类，后面必须写as_view()


#app01下的views：

from django.shortcuts import render,HttpResponse
from django.views import View
from app01 import models
class Login(View):

    def dispatch(self, request, *args, **kwargs):   #在执行get和post执行以前，会先执行dispatch，通过dispatch里的反射找到我们自己定义的get和post 方法
        obj=super(Login,self).dispatch(request, *args, **kwargs)
        return obj

    def get(self,request):
        return render(request,'login.html')

    def post(self,request):
        print(request.POST.get('user'))
        return HttpResponse(123)

```
此时如果是get请求就调用Login类下的get方法；如果是post请求就调用Login类下的post方法。

form表单只能一个get和post提交

ajax提交
一般情况：
get 查
post  创建
put 更新
delete 删除

http_method_names=['get','post','put','patch','delete','head','options','trace']


app01下的urls

```python
from django.conf.urls import url
from app01 import views

urlpatterns=[
    url(r'^login.html',views.Login.as_view())  #Login是views下的一个类
]
```
在app01/views下的Login类
```python
from django.shortcuts import render,HttpResponse
from django.views import View
class Login(View):
    def get(self,request):     #get请求时访问
	     return render(request,'login.html')
	def post(self,request):           #post请求时访问
	    return HttpResponse(123)
```



正向关联和反向关联
```python
#在models中创建两个类
class UserType(models.Model):
    title=models.CharField(max_length=32)


class UserInfo(models.Model):
    uid=models.AutoField(primary_key=True)
    username=models.CharField(max_length=32)
    ut=models.ForeignKey('UserType')
```

正向关联：
```
user_list=models.UserInfo.objects.all()
for i in user_list:
     print(i.username ,i.ut.title)
```

反向关联：
```
user_type=models.UserType.objects.all()
for i in user_type:
    for j in i:
	    print(j.userinfo_set.username,j.title)
```


# 6 连表查询
django中的orm，在models中创建类，添加上外键

1. all、filter
[obj,obj,obj]
all() , filter()  返回的是一个queryset，里面包含的是表对像
```python
#正向关联
user_list = models.UserInfo.objects.all()                #访问数据库，并返回的<QuerySet [<UserInfo: UserInfo object>, <UserInfo: UserInfo object>, <UserInfo: UserInfo object>]>
    #print (user_list)
    for user in user_list:
        #print(user.ut)                         #UserType object
        print(user.uid,user.username,user.ut.title)   #通过ut这样来连表，每循环一次，就访问一次数据库。

#反向关联
    user_type=models.UserType.objects.all()
    for i in user_type:
        #print(i.userinfo_set.all())     #<QuerySet [<UserInfo: UserInfo object>]>
       		                                        #如果不写all()，返回的是app01.UserInfo.None
	   for j in i.userinfo_set.all():
            print(j.username,i.title)
```
当是obj对像时，可以使用“obj.属性”的方式来调用
UserInfo和UserType通过外键相关联。此时包含外链的表，直接可以通过".外键名"（注意：这里是外键名，不是外键\_id）获取关联表中对应的一行数据**对像**。而在不包含外键的表中，我们可以通过“包含外键的表名\_set.all()”来
获取包含外键的表中对应的一行数据对象。

2.obj
first()  返回的是一个obj对象

3. values
返回的是queryset，包含的是字典
[{id:1,name:'fd'},{id:2,name:'fd'}]
values  返回的是字典
```python
#正向关联
user_list=models.UserInfo.objects.all().values('uid','ut__title')    #返回的<QuerySet [{'uid':xxx,'ut__title':xxx},{'uid':xxx,'ut__title':xxx}]
    # print(user_list)
    for i in  user_list:
        print(i['uid'],i['ut__title'])

#反向关联
user_type=models.UserType.objects.all().values('id','userinfo','userinfo__username')
    for i in user_type:
        print(i['id'],i['userinfo__username'])
···
反向关联时，要取另一张表，使用“小写的表名”，此时取出的是表的id；如果要取表里的某个字段“小写的表名\_\_字段”。



4. values_list
[(id:1,name:'fd'),(id:2,name:'fd')]
values_list   返回的是queryset，里面包含的是元组
元组和字典的类似，只是索引的时候变成了下标数字

PS：跨表关联时，是用left join连表的







# 模板
母板
模板标记






# 练习
## 分页
django自带分页
自定义分页组件

文件上传
























