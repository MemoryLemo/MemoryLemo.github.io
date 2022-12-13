---
title: "ZSH"
date: 2022-12-13T23:05:16+08:00
draft: true
---

# ZSH

## zsh介绍

zsh 是一个兼容 bash 的 shell，相较 bash 具有以下优点：

- Tab 补全功能强大。命令、命令参数、文件路径均可以补全。
- 插件丰富。快速输入以前使用过的命令、快速跳转文件夹、显示系统负载这些都可以通过插件实现。
- 主题丰富。
- 可定制性高。
- Zsh 兼容 Bash，据传说 99% 的 Bash 操作 和 Zsh 是相同的
- Zsh 官网：http://www.zsh.org/

### 有关shell

Shell是Linux/Unix的一个外壳，你理解成衣服也行。它负责外界与Linux内核的交互，接收用户或其他应用程序的命令，然后把这些命令转化成内核能理解的语言，传给内核，内核是真正干活的，干完之后再把结果返回用户或应用程序。

Linux/Unix提供了很多种Shell，写程序比买衣服复杂多了，而且程序员往往负责把复杂的事情搞简单，简单的事情搞复杂。牛程序员看到不爽的Shell，就会自己重新写一套，慢慢形成了一些标准，常用的Shell有这么几种，sh、bash、csh等



## 安装zsh

```shell
cat /etc/shells 			  #查看你的系统有几种shell

#安装zsh
sudo yum install zsh		   #如果你用 Redhat Linux
sudo apt-get install zsh 	   #如果你用 Ubuntu Linux

#切换shell
chsh -s /bin/zsh 			  #安装完成后设置当前用户使用，根据提示输入当前用户的密码就可以了
chsh -s /bin/bash              #切回bash界面
```



## 安装oh-my-zsh

- oh-my-zsh 帮我们整理了一些常用的 Zsh 扩展功能和主题：https://github.com/robbyrussell/oh-my-zsh
- 我们无需自己去捣搞 Zsh，直接用 oh-my-zsh 就足够了，如果你想继续深造的话那再去弄。
- 先安装 git：`sudo yum install -y git`
- 安装 oh-my-zsh

```shell
#wget自动下载 
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
或者
wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
或者
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

#curl自动下载
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

#手动下载
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```



## 修改主题

