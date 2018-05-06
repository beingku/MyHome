# Django网页模版导入与文件上传

>Author:Sinpei 
>Date: 2018-5-6
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 
>期待大神拍砖，轻拍……怕疼

---

## 前言
> 作为普通的全栈开发人员，自己写前端页面费时费力，而且很难满足客户要求。这时候，需要去寻找优秀的前端页面给自己的项目包装。
> 在很多开发功能里面都涉及到需要上传图片的地方，比如头像，或者封面图，或者内容中插入图片信息等，此指南就是来简单的实现一个图片文件上传并且展示的功能演示

--- 
### 操作流程
- 在项目根目录下创建静态文件夹static
- 在项目配置文件setting.py
```python
# 配置静态文件
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]

```
- 将欲加入的网页静态资源放入static文件夹中，比如css, img, js...
- html文件当然应该放在tempates中, 并做出相应资源的路径更改
```html
    <link href="/static/css/default.css" rel="stylesheet" type="text/css" />
	<!--必要样式-->
    <link href="/static/css/styles.css" rel="stylesheet" type="text/css" />
    <link href="/static/css/demo.css" rel="stylesheet" type="text/css" />
    <link href="/static/css/loaders.css" rel="stylesheet" type="text/css" />

	<script type="text/javascript" src="/static/js/jquery.min.js"></script>
	<script type="text/javascript" src="/static/js/jquery-ui.min.js"></script>

```
- 当然路由urls.py的配置也应该做出相对的更改，业务逻辑提交的网页也当做出更改。

---
## 文件上传
### 部署流程
- 在项目的环境中安装依赖包
```shell
pip install Pillow
```
- 配置setting.py文件
```python
# 配置上传文件路径
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

```
- 配置路由urls.py

```python
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
- 定义模型
在对应的模型中添加以下属性
```python
i_image = models.ImageField(upload_to='upload', null=True)
```
- 页面请求端
```html
头像：<input type="file" name="img">
```
- 业务逻辑处理
```python
    if request.method == 'POST':
        stu_id = request.POST.get('stu_id')
        addr = request.POST.get('addr')

        img = request.FILES.get('img')

        StudentInfo.objects.create(i_addr=addr, s_id=stu_id, i_image=img)

        return HttpResponseRedirect('/stu/index/')
```
- 页面返回信息
```html
头像：
    {% if stuinfo.i_image %}
    <img src="/media/{{ stuinfo.i_image }}" width="100" height="100">
```
