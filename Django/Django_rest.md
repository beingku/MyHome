# Django的REST使用说明

>Author: Sinpei 
>Date: 2018-5-4
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 

---

## 前言
REST: Representational State Transfer 所有Web应用遵守的架构设计指导原则。
面向资源是REST明显特征，对于同一个资源的一组不同的操作。资源是服务器上一个可命名的抽象概念。
REST要求，必须通过统一的接口来对资源执行各种操作。对于每个资源只能执行一组有限的操作。
GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT(PATCH)用来更新资源，DELETE用来删除资源。

### 定义规范
```
http://xxx.com/api/
```
### 资源
在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。

### http请求
```
GET（SELECT）：从服务器取出资源（一项或多项）

POST（CREATE）：在服务器新建一个资源

PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）

PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）

DELETE（DELETE）：从服务器删除资源
```

### 过滤 filter
```
?page=2&per_page=100：指定第几页，以及每页的记录数。

?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。

?animal_type_id=1：指定筛选条件
```

### 状态码
服务端向用户返回请求api的结果，在结果中包含了status codes 状态码的，可以通过状态码去判断请求api的状态是成功还是失败
```
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。

201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。

202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）

204 NO CONTENT - [DELETE]：用户删除数据成功。

400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。

401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。

403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。

404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。

406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。

410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。

422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。

500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
```
### 部署Django中的restful
- 安装依赖包
```shell
$ pip install djangorestframework
$ pip install django-filter  # Filtering suppo
```

- 修改响应结构在根项目的setting.py文件中
```python
# 配置restful api 返回结果

REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES':(
        'utils.RenderResponse.CustomJsonRenderer',
    )
}

```

- 重构JSONRenderer下的render方法，在utils文件夹中创建RenderResponse.py
```python
from rest_framework.renderers import JSONRenderer


class CustomJsonRenderer(JSONRenderer):

    def render(self, data, accepted_media_type=None, renderer_context=None):
        '''
        格式
        {
            'code': xxx
            'msg': 请求成功
            data: {返回数据}
        }
        '''

        if renderer_context:
            if isinstance(data, dict):
                msg = data.pop('msg', '请求成功')
                code = data.pop('code', 0)
            else:
                msg = '请求成功'
                code = 0

            response = renderer_context['response']
            response.status_code = 200

            res = {
                'code': code,
                'msg': msg,
                'data': data
            }
            return super().render(res, accepted_media_type, renderer_context)
        else:
            return super().render(data, accepted_media_type, renderer_context)

```
- PATCH请求，传入空置处理
在serializer中定义s_name的序列化，指定错误的信息，为空的话，提示响应的错误信息
![流程图](https://upload-images.jianshu.io/upload_images/10903485-539b1e53241b9db8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 页面异步提交api接口请求，并且在前端通过js刷新页面
- 应用的url文件编写
```python
from rest_framework.routers import SimpleRouter

router = SimpleRouter()
router.register(r'student', views.StudentEdit)

```
- 应用业务逻辑
```python
class StudentEdit(mixins.ListModelMixin,
                  mixins.RetrieveModelMixin,
                  mixins.UpdateModelMixin,
                  mixins.DestroyModelMixin,
                  mixins.CreateModelMixin,
                  viewsets.GenericViewSet):
    # 查询所以信息
    queryset = Student.objects.all()
    # 序列化
    serializer_class = StudentSerializer
```
- 配置serialiers.py
```python
from rest_framework import serializers

from stu.models import Student


class StudentSerializer(serializers.ModelSerializer):
    
    class Meta:
        model = Student
        fields = ['id', 's_name', 's_tel']

    def to_representation(self, instance):
        data = super().to_representation(instance)
        try:
            data['s_addr'] = instance.studentinfo.i_addr
        except Exception as e:
            data['s_addr'] = ''

        return data
```
- 前端ajax
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>显示所有学生信息</title>
    <script type="text/javascript" src="/static/js/jquery.min.js"></script>
    <script type="text/javascript">
        $(function () {
            $('#getStu').click(function () {
                $.get('/stu/student/', function (msg) {
                    msg=msg['data'];
                    s = '<table><tr><td>姓名</td><td>电话</td><td>操作</td></tr>'
                    for (var i = 0; i < msg.length; i += 1) {
                        s += '<tr><td>' + msg[i].s_name + '</td><td>' + msg[i].s_tel + '</td>' +
                            '<td><a href="javascript:;" onclick="update_stu(' + msg[i].id + ')">编辑 </a></td>' +
                            '<td><a href="javascript:;" onclick="del_stu(' + msg[i].id + ')"> 删除 </a></td></tr>'

                    }
                    s += '</table>';
                    $('#div_stu').html(s);
                }, 'json');
            })
        });

        function del_stu(i) {
            csrf = $('input[name="csrfmiddlewaretoken"]').val();
            $.ajax({
                url: '/stu/student/' + i + '/',
                type: 'delete',
                headers: {'X-CSRFToken': csrf},
                dataType: 'json',
                success: function () {
                    alert('删除成功');
                },
                error: function () {
                    alert('删除失败');
                },
            });

        };

        function update_stu(i) {
            s = '姓名：<input type="text" name="name">' +
                '电话：<input type="text" name="tel">' +
                '<input type="button" value="提交" onclick="update(' + i + ')">'
            $('#div_update').html(s)
        };

        function update(i) {
            csrf = $('input[name="csrfmiddlewaretoken"]').val();
            s_name = $('#s_name').val()
            s_tel = $('#s_tel').val()
            $.ajax({
                url: '/stu/student/' + i + '/',
                type: 'PATCH',
                headers: {'X-CSRFToken': csrf},
                dataType: 'json',
                success: function (msg) {
                    alert('修改成功');
                },
                error: function (msg) {
                    alert('修改失败');
                },
            });

        };
        function add_stu() {
            s = '姓名：<input type="text" name="name">' +
                '电话：<input type="text" name="tel">' +
                '<input type="button" value="提交" onclick="add_student()">'
            $('#div_add').html(s)
        };
        function add_student() {
            csrf = $('input[name="csrfmiddlewaretoken"]').val();
            s_name = $('#s_name').val();
            s_tel = $('#s_tel').val();
            $.ajax({
                url: '/stu/student/',
                type: 'POST',
                headers: {'X-CSRFToken': csrf},
                dataType: 'json',
                success: function (msg) {
                    alert('修改成功');
                },
                error: function (msg) {
                    alert('修改失败');
                },
            });

        };
    </script>
</head>
<body>
{% csrf_token %}
<input type="button" value="获取学生信息" id="getStu">
<div id="div_stu">

</div>
<p><a href="javascript:;" onclick="add_stu()"> 添加 </a></p>
<div id="div_update">

</div>
<div id="div_add">

</div>
</body>
</html>
```