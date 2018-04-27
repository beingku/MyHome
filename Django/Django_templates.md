# Django的模版使用说明

>Author: Sinpei 
>Date: 2018-4-26
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 

---

### 前言
> Django框架是典型的MTV框架，其中T就是本文的主题——模版。模版能快速帮助开发者建立前端页面。T通过加载，渲染两个过程的处理，实现V的业务逻辑，完成与用户的交互。

### 配置templates和static
Django的HTML页面文件主要放在templates文件夹中，先在jango项目中创建文件夹templates，再找到根目录settings.py中找到TEMPLATES的DIRS添加 os.path.join(BASE_DIR, 'templates'。

![templates配置](https://upload-images.jianshu.io/upload_images/10903485-07e177f2a4b80b23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在Django项目的根目录settings.py中最底下有一个叫做static的文件夹，主要用来加载一些模板中用到的资源，提供给全局使用。这个静态文件主要用来配置css，html，图片，文字文件等。

![static配置](https://upload-images.jianshu.io/upload_images/10903485-bbf86bfa57759acc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

只后在模板中，首先加载静态文件，之后调用静态，就不用写绝对全路径了。

----
### 调用static
a) 加载渲染静态配置文件 模板中声明
```
{% load static %} 或者 {% load staticfiles %}
```
在引用资源的时候使用
```
{% static ‘xxx’ %} xxx就是相当于staticfiles_dirs的一个位置
```
b) 直接定义静态配置
```
<img src="/static/images/mvc.png">
```
其中: 展示static文件夹下有一个images文件夹，下面有一个mvc.png的图片

### 模版小结

- 模版主要由2部分构成，HTML代码和动态插入的代码块（block）
- 动态填充，模板中的动态代码断除了做基本的静态填充，还可以实现一些基本的运算。
> note:模板中的变量： 视图传递给模板的数据 标准标识符规则 语法 {{ var }} 如果变量不存在，则插入空字符串。

- 常用形式
```
{{ stu.stu_name }}
```

- 不能传递参数
- 常用语法结构
```
if 语句
    {% if表达式 %}

    {% elif 表达式 %}

    {%  endif %}
    
```
```
for 语句
    {% for 变量 in 列表 %}

    {% empty %}

    {% endfor %}
当列表为空或者不存在时执行empty语句
```
```
注释
注释可见，可运行
<!-- 注释内容 -->

单行注释注释不可见，不可运行
{# 被注释掉的内容 #}

多行注释注释不可见，不可运行
{% comment %}

{% endcomment %}

```

- 过滤器
引入过滤器定义：{{var|过滤器}}， 作用：在变量显示前修改 过滤器有很多，比如add / lower / upper
  - 加法
   ```
   {{ p.page | add:5 }}
    没有减法的过滤器，但是加法里面可以加负数
    {{ p.page | add: -5 }}
   ```
  - 修改大小写
  ```
  lower / upper : {{ p.pname | lower }} 变为小写或者大写
  ```
  - 时间
  ```
   {{ stu.stu_birth | date:'y-m-d' }}
   {{ stu.stu_birth | date:'Y-M-D' }}
   {{ stu.stu_create_time | date:'Y-m-d h:m:s' }}
  ```
  - 乘法
  ```
  {% widthratio variable 分母 分子 %}
  ```
  - 整除
  ```
  {% divisibleby:value %}   整除value，返回True/False
  ```
 
  - forloop.counter是用来计数的
  ```
  <p>计数一（从0开始升序）：{{ forloop.counter0 }}</p >
  <p>计数二（从1开始升序）：{{ forloop.counter }}</p >
  <p>计数三（降序到0）：{{ forloop.revcounter0 }}</p >
  <p>计数四（降序到1）：{{ forloop.revcounter }}</p >
  ```
- 模版继承
  - 关键字 block挖坑 
```
{% block xxx %}
{% endblock %}
```
  - extends 继承，写在开头位置
```
{% extends ‘父模板路径’%}
```
  - include 加载模型进行渲染
```
{% include ‘模板文件’%}
```