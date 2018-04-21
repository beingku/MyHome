## 目录

* [Python是什么](#Python是什么)
* [Python能做什么](#python能做什么)
* [为什么选择Python](#为什么选择python)
* [Python的安装](#python的安装)
* [小结](#小结)

### Python是什么
 
Python是一种面向对象的解释型计算机程序设计语言。由荷兰人Guido van Rossum于1989年发明，第一个公开发行版发行于1991年。
Pythond的设计哲学强调代码的可读性和简洁的语法（以空格缩进划分代码块）。所以阅读Python代码总是清晰明了。

> 与 Scheme、Ruby、Perl、Tcl 等动态类型编程语言一样，Python 拥有动态类型系统和垃圾回收功能，能够自动管理内存使用，并且支持多种编程范式，包括面向对象、命令式、函数式和过程式编程。其本身拥有一个巨大而广泛的标准库。     
#### Python的历史
> Python的创始人为Guido van Rossum。1989年的圣诞节期间，Guido为了在阿姆斯特丹打发时间，决心开发一个新的脚本解释程序，作为ABC语言的一种继承。之所以选中Python作为程序的名字，是因为他是BBC电视剧——蒙提·派森的飞行马戏团的爱好者。ABC是由吉多参加设计的一种教学语言。就吉多本人看来，ABC这种语言非常优美和强大，是专门为非专业程序员设计的。但是ABC语言并没有成功，究其原因，吉多认为是非开放造成的。吉多决心在Python中避免这一错误，并获取了非常好的效果，完美结合了C和其他一些语言。
就这样，Python在Guido手中诞生了。实际上，第一个实现是在Mac机上。可以说，Python是从ABC发展起来，主要受到了Modula-3（另一种相当优美且强大的语言，为小型团体所设计的）的影响。并且结合了Unix shell和C的习惯。
目前Guido仍然是Python的主要开发者，决定整个Python语言的发展方向。Python社区经常称呼他是仁慈的独裁者。
Python 2.0于2000年10月16日发布，增加了实现完整的垃圾回收，并且支持Unicode。同时，整个开发过程更加透明，社区对开发进度的影响逐渐扩大。Python 3.0于2008年12月3日发布，此版不完全兼容之前的Python源代码。不过，很多新特性后来也被移植到旧的Python 2.6/2.7版本。

### Python能做什么
- Web程序开发
由于Python对于各种网络协议的支持很完善，因此经常被用于编写服务器软件、网络爬虫。


- GUI开发
Python本身包含的Tkinter库能够支持简单的GUI开发。

- 操作系统
很多操作系统里，Python是标准的系统组件。

- 其他
NumPy、SciPy、Matplotlib可以让Python程序员编写科学计算程序。有些公司会使用Scons代替make构建C++程序。很多游戏使用C++编写图形显示等高性能模块，而使用Python或者Lua编写游戏的逻辑、服务器。

### 为什么选择Python
Python的设计哲学是“优雅”、“明确”、“简单”。Python开发者的哲学是“用一种方法，最好是只有一种方法来做一件事”，也因此它和拥有明显个人风格的其他语言很不一样。
附上Python之禅
```
The Zen of Python
by Tim Peters
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```
### Python的安装
- Windows环境
前往[官网](https://www.python.org/downloads/)选择相应的版本下载（.exe文件），安装前要注意本机是否有Service Pack 1补丁包（win7之后的版本都有，win7打开系统属性查看是否有该补丁包。没有可以windosw更新重要更新中安装)。安装过程建议勾选“Add Python 3.6 to PATH”（将Python 3.6添加到PATH环境变量）并选择自定义安装，在设置“Optional Features”界面最好将“pip”、“tcl/tk”、“Python test suite”等项全部勾选上。强烈建议使用自定义的安装路径并保证路径中没有中文。

- Linux环境
Linux环境自带Python2.x版本。如果要安装3.x版本。

1. 首先安装依赖库

  ```shell
$ yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
  ```

2. 下载源码并解压

  ```shell
$ wget https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tar.xz
$ tar -xzf Python-3.6.5.tar.xz
  ```

3. 切换到Python源代码目录执行下面操作
  ```shell
$ cd Python-3.6.5
$ ./configure --prefix=/usr/local/python3.6 --enable-optimizations
$ make && make install
  ```
4. 创建软链接,可以直接通过python3直接启动Python解释器。
  ```shell
$ ln -s /usr/local/python3.6/bin/python3 /usr/bin/python3
  ```

- MacOS环境
MacOS也是自带了Python 2.x版本的，可以通过Python的官方网站提供的安装文件（pkg文件）安装3.x的版本。默认安装完成后，可以通过在终端执行python命令来启动2.x版本的Python解释器，可以通过执行python3命令来启动3.x版本的Python解释器，当然也可以通过重新设置软链接来修改启动Python解释器的命令。

### 小结
Python的启航篇，后续博客正在整理更新。
Python历史摘抄自维基百科。
最后老规矩，第一个python程序
```python
'''
致敬Dennis M. Ritchie先生
'''
print ('hello,world!')
```

