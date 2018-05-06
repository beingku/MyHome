# Django登录/注册/注销功能实现说明

>Author:Sinpei 
>Date: 2018-5-6
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 
>期待大神拍砖，轻拍……怕疼

---

## 前言
Django项目的功能，本质上都是对数据表的增删改查。当然作为最常用用户登录模块也不例外。本文着重介绍Django的登录模块
功能的实现。


## 实现思路
- 在models中创建相应的数据表（用户表）
- 编写对应的登录/注册/注销 页面
- 在路由urls中配置相关路径
- 在views中编写登录/注册/注销相应的方法


## 实现过程
### 模型创建
```python
from django.db import models


class Users(models.Model):
    u_name = models.CharField(max_length=10, unique=True)
    u_password = models.CharField(max_length=255)
    # 记录cookie
    u_ticket = models.CharField(max_length=30, null=True)
    class Meta:
        db_table = 'dy5_user'
```
### url路由配置
```python
# 在自定义登录功能app的urls.py文件中添加
# 注册/登录/注销
urlpatterns = [
    url(r'^regist/', views.regist),
    url(r'^login/', views.login),
    url(r'^logout/', views.logout),
]
# 当然不要忘记配置项目urls.py
url(r'^uauth/', include('uauth.urls'))
```
### 注册功能
#### 页面代码register.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>注册页面</title>
</head>
<body>
<form action="" method="post">
    姓名：<input type="text" name="name">
    密码：<input type="password" name="password" id="">

    <input type="submit" value="提交">
</form>
</body>
</html>
```


#### views方法实现
```python
def regist(request):
    if request.method == 'GET':
        return render(request, 'day6_register.html')

    if request.method == 'POST':
        # 注册
        name = request.POST.get('name')
        # make_password 引用自django.contrib.auth.hashers
        password = make_password(request.POST.get('password'))

        Users.objects.create(u_name=name, u_password=password)
        return HttpResponseRedirect('/uauth/login/')
```

### 登录功能
#### 页面代码 login.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录页面</title>
</head>
<body>
<form action="" method="post">
    姓名：<input type="text" name="name">
    密码：<input type="password" name="password" id="">

    <input type="submit" value="提交">
</form>
</body>
</html>
```
#### 方法实现 views.py
```python

def login(request):
    if request.method == 'GET':
        return render(request, 'login.html')

    if request.method == 'POST':
        # 如果登录成功，绑定参数到cookie中，set_cookie
        name = request.POST.get('name')
        password = request.POST.get('password')
        # 判断用户名是否存在
        if Users.objects.filter(u_name=name).exists():
            user = Users.objects.get(u_name=name)
            # 判断密码是否正确
            if check_password(password, user.u_password):
                # ticket = 'zxcvbnm'
                ticket = ''
                for i in range(15):
                    # 获取随机的字符串
                    ticket += random.choice('qwertyuiopasdfghjklzxcvbnm1234567890')
                now_time = int(time.time())
                ticket = 'TK_' + ticket + str(now_time)
                # 绑定令牌到cookie
                # response = HttpResponse('登录成功')
                # 登录成功，页面跳转。
                response = HttpResponseRedirect('/stu/index/')
                # max_age 存活时间
                response.set_cookie('ticket', ticket, max_age=3000)
                # 存在服务端
                user.u_ticket = ticket
                user.save()
                return response
            else:
                # return HttpResponse('用户密码错误')
                return render(request, 'login.html', {'password': '用户密码错误'})
        else:
            return render(request, 'day6_login.html', {'name': '用户名不存在'})

```

### 注销功能

#### 业务逻辑 views.py
```python
def logout(request):
    if request.method == 'GET':
        # 注销功能实现在于直接删除令牌cookie
        response = HttpResponseRedirect('/uauth/login')
        response.delete_cookie('ticket')
        return response
```


## Django内置登录模块
作为Django的项目，登录/注册是常备功能项。但是，有些项目不用自己去写着一模块功能。所以我们不必自己去造轮子，本文介绍如何详细实用
Django内置的用户注册/登录/注销功能。
### 路由配置 urls.py
```python
from django.conf.urls import url

from uauth import views

urlpatterns = [
    url(r'^djregist/', views.djregist),
    url(r'^djlogin/', views.djlogin),
    url(r'^djlogout/', views.djlogout)
]

```
```python
# 在用户管理的功能模块中添加登录认证路由
  url(r'^index/', login_required(views.index)),
```
```python
MIDDLEWARE = [
#在setting中注销以前自己写的中间件
# 'utils.UserAuthMiddleware.AuthMiddleware',
]
```
### 页面代码
页面代码如本文前部分的，在此不再重复

### 业务逻辑 views.py
```python
from django.contrib.auth.models import User
# 登录功能
def djlogin(request):
    if request.method == 'GET':
        return render(request, 'day6_login.html')

    if request.method == 'POST':
        name = request.POST.get('name')
        password = request.POST.get('password')

        # 验证用户名和密码，如果通过的话，返回user对象
        user = auth.authenticate(username=name, password=password)

        if user:
            # 验证成功
            # 登录用户
            auth.login(request, user)
            return HttpResponseRedirect('/stu/index/')
        else:
            return render(request, 'day6_login.html')

# 注册功能
def djregist(request):
    if request.method == 'GET':
        return render(request, 'day6_register.html')

    if request.method == 'POST':
        name = request.POST.get('name')
        password = request.POST.get('password')

        User.objects.create_user(
            username=name,
            password=password,
        )

        return HttpResponseRedirect('/uauth/login/')

# 注销功能
def djlogout(request):
    if request.method == 'GET':
        auth.logout(request)

        return HttpResponseRedirect('/stu/djlogin/')

```
