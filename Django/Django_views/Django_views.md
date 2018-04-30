# Django的视图说明

>Author: Sinpei 
>Date: 2018-4-28
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 

---

### 前言
前面介绍了MTV的M与T，现在来认识平时用的最多的views。

### 请求与响应

#### HttpRequest定义

服务器在接收到http请求后，会根据报文创建HttpRequest对象
视图中第一个参数就是HttpRequest对象
Django框架会进行自己的包装，之后传递给视图
属性：

    path        请求的完整路径
    method   请求的方法，通常get，post
    Encoding 编码方式，常用utf-8
    Get          类似字典的参数，包含了get的所有参数
    post	       类似字典的参数，包含了post的所有参数
    Files         类似字典的参数，包含了上传的文件
    Cookies    字典，包含了所有的COOKIES
    Session    类似字典，表示会话
    方法： is_ajax()   判断是否是ajax()，通常在移动端和js中


#### 响应QueryDict类型

    1）类似字典的结构数据，与字典的区别，可以存在相同的键
    2）GET和POST都是QueryDict对象
    3）QueryDict中数据获取方式
        dict[‘name’] 或者dict.get(‘name’)
        获取指定key对应的所有值
        dict.getlist(‘name’)




#### 响应
    1）可以直接返回一个HttpResponse对象：
        服务器返回给客户端的数据，HttpResponse由程序员自己创建
        不使用模板，直接HttpResponse()
    2）可以返回模板
    调用模板，进行渲染，直接使用render一步到位
    返回表达式：
        render(request, template_name, context)
            request  请求体对象
            template_name  模板路径
            context  字典参数，用来填坑

    3）属性
        Content   返回的内容
        Charset   编码格式
        status_code  响应状态码（200,4xx,5xx）
            4xx 客户端的错误
            5xx 服务端的错误
        content_type   MIME类型，定义传输类型的，比如有xml，html，png等等，比如
        content_type=’image/jpg’
    4）方法
        init    初始化内容
        write(xxx) 直接写到文本
        flush 冲刷缓冲区
        set_cookie(key, value=’’, max_age=None, exprise=None)
        delete_cookie(key)   删除cookie，上面是设置
    4）重定向
        HttpResponseRedirect响应重定向：可以实现服务器内部的跳转
        Return HttpResponseRedirect(‘/xxx/xxx’)
        使用的时候推荐使用反向解析
        JsonResponse
        使用json数据的请求，通常用在异步请求上jsonResponse(dict)
        content_type是application/json

---

#### views下对对象操作
- 新建一个对象的方法有以下几种：
```
Person.objects.create(name=name,age=age)

p = Person(name="WZ", age=23)

p.save()

p = Person(name="TWZ")

p.age = 23

p.save()

Person.objects.get_or_create(name="WZT", age=23)

这种方法是防止重复很好的方法，但是速度要相对慢些，返回一个元组，第一个为Person对象，第二个为True或False, 新建时返回的是True, 已经存在时返回False.
```


- 获取对象有以下方法：
```
Person.objects.all()

Person.objects.all()[:10] 切片操作，获取10个人，不支持负索引，切片可以节约内存

Person.objects.get(name=name)



get是用来获取一个对象的，如果需要获取满足条件的一些人，就要用到filter

Person.objects.filter(name="abc")  # 等于Person.objects.filter(name__exact="abc") 名称严格等于 "abc" 的人

Person.objects.filter(name__iexact="abc")  # 名称为 abc 但是不区分大小写，可以找到 ABC, Abc, aBC，这些都符合条件



Person.objects.filter(name__contains="abc")  # 名称中包含 "abc"的人

Person.objects.filter(name__icontains="abc")  #名称中包含 "abc"，且abc不区分大小写



Person.objects.filter(name__regex="^abc")  # 正则表达式查询

Person.objects.filter(name__iregex="^abc")  # 正则表达式不区分大小写



filter是找出满足条件的，当然也有排除符合某条件的

Person.objects.exclude(name__contains="WZ")  # 排除包含 WZ 的Person对象

Person.objects.filter(name__contains="abc").exclude(age=23)  # 找出名称含有abc, 但是排除年龄是23岁的
```
- 删除符合条件的结果
和上面类似，得到满足条件的结果，然后 delete 就可以(危险操作，正式场合操作务必谨慎)
```
Person.objects.filter(name__contains="abc").delete() 
# 删除 名称中包含 "abc"的人
```

- 更新某个内容
  - 批量更新，适用于 .all()  .filter()  .exclude() 等后面 (危险操作，正式场合操作务必谨慎) 
  ``` 
  Person.objects.filter(name__contains="abc").update(name='xxx') 
  # 名称中包含 "abc"的人 都改成 xxx
  ```
  
  - 单个 object 更新，适合于 .get(), get_or_create(), update_or_create() 等得到的 obj，和新建很类似。
  ```
  author = Author.objects.get(name="hello")
  author.name="nihao"
  author.save()  # 最后不要忘了保存！！！
  ```
- QuerySet 是可迭代的，比如：
```
es = Entry.objects.all()
for e in es:
    print(e.headline)
```
- 注意事项：
(1). 如果只是检查 Entry 中是否有对象，应该用 Entry.objects.all().exists()
(2). QuerySet 支持切片 Entry.objects.all()[:10] 取出10条，可以节省内存
(3). 用 len(es) 可以得到Entry的数量，但是推荐用 Entry.objects.count()来查询数量，后者用的是SQL：SELECT COUNT(*)
(4). list(es) 可以强行将 QuerySet 变成 列表
