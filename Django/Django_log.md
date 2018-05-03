# Django日志使用说明

>Author: Sinpei 
>Date: 2018-5-3
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 

---

### 前言
Django日志是记录开发过程中的信息记录。方便运维，开发人员的维护，开发工作。

### 日志logging模块
> logging模块可以收集记录错误，警告等调试信息，在程序中可以捕获这些信息，并且甚至可以将错误的重要信息等都可以通过邮件发送给开发者
> 日志logging四部分组成:
- formatters: 格式化，需要保存到日志文件中的信息进行统一格式化
- loggers: 用来处理传入信息
- handlers: 用来处理信息的
- filters: 过滤loggers传递给handlers的信息，加一些处理控制

#### formatters
日志记录需要转换成文本。
Formatter 表示文本的格式。Fomatter 通常由包含日志记录属性的Python 格式字符串组成；
你也可以编写自定义的fomatter 来实现自己的格式。
如下展示了formatters格式: 

![formatters数据格式](https://upload-images.jianshu.io/upload_images/10903485-271532076821dff9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### loggers
Logger 为日志系统的入口。每个logger 是一个具名的容器，可以向它写入需要处理的消息。
每个logger 都有一个日志级别。日志级别表示该logger 将要处理的消息的严重性。
Python 定义以下几种日志级别：
```
DEBUG：用于调试目的的底层系统信息

INFO：普通的系统信息

WARNING：表示出现一个较小的问题。

ERROR：表示出现一个较大的问题。

CRITICAL：表示出现一个致命的问题。

日志级别等级CRITICAL > ERROR > WARNING > INFO > DEBUG > NOTSET
```

#### handlers
Handler 决定如何处理logger 中的每条消息。它表示一个特定的日志行为。
与logger 一样，handler 也有一个日志级别。如果消息的日志级别小于handler 的级别，handler 将忽略该消息。
Logger 可以有多个handler，而每个handler 可以有不同的日志级别。

#### Filters
Filter 用于对从logger 传递给handler 的日志记录进行额外的控制。

### 部署日志
在项目的setting.py文件中添加下面代码
```python
# 创建log的路径
LOG_PATH = os.path.join(BASE_DIR, 'log')
# 如果地址不存在，自动创建log文件夹
if not os.path.isdir(LOG_PATH):
    os.mkdir(LOG_PATH)

LOGGING = {
    'version': 1,
    # Ture表示禁用loggers
    'disable_existing_loggers': False,

    'formatters': {
        'default': {
            'format': '%(levelname)s %(pathname)s %(funcName)s %(module)s %(created)s %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(module)s %(created)s %(message)s'
        }
    },
    'handlers': {
        'stu_handlers': {
            'level': 'DEBUG',
            # 日志文件指定为5M，超过5M重新备份，然后写入新的日志文件
            'class': 'logging.handlers.RotatingFileHandler',
            'maxBytes': 5 * 1024 * 1024,
            # 文件地址
            'filename': '%s/log.txt' % LOG_PATH,
            'formatter': 'default'
        },
        'uauth_handlers': {
            'level': 'DEBUG',
            # 日志文件指定为5M，超过5M重新备份，然后写入新的日志文件
            'class': 'logging.handlers.RotatingFileHandler',
            'maxBytes': 5 * 1024 * 1024,
            # 文件地址
            'filename': '%s/log.txt' % LOG_PATH,
            'formatter': 'simple'
        }
    },
    'loggers': {
        'stu': {
            'handlers': ['stu_handlers'],
            'level': 'INFO',
        },
        'autu': {
            'handlers': ['uauth_handlers'],
            'level': 'INFO'
        }
    },
    'filters': {

    }
}

```
