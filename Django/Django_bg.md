# Django开发知识（一）

>Author:Sinpei 
>Date: 2018-4-23
>GitHub:https://github.com/sinpeilee/MyHome
>Email:sinpeilee@163.com 

---


[TOC]

### Django简介
---
>Django是一个开放源代码的Web应用框架，由Python写成。采用了MVT的软件设计模式，即模型Model，视图View和模板Template。详见[官网](https://www.djangoproject.com)
>Django发布于2005年7月，最初是被开发用于管理劳伦斯出版集团旗下的以新闻内容为主的网站的，即CMS(内容管理系统)软件。


#### MVC模式简介
---
>MVC模式（Model–view–controller）是软件工程中的一种软件架构模式，把软件系统分为三个基本部分：模型（Model）、视图（View）和控制器（Controller）。
>MVC模式的目的是实现一种动态的程序设计，使后续对程序的修改和扩展简化，并且使程序某一部分的重复利用成为可能。除此之外，此模式通过对复杂度的简化，使程序结构更加直观。
>核心思想：解耦
专业人员可以通过自身的专长分组：
* 控制器（Controller）- 负责转发请求，对请求进行处理。
* 视图（View） - 界面设计人员进行图形界面设计。
* 模型（Model）-程序员编写程序应有的功能（实现算法等等）、数据库专家进行数据管理和数据库设计(可以实现具体的功能)。

![MVC说明](https://img-blog.csdn.net/20180423140509138?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODE5ODI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### MVT模式简介
---
>严格来说，Django的模式应该是MVT模式，本质上和MVC没什么区别，也是各组件之间为了保持松耦合关系，只是定义上有些许不同。
Model： 负责业务与数据库(ORM)的对象。
View： 负责业务逻辑并适当调用Model和Template。
Template: 负责把页面渲染展示给用户。
>Django框架的核心除了上述3项，还有一个基于正则表达式的URL分发器；一个轻量级的、独立的Web服务器，用于开发和测试；一个表单序列化及验证系统，用于HTML表单和适于数据库存储的数据之间的转换；一个缓存框架，并有几种缓存方式可供选择；中间件支持，允许对请求处理的各个阶段进行干涉；内置的分发系统允许应用程序中的组件采用预定义的信号进行相互间的通信；一个序列化系统，能够生成或读取采用XML或JSON表示的Django模型实例；一个用于扩展模板引擎的能力的系统。

### Django安装（Mac OS)
---
#### 1.安装虚拟环境
```
$ pip install virtualenv
```

> virtualenv使用场景:当开发成员负责多个项目的时候，每个项目安装的库又是有很多差距的时候，会使用虚拟环境将每个项目的环境给隔离开来。当然除了virtualenv可以起到隔离环境的作用，还有其他技术方案来实现，而且上线流程简单，大大减轻运维人员的出错率，比如每一个项目使用一个docker镜像，在镜像中去安装项目所需的环境，库版本等等

---

#### 2.安装env环境
```
# env代表创建的虚拟环境的名称
$ virtualenv --no-site-packages -p [PATH] env
# 默认python版本安装
$ virtualenv --no-site-packages env
```
---

#### 3.进入/退出 env
```
# 先到env/bin下找到activate
$ cd /env/bin
$ source activate
$ deactivate
```
---

#### 4.pip的使用
```
# 查看虚拟环境下安装的所有的包
$ pip list

# 查看虚拟环境中通过pip安装的包
$ pip freeze

# 安装django
$ pip install django==1.11
```
---

#### 5.在虚拟环境下到自定目录下创建Django项目
```
$ django-admin startproject helloworld
```
---

#### 6.启动Django项目
```
# port端口自定义
$ python manage.py runserver ip: port
```
---

#### 7.创建app
```
#appname 自定义
$ python manage.py startapp appname

并在项目setting.py中添加app信息
INSTALLED_APPS = [
	'appname',
]
```

---
#### 8.Django项目结构与基础设置
![Django项目结构](https://img-blog.csdn.net/20180423191643998?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1ODE5ODI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)


Django项目创建好了需要进行一些基础配置:

   - 修改项目时区和语言
```python
#修改主项目下的settings.py文件
#修改项目语言为中文
LANGUAGE_CODE = 'zh-hans'
#修改项目时区
TIME_ZONE = 'Asia/Shanghai'
```
   - 配置数据库
```python
#在settings.py中找到DATABASES
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': 'xxx.xxx.xxx.xxx',
        'USER': 'root',
        'PASSWORD': '*******',
        'PORT': '3306',
        'NAME': 'helloworld'

    }
```

   - app目录下单独创建urls
```python
#这样做，将单一职责原则进行到底，每个app都有自己的url，最后在主项目urls.py的urlpatterns中加入一下声明即可
url('', include('appname.urls'))

```

---
#### 8.app简介
> app是项目中一个功能模块的具体实现。
_ _ init_ _ .py：app初始化
apps.py: settings.py里面注册app的时候需要使用到。一般不推荐这样使用
from app.apps import AppConfig
AppConfig.name
models.py: 写模型的地方
views.py: 写处理业务逻辑的地方

---
#### 9.迁移数据库
```python
#在工作目录下的__init__.py文件中输入以下完成驱动加载
import pymysql
pymysql.install_as_MySQLdb()

```
- 定义模型
**重要概念：模型，表，属性，字段**
一个模型类在数据库中对应一张表，在模型类中定义的属性，对应模型对照表中的一个字段
```python
#创建学生模型类
class Student(models.Model):
    name = models.CharField(max_length=20)
    sex = models.BooleanField()

    class Meta:
        db_table = 'stu'

```
- 调用方法
在views.py中对上述类进行调用
```python
def add_student(request):
    stu = Student()
    stu.name = '李四'
    stu.sex = 0
    stu.save()
    return HttpResponse('添加成功')
```
- 配置app的url
```python
from django.conf.urls import url

from student import views

urlpatterns = [
    url(r'addstu/', views.add_student)
]
```
- 迁移数据库
```
#生成迁移文件
$ python manage.py makemigrations
#执行迁移生产数据库
$ python manage.py migrate
#注意：生成迁移文件的时候，并没有在数据库中生成对应的表，而是执行migrate命令之后才会在数据库中生成表
```
#### 10.ORM
ORM(Objects Relational Mapping)对象关系映射，是一种程序技术，用于实现面向对象编程语言里不同类型系统的数据之间的转换。可以简单理解为翻译机。

![ORM效果图](https://img-blog.csdn.net/20150807202113748?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

