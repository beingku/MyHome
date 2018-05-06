# cookie使用说明

>Author:Sinpei 
>Date: 2018-5-6
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 
>期待大神拍砖，轻拍……怕疼



---


## 前言
>HTTP协议 是短连接、且状态的，所以在客户端向服务端发起请求后，服务端在响应头 加入cokie响应给浏览器，以此记录客户端状态；

>cook是来自服务端，保存在浏览器的键值对，主要应用于用户登录；

>cookie如此重要！！那么如何在Django应用cookie呢？cookie又有什么缺陷呢?

## Django应用cookie
###参数介绍
```
1、max_age=1 ：cookie生效的时间，单位是秒

2、expires:具体过期日期  

3、path='/'：指定那个url可以访问到cookie；‘/’是所有； path='/'

4、domain=None（None代表当前域名）：指定那个域名以及它下面的二级域名（子域名）可以访问这个cookie

5、secure=False：https安全相关

6、httponly=False：限制只能通过http传输，JS无法在传输中获取和修改

 ```

### 设置cookie
```
1.普通

obj.set_cookie("tile","zhanggen",expires=value,path='/' )

2.加盐

普通cookie是明文传输的，可以直接在客户端直接打开，所以需要加盐，解盐之后才能查看

obj.set_signed_cookie('k','v',salt="zhangge")
```
 

### 获取cookie
```
1、普通

obj.set_signed_cookie('k','v',salt="zhangge")

2、加盐

cookies=request.get_signed_cookie('k',salt='zhanggen')
```

---

## cookie+session

### cookie引入session：

cookie看似解决了HTTP（短连接、无状态）的会话保持问题，但把全部用户数据保存在客户端，存在安全隐患，

于是cookie+session出现了！我们可以 把关于用户的数据保存在服务端，在客户端cookie里加一个sessionID（随机字符串），

基于以上原因：cook+session组合就此作古了单单使用cookie做会话保持的方式；

### cookie+session的工作流程：

(1)、当用户来访问服务端时,服务端生成一个随机字符串；

(2)、当用户登录成功后 把 {sessionID :随机字符串} 组织成键值对 加到 cookie里发送给用户；

(3)、服务器以发送给客户端 cookie中的随机字符串做键，用户信息做值，保存用户信息；
 