在 [https://github.com/ohmyzsh/ohmyzsh/wiki/Themes](https://link.zhihu.com/?target=https%3A//github.com/ohmyzsh/ohmyzsh/wiki/Themes) 中查看内置的主题样式和对应的主题名。这些内置主题已经放在 `～/.oh-my-zsh/themes` 目录下，不需要再下载。



## 配置zsh

使用 vim 编辑 ~/.zshrc，键入以下内容并保存：

```shell
#以下是基本配置
alias cls='clear'
alias ll='ls -l'
alias la='ls -a'
alias vi='vim'
alias javac="javac -J-Dfile.encoding=utf8"
alias grep="grep --color=auto"
alias -s html=mate   # 在命令行直接输入后缀为 html 的文件名，会在 TextMate 中打开
alias -s rb=mate     # 在命令行直接输入 ruby 文件，会在 TextMate 中打开
alias -s py=vi       # 在命令行直接输入 python 文件，会用 vim 中打开，以下类似
alias -s js=vi
alias -s c=vi
alias -s java=vi
alias -s txt=vi
alias -s gz='tar -xzvf'
alias -s tgz='tar -xzvf'
alias -s zip='unzip'
alias -s bz2='tar -xjvf'
```

最后，执行 `source ~/.zshrc` 配置生效



## 安装zsh插件

oh my zsh 项目提供了完善的插件体系，相关的文件在~/.oh-my-zsh/plugins目录下，默认提供了100多种，大家可以根据自己的实际学习和工作环境采用，想了解每个插件的功能，只要打开相关目录下的 zsh 文件看一下就知道了。插件也是在.zshrc里配置，找到plugins关键字，你就可以加载自己的插件了，系统默认加载 git ，你可以在后面追加内容oh-my-zsh 已经内置了 git 插件

### 插件推荐：

1、git：当你处于一个 git 受控的目录下时，Shell 会明确显示 「git」和 branch，如上图所示，另外对 git 很多命令进行了简化，例如 gco=’git checkout’、gd=’git diff’、gst=’git status’、g=’git’等等，熟练使用可以大大减少 git 的命令长度，命令内容可以参考~/.oh-my-zsh/plugins/git/git.plugin.zsh

2、textmate：mr可以创建 ruby 的框架项目，tm finename 可以用 textmate 打开指定文件。

3、osx：tab 增强，quick-look filename 可以直接预览文件，man-preview grep 可以生成 grep手册 的pdf 版本等。

### 插件配置实操：

- ```
  wd
  ```

  - 简单地讲就是给指定目录映射一个全局的名字，以后方便直接跳转到这个目录，比如：
  - 编辑配置文件，添加上 wd 的名字：`vim /root/.zshrc`
  - 我常去目录：/opt/setups，每次进入该目录下都需要这样：`cd /opt/setups`
  - 现在用 wd 给他映射一个快捷方式：`cd /opt/setups ; wd add setups`
  - 以后我在任何目录下只要运行：`wd setups` 就自动跑到 /opt/setups 目录下了
  - 插件官网：https://github.com/mfaerevaag/wd

  

- ```
  autojump
  ```

  - 这个插件会记录你常去的那些目录，然后做一下权重记录，你可以用这个命令看到你的习惯：`j --stat`，如果这个里面有你的记录，那你就只要敲最后一个文件夹名字即可进入，比如：`j program`，就可以直接到：`/usr/program`
  - 插件官网：https://github.com/wting/autojump
  - 官网插件下载地址：https://github.com/wting/autojump/downloads
  - 插件下载：`wget https://github.com/downloads/wting/autojump/autojump_v21.1.2.tar.gz`
  - 解压：`tar zxvf autojump_v21.1.2.tar.gz`
  - 进入解压后目录并安装：`cd autojump_v21.1.2/ ; ./install.sh`
  - 再执行下这个：`source /etc/profile.d/autojump.sh`
  - 编辑配置文件，添加上 autojump 的名字：`vim /root/.zshrc`

  

- ```
  zsh-syntax-highlighting
  ```

  - 这个插件会对终端命令高亮显示,比如正确的拼写会是绿色标识,否则是红色,另外对于一些shell输出语句也会有高亮显示,算是不错的辅助插件

  - 插件官网：https://github.com/zsh-users/zsh-syntax-highlighting

  - 安装，复制该命令：'git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting'

  - 编辑：`vim ~/.zshrc`，找到这一行，后括号里面的后面添加：`plugins=( 前面的一些插件名称 zsh-syntax-highlighting)`

  - 刷新下配置：`source ~/.zshrc`

    

  ## autojump

  zsh 和 autojump 的组合形成了 zsh 下最强悍的插件

  

  ### autojump是什么？

  autojump是一个[命令行](https://so.csdn.net/so/search?q=命令行&spm=1001.2101.3001.7020)工具，它允许你可以直接跳转到你喜爱的目录，而不受当前所在目录的限制。意思就是可以让你更快地切换目录，而不用频繁地使用`cd` `tab` 等命令。

  

  ### autojump工作原理

  它会在你每次启动命令时记录你当前位置，并把它添加进它自身的数据库中。这样，某些目录比其它一些目录添加的次数多，这些目录一般就代表你最重要的目录，而它们的“权重”也会增大。

  

  ### 基本命令

  ```shell
  #目录跳转
  
  j [目录的名字或名字的一部分]  ##不受当前所在目录的限制
  
  #查看当前权重
  j --stat	
  
  #进入权重最高的目录
  j			
  
  #改变权重值
  j -i [权重] ## 增加
  j -d [权重] ## 减少
  ```

  

  ## 使用zsh

  1、兼容 bash，原来使用 bash 的切换过来毫无压力。

  2、强大的历史纪录功能，输入 grep 然后用上下箭头可以翻阅你执行的所有 grep 命令。

  3、智能拼写纠正，输入gtep mactalk * -R，系统会提示：zsh: correct ‘gtep’ to ‘grep’ [nyae]? 比妹纸贴心吧，她们向来都是让你猜的……

  4、各种补全：路径补全、命令补全，命令参数补全，插件内容补全等等。触发补全只需要按一下或两下 tab 键，补全项可以使用 ctrl+n/p/f/b上下左右切换。比如你想杀掉 java 的进程，只需要输入 kill java + tab键，如果只有一个 java 进程，zsh 会自动替换为进程的 pid，如果有多个则会出现选择项供你选择。ssh + 空格 + 两个tab键，zsh会列出所有访问过的主机和用户名进行补全

  5、智能跳转，安装了autojump之后，zsh 会自动记录你访问过的目录，通过 j + 目录名 可以直接进行目录跳转，而且目录名支持模糊匹配和自动补全，例如你访问过hadoop-1.0.0目录，输入j hado 即可正确跳转。j –stat 可以看你的历史路径库。

  6、目录浏览和跳转：输入 d，即可列出你在这个会话里访问的目录列表，输入列表前的序号，即可直接跳转。

  7、在当前目录下输入 .. 或 … ，或直接输入当前目录名都可以跳转，你甚至不再需要输入 cd 命令了。

  8、通配符搜索：ls -l **/*.sh，可以递归显示当前目录下的 shell 文件，文件少时可以代替 find，文件太多就歇菜了。

  

## 补充事项

### Home / End 失灵问题

- Home = Ctrl + a
- End = Ctrl + e
