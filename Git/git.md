# Git的使用说明

>Auther:sinpei 
>Email:sinpeilee@163.com 
>Date: 2018-4-20

---

## Git是什么
```
一个开源的分布式版本控制系统，可以有效、高速的处理从很小到非常大的项目版本管理。Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。
分布式相比于集中式的最大区别在于开发者可以提交到本地，每个开发者通过克隆（git clone），在本地机器上拷贝一个完整的Git仓库。
```
## Git的优势
- 适合[分布式开发](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E5%BC%80%E5%8F%91)，强调个体。
- 公共服务器压力和数据量都不会太大。
- 速度快、灵活。
- 任意两个开发者之间可以很容易的解决冲突。
- 离线工作。
###使用Git开发结构
```
master(线上分支)，dev(开发环境分支)，test(测试分支)，sinpei(自己开发分支)
```
##Git的安装
前往[官网](https://git-scm.com/downloads)下载与个人系统匹配的git版本。
##Git的操作流程
![流程图](https://upload-images.jianshu.io/upload_images/10903485-9569cc93e3fcfe47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- Remote:远端仓库
- Repository:本地仓库
- Index:缓存区
- workspace:工作区
#### 1. 创建代码仓库
- 在当前目录新建一个Git代码库
```shell
$ git init
```
- 新建一个目录，将其初始化为Git代码库
```shell
$ git init [project-name]
```
- 下载一个项目和它的整个代码历史
```shell
$ git clone [url]
```
#### 2.配置
- 显示配置
```shell
$ git config list
```
- 编辑配置
```shell
 $ git config -e --[global]
```
设置提交代码时的用户信息可以用上面命令直接打开文件设置，也可以在命令行中直接输入下面命令
```shell
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```
- 建立ssh秘钥，避免每次输入账户名密码的繁琐
去到用户目录下的.ssh文件中，没有mkdir一个就可以。输入以下指令
```shell
$ ssh-keygen -t rsa -C [account]  
```
      note:account指你的GitHub账号
在.ssh目录中cat id_rsa.pub, 复制公钥去GitHub上设置ssh秘钥。![GitHub设置ssh秘钥](https://upload-images.jianshu.io/upload_images/10903485-6160372e8f24cec0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![GitHub设置ssh秘钥](https://upload-images.jianshu.io/upload_images/10903485-b9afa127f5336407.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
测试链接默认回车进入，不设置使用私钥的密码，直接登录。

#### 3.增加/删除/提交文件
-  添加指定文件/所有文件到暂存区
```shell
$ git add [filename]/.
``` 

- 删除工作区文件，并且将这次删除放入暂存区
```shell
$ git rm [file1] [file2] ...
```
- 提交文件的注释
```shell
$ git commit -m '注释'
```
- 合并添加上传文件和添加上传文件注解到本地分支的操作
```shell
$ git commit -am '注解'
```
#### 4.分支

- 创建分支并留在当前分支
```shell
$ git branch [branch_name]
```
- 切换到指定分支，并更新工作区
```shell
$ git checkout [branch_name]
```
- 删除本地指定分支
```shell
$ git branch -d [branch_name]
```
- 删除远程分支
```shell
$ git push origin --delete [branch_name]
```
- 查询分支区别
```shell
$ git diff [branch1] [branch2]
```
#### 5. 远程同步
- 取回远程仓库的变化，并与本地分支合并
```shell
$ git pull origin [branch]
```
- 上传本地指定分支到远程仓库
```shell
$ git push origin [branch]
```
#### 6. 版本信息
- 代码合并提交

 1. 先切换版本到dev分支
```shell
$ git checkout dev
```
 2. 当前dev分支在合并个人分支
```shell
$ git merge sinpei
```
  3. 提交dev分支合并的代码到远程dev分支上
```shell
$ git push origin dev
```

- 上线代码需要打tag，在master分支打tag。 打版本
```shell
$ git tag -a [版本号] -m '注解'
```

- 提交版本
```shell
$ git push origin [版本号]
```
- 删除本地/远程tag 
```shell
$ git tag -d [版本号] / git push origin --delete tag [版本号]
```

#### 7.查看信息
- 查看本地/远程分支
```shell
$ git branch / git branch -f
```
- 显示有变更的文件
```shell
$ git status
```
- 显示当前分支的提交的日志
```shell
$ git log
```
- 查看版本
```shell
$ git tag
```
#### 8.缓存机制。
在某一个分支修改了代码，但是不想提交该分支，又想切换到另外一个分支在修改相同的代码，就需要使用stash命令
- 缓存本地修改的代码
```shell
$ git stash
```
> 缓存之后，在git status去查看修改代码记录会发现提示 nothing to commit，working tree clean。说明刚才修改的代码都缓存起来了

- 查看缓存的片段
```shell
$ git stash list
```
> 发现有缓存列表，刚才缓存的记录为 stash@{0}: XXXXXXXXXX

- 还原缓存的代码
```shell
$ git stash apply stash@{0}
```
