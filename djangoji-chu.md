---
title: django基础
tags: django,路由,
grammar_cjkRuby: true
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
python manage.py runserver 127.0.0.1:8080   (默认是8000监听端口)
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
    'app01',                #注册app
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
STATICFILES_DIRS = (     #是一个元组
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
<link rel="stylesheet" href="/static_/css/classes.css">   <!-- 这样写的样式是无效的，django是不能渲染到该样式的 -->
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
        'HOST': '',   #默认不写，是localhost
        'PORT': ,  # 端口号，mysql默认是3306
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
python manage.py runserver 0.0.0.0  #用xxx.xxx.xxx.xxx：xxxx的ip和端口启动项目
python manage.py startapp appname  #创建新的app
python manage.py syncdb
python manage.py makemigrations [appname]    #实现表的变化，生成变化后的表实现。根据app中models.py文件中的代码,在同一个app下的migrations文件夹中生成关于ORM操作的记录和日志。不加appname将所有存在于setting.py中INSTALLED_APPS下的app全部实现一遍
python manage.py migrate [appname]   #根据app中migrations文件夹下的记录和日志，对数据库里的数据表和数据行进行增删改查的操作。不加appname是对所有的app执行操作
python manage.py createsuperuser
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
<a href='/index/？nid=123'>   #http://127.0.0.1:8000/index/?nid=123

# urls中的路由信息
url('^index$',views.index),   #以index开始，并以index结束

# views中的index函数定义
def index(request):
    nid=request.GET.get('nid')   #获取get请求中的nid的值
```

## 2 基于正则的路由(动态路由)
只需要在路由调用的对应方法中在函数定义时，加入额外的参数，通过这些参数来获取。获取的方式有两种：
### 1 根据位置参数来取值

```python
#浏览器中的get请求：
<a href="/edit/{{ id }}/">    #http://127.0.0.1:8000/edit/123

#urls中的路由信息
url('^edit/(\w*)'，views.edit)

#views中的edit函数定义
def edit(request,a1):   #如果有多个位置参数，像这样依次a1，a2...去取
    print(a1)   #不需要再通过request.GET来获取,直接就可以拿到a1的值。（a1可以随便取名字）
```

### 2 根据关键字参数来取值
在路由命名分组里的名称必须和路由调用的函数里的参数名是一样的。（不一样的就是位置参数）
```python
#浏览器中的get请求：
http://127.0.0.1:8000/edit/123/456/

#urls中的路由信息
url('^edit/(?P<a1>\w*)/(?P<a2>\w*)/'，views.edit)

#views中的edit函数定义
def edit(request,a2,a1):   #如果有多个位置参数，像这样依次a1，a2...去取
    print(a1)   #不需要再通过request.GET来获取,直接就可以拿到a1的值。（a1可以随便取名字）
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
#def index(request,*args,**kwargs):    #依然会报错
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
    print(name)    #123abc
    print(id)       #333
    return HttpReponse(11111)
```


### 4 为路由映射名称
相当于给路由系统中的url取一个别名，而**这个别名实际上只是映射了正则匹配中前面那一部分固定的url，后面正则匹配的部分是不被映射的**。
使用时，需要注意：
1. 在url调用的函数里面，通过reverse('url-mapping-name',args...)来使用路由映射关系，且args的个数必须和路由url中的正则匹配这一部分的参数个数一致。不然会报错：
```python
NoReverseMatch at /login2/123
Reverse for 'n2' with no arguments not found. 1 pattern(s) tried: ['login2/(?P<a2>\\w+)']
```
2. 可以在html模板中使用{% url 'url-mapping-name' args... %}，参数args的个数也必须和路由url中正则匹配这一部分的参数个数一致。否则也会报和上面一样的错误。
3.无论是在html模板中，还是在views中的函数都需要添加“''”引号把url-mapping-name引起来。

PS：**给路由映射名称，是django特有的，其他框架没有**

```python
#浏览器中的代码：
{% url 'n1' %}    #最终显示效果 /login/

#urls路由：
url(r'^login/',views.login,name='n1'),

#views中的login函数：
from django.urls import reverse
def login(request):
    v=reverse('n1')   #根据名字‘n1’通过reverse方法来反向生成url。
    print(v)    #/login/
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
{% url 'n2' 2017 %}     #/login2/2017
{% url 'n2' 'a1' %}       #/login2/a1
<!-- {% url 'n2' %}  -->      #此时会报错
#错误信息：NoReverseMatch at /login2/123
# Reverse for 'n2' with no arguments not found. 1 pattern(s) tried: ['login3/(?P<a3>\\w+)']

#urls路由：
url (r'^login2/(\w+)', views.login2, name='n2'),

#views中的login2函数：
from django.urls import reverse
def login2(request,a1):
    v=reverse('n2',args=(0000))  #根据名字‘n2’通过reverse方法来反向生成url。
    print(v)        #/login/0000
    print(a1)           #123
    return render(request,'login2.html')
```
通过urls.py中取的名字，来反向生成url。如果urls.py中的url有参数，我们可以通过reverse方法来让它反向生成我们所想要的ur。此时生成的url和我们在导航栏中输入的url没有关系。相当于我们在服务端修改了url。
注意：**如果我们在reverse中设置了参数（位置参数和关键字参数），此时在html模板中的{% url '路由映射名' 'xxx' %}，且xxx不能为空。**

##### 2 urls路由中的参数是位置参数
```
#url地址
http://127.0.0.1:8000/login2/123

#浏览器中的代码：
{% url 'n2' 2017  %}     #/login2/2017


#urls路由：
url (r'^login2/(？P<a2>\w+)', views.login2，name='n2'),

#views中的login2函数：
from django.urls import reverse
def login2(request,a2):
    #print(reverse('n2'))     #此时会报错
    #错误信息：NoReverseMatch at /login2/123
     #Reverse for 'n2' with no arguments not found. 1 pattern(s) tried: ['login2/(?P<a2>\\w+)']
    
    v=reverse('n2',args=(0000))  #根据名字‘n2’通过reverse方法来反向生成url。
    print(v)        #/login/0000
    print(a1)           #123
    return render(request,'login2.html')
```
PS:'n2'是路由映射名称，不要认为进行`n2=reverse('n2',args=(0000))`的操作，在网页上显示的会变成`/login/0000/2017`
****

#### 2 路由映射名称中使用的方法
在模板中使用：
```
{% url 'url-mapping-name' args... %}  #后面args的参数个数必须和路由url中正则匹配的参数的个数一致   
```
在views定义函数中：
```
from django.urls import reverse
reverse('url-mapping-name',args...)    #后面args的参数个数必须和路由url中正则匹配的参数的个数一致  
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
    #v=reverse('n2',kwargs={'a3':'abc'})  #根据名字‘n2’通过reverse方法来反向生成url。
    #print(v)        #/login/abc
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
from django.conf.urls import include   #在urls路由配置文件里，所以是django.conf.urls，这样来理解便于记忆

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
http://localhost:8000/app01/login/    //就能访问login
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 当我们输入上面的网站时，会优先在与项目同名的文件夹中的urls.py匹配，当它匹配到
`^app01/`,此时会进入到`app01.urls`中去匹配。当它匹配到`'^login/'`时，会执行
views.login函数。



### 用户输入错误时跳转到某个网页
当用户输入错误时，我们可以让他访问某个网页
```
urlpattern=[
    url(r'^',views.login),   也可以写成url(r'^.*',views.login)
]
```


### 注意：
```
<a href='/index/?nid={{ i }}'>
```
//这种写法,以get请求，百度会认为你的 127.0.0.1/?nid=1 ,nid后面的内容会经常变，SEO权重会很低。不利于百度爬虫收录网页
推荐使用：**127.0.0.1/ffff **
**比上面这种更美观，同时让百度收录优先级更高。**
而像我们如果写成：
```
url(r'^edit/(\w+).html',views.edit),  //伪静态

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
        'HOST': '',   #默认不写，是localhost
        'PORT': ,  # 端口号，mysql默认是3306
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
python manage.py makemigrations [appname]    #实现表的变化，生成变化后的表实现。根据app中models.py文件中的代码,在同一个app下的migrations文件夹中生成关于ORM操作的记录和日志。不加appname将所有存在于setting.py中INSTALLED_APPS下的app全部实现一遍
python manage.py migrate [appname]   #根据app中migrations文件夹下的记录和日志，对数据库里的数据表和数据行进行增删改查的操作。不加appname是对所有的app执行操作
```
所以当我们在命令行执行上面这两条命令时，django自带app也会生成相应的数据库表。
其中生成的表命名是：appname\_classname。如：app01\_class。

## 3 Django ORM 操作数据表
编写app下的model.py文件：
### 1 创建数据表
```python
from django.db import models

class Class(models.Model):     #必须继承models.Model  类名会映射成表名
    cid=models.BigAutoField(primary_key=True)      #设置主键自增字段，long类型   
    #如果是AutoField，就是int类型自增
    #如果不写此行，django默认会帮我们创建一个id字段：主键自增int类型
    cname=models.CharField(max_length=16)   #设置字段cname，最大长度为16

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
| Tables_in_db_manage        |
+----------------------------+
| app01_class                |
| auth_group                 |
| auth_group_permissions     |
| auth_permission            |
| auth_user                  |
| auth_user_groups           |
| auth_user_user_permissions |
| django_admin_log           |
| django_content_type        |
| django_migrations          |
| django_session             |
+----------------------------+
11 rows in set (0.00 sec)
```
其中生成的表命名是：appname\_classname。如：app01\_class。
```cmd
mysql> show create table app01_class;
+-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table       | Create Table                                                                                                                                                        |
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
class Class(models.Model):     #必须继承models.Model  类名会映射成表名
    class_id=models.BigAutoField(primary_key=True)  
    #cname=models.CharField(max_length=32)   #设置字段cname，最大长度为16
    #age=models.SmallIntegerField()
    age = models.SmallIntegerField (null=True)   #可以为空
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
| Table       | Create Table                                                                                                                                                                    |
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
23、models.ImageField   图片
24、models.FilePathField 文件
```

大致可分为： 字符串、数字、时间、二进制、自增（primary_key=True）几类。大致分为 字符串、数字、时间、二进制、自增（primary_key=True）几类。

### 5 字段参数
```
null                # 是否可以为空
default             # 默认值
primary_key         # 主键
db_column           # 列名
db_index            # 索引(db_index=True)
unique              # 唯一索引(unique=True)
unique_for_date     # 只对日期索引
unique_for_month    # 只对月份索引
unique_for_year     # 只对年做索引
auto_now            # 创建时，自动生成时间
auto_now_add        # 更新时，自动更新为当前时间
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

其中数据部分在django中是用orm来完成的。
AutoField  自增 默认整型
BigAutoField


















python manage.py makemigrations 会在migrates文件夹下生成相关的配置文件（日志）
python manage.py migrate  会通过migrates文件夹配置文件来生产或修改表