# Django后台管理工具

>Author: Sinpei 
>Date: 2018-4-24
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 

---


## 前言
Django提供了基于Web的管理工具。集成了管理后台。在管理后台中可以对我们自定义model进行CRUD操作，以及分页，条件筛选等操作。

---
## admin管理后台使用流程

### 1. 确保基础配置
a) 在项目的settings.py文件中的INSTALLED_APPS中确保有django.contrib.admin。
b) 在项目的urls.py中确定如下：
```python
urlpatterns = [
    url(r'^admin/', admin.site.urls)
]
```
c) 一切前提配置OK,可以运行开发服务器（python manage.py runserver)，看到如下界面,就算配置成功。
![admin_login.jpg](https://upload-images.jianshu.io/upload_images/10903485-78569647faf139ef.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
### 2.在创建额app的model中定义一个模型，以Student为例。
```python
class Student(models.Model):
    name = models.CharField(max_length=20)
    sex = models.BooleanField()
    
    class Meta:
      db_table = 'student'
```
___
### 3.创建admin后台密码
```shell
$ python manage.py createsuperuser
```
![create_superuser.jpg](https://upload-images.jianshu.io/upload_images/10903485-9b89323c86206758.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
### 4.登录管理后台，自定义模型进行CRUD操作
在管理后台中操作模型对象，需要在app的admin.py进行声明，以Student模型为例。
```python
class StudentAdmin(admin.ModelAdmin):

    def set_sex(self):
        if self.sex:
            return '男'
        else:
            return '女'

    # 修改性别字段
    set_sex.short_description = '性别'
    # 显示选取字段
    list_display = ['id', 'name', set_sex]
    # 过滤
    list_filter = ['name']
    # 搜索
    search_fields = ['name']
    # 分页
    list_per_page = 2


admin.site.register(Student, StudentAdmin)
```

----
### 5.除了上述方式注册，还有装饰器写法
```python
# 第二种注册方式，装饰器模式
@admin.register(Student)
class StudentAdmin(admin.ModelAdmin):

    def set_sex(self):
        if self.sex:
            return '男'
        else:
            return '女'

    # 修改性别字段
    set_sex.short_description = '性别'
    # 显示选取字段
    list_display = ['id', 'name', set_sex]
    # 过滤
    list_filter = ['name']
    # 搜索
    search_fields = ['name']
    # 分页
    list_per_page = 2

```

---

### 6.管理效果图
![admin.jpg](https://upload-images.jianshu.io/upload_images/10903485-17e2b7fb121a4c19.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<br>
![student的CRUD](https://upload-images.jianshu.io/upload_images/10903485-73e3df3b1eb69371.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)













