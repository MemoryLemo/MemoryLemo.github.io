---
title: "Git"
date: 2022-12-13T23:05:20+08:00
draft: true
---

# Git

## 一、Git是什么？

------

Git是目前世界上最先进的[分布式](https://so.csdn.net/so/search?q=分布式&spm=1001.2101.3001.7020)版本控制系统。

Git是一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。其可运行于Linux、Mac OS、Windows平台。Git已经成为各大公司管理项目版本的首选！

       Git可以使用简单直观的GUI图形化界面进行使用，但并不是所有平台和场景都可以使用Git的图形化界面。更多情况下，我们都是使用Git的命令行进行操作，那么本文讲解一下如何使用Git命令行进行相关操作。



完整的Git框架有四部分，分别是工作目录（Working Directory）、暂存区(Stage/Index)、资源库(Repository或Git Directory)、远程仓库(Remote Directory)。我们先来看一下这几个概念：

工作目录（Workspace）：所有版本的目录。

暂存区（Index / Stage）：修改文件后，这里临时保存着你当前的修改。

资源库（Repository）：也就是本地仓库！你确定自己修改的文件后，可以将目前确定修改的文件放入这个本地仓库。

远程仓库（Remote）：网络端的仓库，你可以把Git的项目文件放在指定网络平台保管或者开源。

       上述就是一些概念性的东西，可能初学者不好理解。在这里读者画一张图来表述这个框架的关系以及Git命令之间的关系！这幅框架图十分重要，最常使用的命令几乎都在图中标识出来了，希望大家好好理解一下！如下图所示。

![工作原理 / 流程：](https://img-blog.csdnimg.cn/img_convert/cf32fcc41799d64541cb6c9b5f9373a2.png#pic_center)



## 二、SVN与Git的最主要的区别？

------

SVN是集中式版本控制系统，版本库是集中放在中央服务器的，而干活的时候，用的都是自己的电脑，所以首先要从中央服务器哪里得到最新的版本，然后干活，干完后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，如果在局域网还可以，带宽够大，速度够快，如果在互联网下，如果网速慢的话，就纳闷了。

 Git是分布式版本控制系统，那么它就没有中央服务器的，每个人的电脑就是一个完整的版本库，这样，工作的时候就不需要联网了，因为版本都是在自己的电脑上。既然每个人的电脑都有一个完整的版本库，那多个人如何协作呢？比如说<u>自己在电脑上改了文件A，其他人也在电脑上改了文件A，这时，你们两之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。</u>

用好 Git 很难: 很容易就犯错了，然后想自己弥补犯下的错，简直太难了。查阅 Git 文档简直就像是个 “鸡生蛋 蛋生鸡” 的问题。



## 三、Git如何操作

### 创建版本库

什么是版本库？版本库又名仓库，英文名repository,你可以简单的理解一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改，删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻还可以将文件”还原”。

（1）在空白处右键，点击“Git Bash Here”打开Git命令行窗口

下面这个用户名就写github的用户名，邮箱地址就写github的邮箱地址

![在这里插入图片描述](https://img-blog.csdnimg.cn/2297bcfaaf914f83aa70231f4134c9dd.png#pic_center)

（2）创建仓库

使用git去管理项目，可以使用一个空目录学习git管理

**tips：目录路径不要有中文！**

![在这里插入图片描述](https://img-blog.csdnimg.cn/338eae3d7e614e4392337d4d65216a7e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p6X5p-a5pme,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

**注意：这个目录执行了初始化操作，会在根目录这边生成一个.git的隐藏目录，这个目录是git所创建，不能删除和更改！**



## 四、Git分支管理

在公司进行开发的时候，是多人协同开发，每个人互不干扰，所以git也要进行分支管理



## 五、Git常用命令

### 初始化操作

```shell
$ git config -global user.name <name> #设置提交者名字
$ git config -global user.email <email> #设置提交者邮箱
$ git config -global core.editor <editor> #设置默认文本编辑器
$ git config -global merge.tool <tool> #设置解决合并冲突时差异分析工具
$ git config -list #检查已有的配置信息
```



### 创建新版本库

```shell
$ git clone <url> #克隆远程版本库
$ git init #初始化本地版本库
```



### 修改和提交

```shell
$ git add . #添加所有改动过的文件
$ git add <file> #添加指定的文件
$ git mv <old> <new> #文件重命名
$ git rm <file> #删除文件
$ git rm -cached <file> #停止跟踪文件但不删除
$ git commit -m <file> #提交指定文件
$ git commit -m “commit message” #提交所有更新过的文件
$ git commit -amend #修改最后一次提交
$ git commit -C HEAD -a -amend #增补提交（不会产生新的提交历史纪录）
```



### 查看提交历史

```shell
$ git log #查看提交历史
$ git log -p <file> #查看指定文件的提交历史
$ git blame <file> #以列表方式查看指定文件的提交历史
$ gitk #查看当前分支历史纪录
$ gitk <branch> #查看某分支历史纪录
$ gitk --all #查看所有分支历史纪录
$ git branch -v #每个分支最后的提交
$ git status #查看当前状态
$ git diff #查看变更内容
```



### 撤消操作

```shell
$ git reset -hard HEAD #撤消工作目录中所有未提交文件的修改内容
$ git checkout HEAD <file1> <file2> #撤消指定的未提交文件的修改内容
$ git checkout HEAD. #撤消所有文件
$ git revert <commit> #撤消指定的提交
```



### 分支与标签

```shell
$ git branch #显示所有本地分支
$ git checkout <branch/tagname> #切换到指定分支或标签
$ git branch <new-branch> #创建新分支
$ git branch -d <branch> #删除本地分支
$ git tag #列出所有本地标签
$ git tag <tagname> #基于最新提交创建标签
$ git tag -d <tagname> #删除标签
```



### 合并与衍合

```shell
$ git merge <branch> #合并指定分支到当前分支
$ git rebase <branch> #衍合指定分支到当前分支
```



### 远程操作

```shell
$ git remote -v #查看远程版本库信息
$ git remote show <remote> #查看指定远程版本库信息
$ git remote add <remote> <url> #添加远程版本库
$ git fetch <remote> #从远程库获取代码
$ git pull <remote> <branch> #下载代码及快速合并
$ git push <remote> <branch> #上传代码及快速合并
$ git push <remote> : <branch>/<tagname> #删除远程分支或标签
$ git push -tags #上传所有标签
```
