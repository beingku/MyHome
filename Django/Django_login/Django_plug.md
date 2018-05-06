# Django中间件与分页部署

>Author:Sinpei 
>Date: 2018-5-6
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 
>期待大神拍砖，轻拍……怕疼
---
## 前言
> 在django中提供了很多的插件功能，用于开发者快速开发，比如中间件（隔离验证），分页器等 还提供了Pillow库，可以进行生成图片，用于登录的验证码图书设计等。

### 中间件 Middleware
#### Middleware 简介
- 是一个轻量级的，底层的插件，可以介入Django的请求和响应的过程（面向切面编程)

- 中间件的本质就是一个python类

- 面向切面编程(Aspect Oriented Programming)简称AOP，AOP的主要实现目的是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤或阶段，以获取逻辑过程中各部分之间低耦合的隔离效果

#### Middleware 处理函数
```
__init__：没有参数，在服务器响应的第一个请求的时候自动调用，用户确定时候启动该中间件

process_request(self, request): 在执行视图前被调用，每个请求上都会被调用，不主动进行返回或返回HttpResponse对象(常用)

process_view(self, request, view_func,view_args, view_kwargs):调用视图之前执行，每个请求都会调用，不主动进行返回或返回HttpResponse对象

process_template_response(self, request, response)：在视图刚好执行完后进行调用，每个请求都会调用，不主动进行返回或返回HttpResponse对象

process_response(self, request, response):所有响应返回浏览器之前调用，每个请求都会调用，不主动进行返回或返回HttpResponse对象
process_exception(self, request, exception):当视图抛出异常时调用，不主动进行返回或返回HttpResponse对象
```

#### 部署自定义 Middleware
- 在项目根目录创建 middleware文件夹
- 在上述文件夹中创建python文件
- 根据业务需求，写入相应的切入需求类，切入方法
```python
class AuthMiddleware(MiddlewareMixin):

    def process_request(self, request):

        # 统一验证登录
        # 返回null/没有返回，代表验证成功

        if request.path == '/uauth/login/' or request.path == '/uauth/regist/':
            return None

        ticket = request.COOKIES.get('ticket')
        if not ticket:
            return HttpResponseRedirect('/uauth/login/')

        users = Users.objects.filter(u_ticket=ticket)
        if not users:
            return HttpResponseRedirect('/uauth/login/')

        request.user = users[0]

```
- 启动中间件在setting.py中配置，在MIDDLEWARE中添加middleware.文件名.类名

```python
'utils.UserAuthMiddleware.AuthMiddleware',
```
---

### 分页器的部署
django提供了分页的工具，存在于django.core中
- 分页库的基本语法：
```
Paginator： 数据分页工具
Page：具体的某一页
count  计算和
num_pages: 页面总和
page_range: 页码列表，从1开始
page(页码)：获取的一个page对象，页码不存在则抛出invalidPage的异常
# 常见错误
invalidPage：page()传递无效页码
PageNotAnInteger：Page()传递的不是整数
Empty:page()传递的值有效，但是没有数据
```
- page对象操作
```
page：

对象获取，通过Paginator的page()方法获得

属性：

object_list: 当前页面上所有的数据对象
number： 当前页的页码值
paginator: 当前page关联的Paginator对象

方法：

has_next()   判断是否有下一页
has_previous():  判断是否有上一页
has_other_pages():  判断是否有上一页或下一页
next_page_number();  返回下一页的页码
previous_page_number(): 返回上一页的页码
len(): 返回当前也的数据的个数
```
- 实例代码
```python
# 业务逻辑实现
def stuPage(request):
    if request.method == 'GET':
        pages = request.GET.get('page_id')
        stus = Student.objects.all()
        paginator = Paginator(stus, 3)
        # 显示第几页
        page = paginator.page(pages)
        return render(request, 'index_page.html', {'stus': page})
```

```html
# 前端代码
{% for stu in stus %}
    姓名：{{ stu.s_name }}
    电话：{{ stu.s_tel}}
    姓名：{{ stu.studentinfo.i_addr }}
    <br>
{% endfor %}

{#获取一共有多少页#}
<h4>一共{{ stus.paginator.num_pages }}页/ 一共{{ stus.paginator.count }}页</h4>
{#获取分页数目，页码#}
<h5>
    {% for i in stus.paginator.page_range %}
        <a href="/stu/stuPage/?page_id={{ i }}"> {{ i }} </a>
    {% endfor %}
</h5>
{#判断前后页是否存在#}
{% if stus.has_previous %}
    <a href="/stu/stuPage/?page_id={{ stus.previous_page_number }}">上一页</a>
{% endif %}

{% if stus.has_next %}
    <a href="/stu/stuPage/?page_id={{ stus.next_page_number }}">下一页</a>
{% endif %}
```






























