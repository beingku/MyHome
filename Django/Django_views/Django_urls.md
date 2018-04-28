# Django的Url使用说明

>Author: Sinpei 
>Date: 2018-4-26
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 

---

### 前言
> MVT模式中的V代表视图，在Django中的视图主要是用来接受Web请求，并做出响应。视图的本质以python函数做出响应，以json数据形式或者以网页的形式响应。
> 重定向到另一个网页的视图响应过程：
> 浏览器输入 -> Django获取信息并去掉ip，端口，身下路径 -> urls路由匹配 -> 视图响应 -> 反馈给浏览器

---

### url配置
- 配置流程
settings中制定根级url配置文件，对应的属性ROOT_URLCONF

- urlpatterns
一个url实例的对象，全在根配置搞定，内部由url组成。如：url(r'^stu/', views.stu)

- 导入其他的url配置
```
在应用中创建urls.py 文件，编写配置规则，在工程urls.py中进行
导入包含
    From django.conf.urls import include
    urlpatterns = [url(r’^xxx/’, include(‘app.urls’))]
注意：
    url配置正则注意事项：
        正则匹配时从上到下进行遍历，匹配到就不会继续向后查找了
        匹配的正则前方不需要加反斜杠
        正则钱需要加（r）表示字符串不转义
```

---

### url的路径说明
- url()中参数说明
   - regex 
“regex”经常被用作正则表达式的缩写，正则表达式是一种对字符串进行模式匹配的语法，对于我们而言，就是对url模式的匹配。Django会把请求的url从第一条正则表达式开始匹配，直到找到一个可以匹配上的正则表达式为止。
需要注意的是，正则表达式并不查询get或者post参数，以及域名。
   - view
当正则表达式被匹配，view函数就会被调用。而且HttpRequest对象会作为第一个参数，其它被正则表达式捕获的值会作为其它参数传入到函数中。
   - kwargs
其它的参数以字典的形式传入
   - name
给你的url起个名字让能让你在其它地方明确引用它。这个强大的功能可以让你只要修改一个文件就能修改你的URL 模式。
```
如果需要从url中获取一个值，需要对正则加小括号
url(r‘^grade/(\d+)$’, views.getStudent)
注意，url匹配中添加了()取参，在请求调用的函数中必须接收 
def getStudent(request, classid)
```

[流程](images/django_url_patterns.png)

> 网址中多了 (\d+), 正则表达式中 \d 代表一个数字，+ 代表一个或多个前面的字符，写在一起 \d+ 就是一个或多个数字，用括号括起来的意思是保存为一个子组，每一个子组将作为一个参数，被 views.py 中的对应视图函数接收。

- url()中关键字获取参数
```
参数也可以使用关键字参数形式,参数顺序可以任意 url(r’news/(?P\d{4})/(?P\d)+
/(?P\d+)$’, views.getNews)
```

[流程](images/django_urls_more.png)

> ?P<value> 将参数传给views里的对应方法

---

### 逆向解析
> Django中提供了一个关于URL的映射的解决方案，你可以做两个方向的使用：
> - 有客户端的浏览器发起一个url请求，Django根据URL解析，把url中的参数捕获，调用相应的试图，获取相应的数据，然后返回给客户端显示。
> 
> - 通过一个视图的名字，再加上一些参数和值，逆向获取相应的URL。
>
>第一个就是平常的请求有URLconf来解析的过程，第二个叫做，url的逆向解析，url逆向匹配，url的逆向查阅等。

Django提供了不同的层级的url逆向处理工具：
 1. 在模板templates中，使用url标记，如：{% url %}
 ```
 关键字参数{% url namespace:name key=value key1=value1 %}
 ```

 > 定义url： 在根urls中 url(r’^apps/’, include(‘App.urls’, namespace=’app’))在子urls中 url(r’^hello/(\d+)’, views.hello, name=’sayhello’) 在模板中使用:<a href=’{% url ‘app:sayhello’ grade_id%}’> 其中grade_id是参数，其中参数可以使用位置参数，也可以使用关键词参数，和配置urls一样 
[反向解析流程](images/django_redirect_revere.png)
 
 2. 在Python代码中，使用django.core.urlresolvers.reverse()方法
 ```
 HttpResponseRedirect(reverse(namespace:name,
 kwargs = {key1 : value1, key2 : value2}))
 ```
 3. 在更高一层级的处理url中，用get_absolute_url()方法

#### URL的别名
> 在此需要先引入url的别名，简单的说就是给对应的url路径起了个名字，在views，models,templates中可以使用该名字调用。

- 在项目文件的设置
在include()中加入namespace设置
 [项目文件urls.py](images/namespace.png)
 
- 在应用文件的设置
在url()中加入name设置
 [应用文件urls.py](images/name.png)
 
> 用处：避免因为写“死网址”，会使得在改了网址（正则）后，模板（template)，视图(views.py，比如用于URL跳转)，模型(models.py，获取记录访问地址等）用了此网址的，都必须进行相应的更改，修改的代价很大，一不小心，有的地方没改过来，就不能用了。
























