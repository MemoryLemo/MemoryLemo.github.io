---
title: "Docker"
date: 2022-12-13T23:02:39+08:00
draft: false
---

## Docker基本命令

```shell
#帮助命令
docker version   # 查看 docker版本号 
docker info    # 显示docker的系统信息
docker 命令 --help    # 显示帮助命令

#搜索镜像命令
docker search    # 在 docker hub 中搜索镜像
docker search    #在命令行中搜索镜像
--filter=STARS=镜像大小 #搜索镜像 限制大小因素

#查看镜像
docker images -a # 查看所有镜像信息
docker images -aq # 查看所有的镜像id

#查看容器
docker ps        # 查看正在运行中的容器
docker ps -a     # 查看所有容器

#拉取镜像
docker pull      	# 从docker镜像源服务器拉取指定镜像或者库镜像
docker pull:版本号   # 从docker镜像源服务器拉取指定版本下载

docker push      # 推送指定镜像或者库镜像至docker源服务器

docker restart   # 重启运行的容器

#删除镜像
docker rm        # 移除一个或多个容器
docker rmi       # 移除一个或多个镜像（无容器使用该镜像才可删除，否则需要删除相关容器才可继续或 -f 强制删除）
docker rmi 镜像:版本号     #删除指定名称的镜像
docker rmi -f $(docker images  -aq)  #迭代删除所有的镜像

#运行镜像
docker run  镜像名     # 使用镜像创建一个新的容器并运行一个命令
docker run --name=mycat -d -p 8080:8080 tomcat  # 用完即删
docker run -it --rm tomcat   # 指定环境变量（实例）
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m"  elasticsearch:7.6.2

--name="Name"：容器名字 tomacat01、tomacat02区分容器

-i:以交互模式运行容器，通常与 -t 同时使用；

-d:后台运行容器，并返回容器ID；

-t:为容器重新分配一个伪输入终端，通常与 -i 同时使用；

-p：指定容器的端口    eg：-p 8080:8080

-e：指定环境变量

-P：随机指定端口

-v:  指定数据卷       eg：-v 主机文件位置:容器文件位置

--volumes-from: 指定容器的数据卷共享（指定谁，就同步谁的数据！继承！）

--privileged：容器将拥有访问主机所有设备的权限

--net: 缺省 bridge

docker save      # 保存一个镜像为一个 tar 包【对应 load】

docker load      # 导入使用docker save 命令导出的镜像 

--input , -i : 指定导入的文件，代替 STDIN。

--quiet , -q : 精简输出信息。

docker port  	  # 查看映射端口对应的容器内部源端口

#启动容器 暂停容器 停止容器
docker start 容器名/容器id    # 启动容器
docker restart 容器名/容器id  # 重启容器
docker kill 容器名/容器id     #强制杀死容器
docker stop 容器名/容器id     # 停止容器
docker pause 容器名/容器id	 # 暂停容器
docker unpause   # 取消暂停容器

#删除容器
docker rm 容器id 	   			# 删除指定容器
docker rm -f 容器id  	  	     # 强制删除运行中的容器
docker rm -f $(docker ps -aq)  # 迭代删除全部的容器

#退出容器
exit   # 直接容器停止并退出
Ctrl + P + Q  # 容器退出不停止

#进入当前正在运行中的容器
docker exec -it 容器名/容器id /bin/bash # 进入到指定容器内部进行修改  开启一个新的终端
docker attach 容器id 					# 进入到正在执行中的终端

#将文件从容器拷贝到宿主机上
docker cp 容器id:容器内文件的路径 宿主机路径
#实例
docker cp 0cd4d9d94de2:/Test.java /Test.java

#其他常用指令
docker logs -ft --tail 10 容器id # 查看容器运行产生的日志
docker tag       # 给源中镜像打标签
docker top       # 查看容器中运行的进程信息
docker inspect 容器id # 查看容器元信息
docker wait      # 截取容器停止时的退出状态值
docker stats     #查看cpu的状态
```



#### 端口暴露概念

![image-20221202101030377](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221202101030377.png)



```shell
#进入容器——实例演示
[root@localhost ~]# docker exec -it nginx01 /bin/bash
root@e5e0a0665832:/# ls
bin   dev                  docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc                   lib   media  opt  root  sbin  sys  usr
root@e5e0a0665832:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@e5e0a0665832:/# cd /etc/nginx/
root@e5e0a0665832:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params
```



![image-20221203092638988](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221203092638988.png)



#### 可视化

```shell
#Portainer （先用这个）
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer   #可通过外网进行访问测试：http://ip:8088/ 来访问

什么是Portainer？
Docker图形化界面管理工具！提供后台面板可供我们操作！

#Rancher（CI/CD再用）
```



![image-20221203102652427](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221203102652427.png)



## Docker镜像讲解

```shell
#什么是镜像？
镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、配置文件。

所有的应用，直接打包docker镜像，不需要配置环境，直接就可以跑起来！

如何得到镜像？
1.从远程仓库下载
2.朋友拷贝给你
3.自己制作一个镜像 DockerFile

#镜像加载原理
UnionFS
联合文件系统（Union File System）：2004年由纽约州立大学石溪分校开发，它可以把多个目录(也叫分支)内容联合挂载到同一个目录下，而目录的物理位置是分开的。UnionFS允许只读和可读写目录并存，就是说可同时删除和增加内容。UnionFS应用的地方很多，比如在多个磁盘分区上合并不同文件系统的主目录，或把几张CD光盘合并成一个统一的光盘目录(归档)。另外，具有写时复制(copy-on-write)功能UnionFS可以把只读和可读写文件系统合并在一起，虚拟上允许只读文件系统的修改可以保存到可写文件系统当中。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层文件和目录。

#Docker镜像加载原理
Docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS（联合文件系统）。

分为两个部分：

bootfs（boot file system）：主要包含bootloader和kernel（Linux内核），bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，而在Docker镜像的最底层也是bootfs这一层，这与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后，整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

即：系统启动时需要的引导加载，这个过程会需要一定时间。就是黑屏到开机之间的这么一个过程。电脑、虚拟机、Docker容器启动都需要的过程。在说回镜像，所以这一部分，无论是什么镜像都是公用的。

rootfs（root file system）：rootfs在bootfs之上。包含的就是典型Linux系统中的/dev，/proc，/bin，/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

即：镜像启动之后的一个小的底层系统，这就是我们之前所说的，容器就是一个小的虚拟机环境，比如Ubuntu，Centos等，这个小的虚拟机环境就相当于rootfs。
```

![img](https://img-blog.csdnimg.cn/0ea8e92d4a7f476290e7ae335f21f53b.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plcnJ5X2xpdWZlbmc=,size_16,color_FFFFFF,t_70)

```
平时我们安装进虚拟机的CentOS系统都是好几个G，为什么Docker这里才200M？

对于一个精简的OS系统，rootfs可以很小，只需要包含最基本的命令、工具和程序库就可以了，因为底层直接用Host（宿主机）的kernel（也就是宿主机或者服务器的boosfs+内核），自己只需要提供rootfs就可以了。

由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs部分。
```

**这就是我们之前说：虚拟机的启动是分钟级的，容器的启动是秒级的**



#### 分层理解

```
所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

举一个简单的例子，假如基于Ubuntu Linux 16.04创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。

该镜像当前已经包含3个镜像层，如下图所示（这只是一个用于演示的很简单的例子）。
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/27d85a0ebd7b42368f41bb578babbcca.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plcnJ5X2xpdWZlbmc=,size_16,color_FFFFFF,t_70)

```
在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重要。下图中举了一个简单的例子，每个镜像层包含3个文件，而整体的镜像包含了来自两个镜像层的6个文件。
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/80813f9132d64346b8a78ddba127b381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plcnJ5X2xpdWZlbmc=,size_16,color_FFFFFF,t_70)

```
上图中的鏡像层跟之前图中的略有区别，主要目的是便于展示文件。

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版本。
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b459491ccd1147818a4af5ae8151d9fc.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2plcnJ5X2xpdWZlbmc=,size_16,color_FFFFFF,t_70)

```
这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。

Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。

如上边的三层镜像，Docker最终会把所有镜像层堆叠并合并，对外提供统一的视图，如下图
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/5a61f2ea34a843e485a4a34d48bca926.png)

#### 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

![image-20221204140324289](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221204140324289.png)



如何提交一个自己的镜像

#### commit镜像

```shell
docker commit  #提交容器成为一个新的副本
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名：【TAG】
```

实战测试

```shell
#1.启动一个默认的tomcat

#2.发现这个默认的tomcat 时没有webapps的应用，镜像的原因，官方的镜像默认webapps下面是没有文件的！

#3.自己拷贝进去基本文件

#4.将操作过的容器通过commit提交作为一个镜像！以后我们就使用自己修改过的镜像即可，这就是一个我们自己修改过的镜像
```

![image-20221204143144245](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221204143144245.png)

学习方法说明：理解概念，但是一定要实践，最后实践和理论相结合，一次搞定这个知识

```
如果你想要保存当前容器的状态，就可以通过commit来提交，获得一个镜像
eg：VMware中的快照！
```



## Docker精髓

### 容器数据卷

#### **docker的理念回顾**

将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！ **需求：数据可以持久化**

MySQL，容器删了，删库跑路！ **需求：MySQL数据可以存储在本地！**

容器之间可以有一个数据共享技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂在，将我们容器内的目录，挂载到了Linux上面！

![image-20221204144628113](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221204144628113.png)

**总结一句话：容器的持久化和同步操作！容器间也是可以数据共享的**



#### **使用数据卷**

```shell
方式一：直接使用命令来挂载 -v

docker run -it -v 主机目录:容器内目录

#测试
[root@localhost home]# docker run -it -v /home/ceshi:/home centos /bin/bash

#启动起来时候我们可以通过docker inspect 容器id
```

![image-20221204145859764](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221204145859764.png)

测试文件的同步

![image-20221204150358462](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221204150358462.png)

再来测试！

1.停止容器

2.宿主机上修改文件

3.启动容器

4.容器内的数据依旧是同步的

![image-20221204150611803](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221204150611803.png)

好处：我们以后修改只需要在本地修改即可，容器本地内会自动同步！

------



#### 安装Mysql

思考：Mysql的数据持久化问题！

```shell
#获取镜像
docker pull mysql:5.7
#运行容器，需要做数据挂载！ #安装并启动mysql，需要配置密码，这是要注意点！
#官方测试：docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

#启动
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 名字
[root@localhost ~]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7 /bin/bash
```

#### 具名挂载和匿名挂载

```shell
#匿名挂载
-v 容器内的路径！
docker run -d -p --name nginx01 -v /etc/nginx nginx

#查看所有的volume的情况
docker volume ls
local               9fc5676080feacd078b43a89b573c650e696dbd0abe257f9f41315cc95aa5341

#这里发现，这种就是匿名挂载，我们在-v只写了容器内的路径，没有写容器外的路径！

#具名挂载
[root@localhost mysite]# docker run -d -P --name nginx03 -v juming-nginx:/etc/nginx nginx
0d3af10f4f8294fbac717baffab5962537c01ba9f1116599880f90a77e3b4ebe
[root@localhost mysite]# docker volume ls
DRIVER              VOLUME NAME
local               9fc5676080feacd078b43a89b573c650e696dbd0abe257f9f41315cc95aa5341
local               juming-nginx

#通过 -v 卷名：容器内路径
#查看一下这个卷
```

![image-20221205123142544](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221205123142544.png)

所有的docker容器内的卷，没有指定目录的情况下都是在`**/var/lib/docker/xxxx/data**`

我们通过具名挂载可以方便找到我们的一个卷，大多数情况在使用的 **具名挂载**

```shell
#如何确定是具名挂载还是匿名挂载，还是通过指定路径挂载！
-v 容器内路径 #匿名挂载
-v 卷名：容器内路径 #具名挂载
-v /宿主机路径：：容器内路径 #指定路径挂载！
```

拓展：

```shell
#通过 -v 容器内路径：ro rw 改变读写权限
ro readonly #只读
rw readwrite # 可读可写

#一旦这个设置了容器权限，容器对我们挂载出来的内容就有了限定！
docker run -d -P --name nginx03 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx03 -v juming-nginx:/etc/nginx:rw nginx

#ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作的！
```



### **DockerFile**

#### 初识DockerFile

------

Dockerfile就是用来构建docker镜像的构建文件！命令脚本！

通过这个脚本可以可以生成镜像

```shell
#创建一个dockerfile文件，名字可以随机  建议Dockerfile
#文件中的内容   指令（大写）  参数
FROM centos

VOLUME ["volume01","volume02"]  #匿名挂载

CMD echo "----end----"
CMD /bin/bash
#这里的每一个命令，就是镜像的一层！

#构建
[root@localhost docker-test-volume]# docker build -f /root/docker-test-volume/dockerfile1 -t centos:1.0 .
```

![image-20221206112135173](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206112135173.png)

```shell
#启动自己写的容器
```

![image-20221206112455218](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206112455218.png)

这个卷和外部一定有一个同步的目录！

查看一下卷挂载的路径

![image-20221206113120701](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206113120701.png)

测试一下刚才的文件是否同步出去了！
这种方式我们未来使用的十分多，因为我们通常会构建自己的镜像！

假设构建镜像时候没有挂载卷，要手动镜像挂载，-v卷名：容器内路径！

### 数据卷容器

多个mysql同步数据！

![image-20221206113822912](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206113822912.png)

```
#启动三个容器，通过我们刚才自己写的镜像来启动
```

![image-20221206114120220](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206114120220.png)

![image-20221206114942381](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206114942381.png)

![image-20221206115106057](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206115106057.png)

![image-20221206115403854](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206115403854.png)

![image-20221206115608943](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221206115608943.png)

```shell
#测试：当删除了docker01之后，查看docker02和docker03，文件依旧可以访问！
```

相当于三个容器都是挂载到宿主机的同一个目录下的，如果删除掉宿主机下的目录文件，三个容器的数据都会消失！

多个mysql实现数据共享

```shell
[root@localhost ~]# docker run -d -p 3310:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

[root@localhost ~]# docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01 mysql:5.7

#这个时候，可以实现两个容器数据同步！
```



**结论：**

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

但是一旦你持久化到了本地，这个时候，本地的数据是不会删除的！



## DockerFile

------

#### Dockerfile介绍

dockerfile是用来构建docker镜像的文件！命令参数脚本！

构建步骤：

1.编写一个dockerfile文件

2.docker build构建成为一个镜像

3.docker run 运行镜像

4.docker push 发布镜像（DockerHub、阿里云镜像仓库）

查看一下官方是怎么做的？

![image-20221209151543914](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221209151543914.png)

![image-20221209151732571](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221209151732571.png)

很多官方镜像都是基础包，很多功能没有，我们需要自己搭建自己的镜像！

官方既然可以制作镜像，那我们也可以！



DockerFile构建过程

------

基础知识：

1.每个保留关键字（指令）都是必须是大写字母

2.执行从上到下顺序执行

3.#表示注释

4.每一个指令都会创建提交一个新的镜像层，并提交！

![image-20221209152325462](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221209152325462.png)

dockerfile是面向开发的，我们以后要发布项目，做镜像，就是要编写dockerfile文件，这个文件非常简单！

Docker镜像逐渐成为企业交付的标准，必须掌握！

步骤：开发、部署、运维....缺一不可

Dockerfile：构建文件，定义了一切的步骤，源代码

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品！

Docker容器：就是镜像运行起来后的状态



DockerFile的指令

------

以前的话我们就是使用别人的，现在我们知道了这些指令后，我们来练习自己写一个镜像！

```shell
FROM 			 # 基础镜像，一切从这里构建
MAINTAINER		 # 镜像是谁写的，姓名＋邮箱
（LABEL）         # 新版本使用，可以支持多行
RUN				# 镜像构建的时候需要运行的命令
ADD				# 步骤：tomcat镜像，这个tomcat压缩包！添加内容
WORKDIR			# 镜像的工作目录
VOLUME			# 挂载的目录
EXPOSE			# 声明端口
CMD				# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		# 指定这个容器启动的时候要运行的命令
ONBUILD			# 当构建一个被继承DockerFile，这个时候就会运行onbuild指令 触发指令
COPY  			# 类似ADD ， 将文件拷贝到镜像中
ENV				# 构建的时候设置环境变量
```

![image-20221209154729579](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221209154729579.png)

注意：CMD最后一个生效，enterpoint和cmd同时存在时，谁在最后一行谁生效，他们都是在容器启动时生效，RUN是在构建过程中执行



#### **实战测试**

------

Docker Hub中99%镜像都是从这个基础镜像过来的FROM scratch，然后配置需要的软件和配置来进行构建

![image-20221211161222102](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221211161222102.png)

```shell
创建一个自己的centos
#1.编写Dockerfile的文件
> cat mydockerfile-centos
FROM centos:7
LABEL author="yingxu"
LABEL email="2875760424@qq.com"

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum install vim -y
RUN yum install net-tools -y

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

#2.通过这个文件构建镜像
#命令 docker build -f dockerfile文件路径 -t 镜像名：【tag】
Successfully built d317ad14e9f3
Successfully tagged mycentos:0.1

#3.测试运行
```

对比：之前的原生的centos

![image-20221211163715876](C:\Users\86159\Documents\image-20221211163715876.png)

我们增加后的镜像

![image-20221211163800600](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221211163800600.png)

我们可以列出本地进行的变更历史

![image-20221211164135901](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221211164135901.png)

我们平时拿到一个镜像，可以研究一下它是怎么做的？



> CMD 和 ENTRYPOINT 区别

```shell
CMD				# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		# 指定这个容器启动的时候要运行的命令
```

测试cmd

```shell
#编写dockerfile 文件
> vim dockerfile-cmd-test
FROM centos
CMD ["ls","-a"]

#构建镜像
> docker build -f dockerfile-cmd-test -t cmdtest .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM centos
 ---> 5d0da3dc9764
Step 2/2 : CMD ["ls","-a"]
 ---> Running in 0a81d8092b25
Removing intermediate container 0a81d8092b25
 ---> 31f701137fac
Successfully built 31f701137fac
Successfully tagged cmdtest:latest

#run运行，发现我们ls -a命令生效
> docker run 31f701137fac
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found

#想追加一个命令 -l     ls -al
> docker run 31f701137fac -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:330: starting container process caused "exec: \"-l\": executable file not found in $PATH": unknown.

#cmd的情况下 -l 替换了CMD 【“ls”，“-a”】 命令，-l 不是命令 所以报错！
#cmd像一个变量参数，可以传给启动容器时需要的运行参数
```

测试ENTRYPOINT

```shell
> vim dockerfile-cmd-entrypoint
FROM centos
CMD ["ls","-a"]

> docker build -f dockerfile-cmd-entrypoint -t entrypoint-test .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos:7
 ---> eeb6ee3f44bd
Step 2/2 : ENTRYPOINT ["ls","-a"]
 ---> Running in ffb45d3f750d
Removing intermediate container ffb45d3f750d
 ---> 1c1df8c60dbb
Successfully built 1c1df8c60dbb
Successfully tagged entrypoint-test:latest
> docker run 1c1df8c60dbb
.
..
.dockerenv
anaconda-post.log
bin
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

#我们的追加命令，是直接拼接在我们的 ENTRYPOINT 命令后面的！
> docker run 1c1df8c60dbb -l
total 68
drwxr-xr-x.   1 root root  4096 Dec 11 08:57 .
drwxr-xr-x.   1 root root  4096 Dec 11 08:57 ..
-rwxr-xr-x.   1 root root     0 Dec 11 08:57 .dockerenv
-rw-r--r--.   1 root root 12114 Nov 13  2020 anaconda-post.log
lrwxrwxrwx.   1 root root     7 Nov 13  2020 bin -> usr/bin
drwxr-xr-x.   5 root root   340 Dec 11 08:57 dev
drwxr-xr-x.   1 root root  4096 Dec 11 08:57 etc
drwxr-xr-x.   2 root root  4096 Apr 11  2018 home
lrwxrwxrwx.   1 root root     7 Nov 13  2020 lib -> usr/lib
lrwxrwxrwx.   1 root root     9 Nov 13  2020 lib64 -> usr/lib64
drwxr-xr-x.   2 root root  4096 Apr 11  2018 media
drwxr-xr-x.   2 root root  4096 Apr 11  2018 mnt
drwxr-xr-x.   2 root root  4096 Apr 11  2018 opt
dr-xr-xr-x. 220 root root     0 Dec 11 08:57 proc
dr-xr-x---.   2 root root  4096 Nov 13  2020 root
drwxr-xr-x.  11 root root  4096 Nov 13  2020 run
lrwxrwxrwx.   1 root root     8 Nov 13  2020 sbin -> usr/sbin
drwxr-xr-x.   2 root root  4096 Apr 11  2018 srv
dr-xr-xr-x.  13 root root     0 Dec 11 08:08 sys
drwxrwxrwt.   7 root root  4096 Nov 13  2020 tmp
drwxr-xr-x.  13 root root  4096 Nov 13  2020 usr
drwxr-xr-x.  18 root root  4096 Nov 13  2020 var
```

Dockerfile中很多命令都十分的相似，我们需要了解它们的区别，我们最好的学习就是对比他们然后测试效果！



#### **实战：Tomcat镜像**

------

1.准备镜像文件tomcat压缩包，jdk的压缩包！

![image-20221211231948348](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221211231948348.png)

2.编写dockerfile文件,官方命名'Dockerfile' ,build会自动寻找这个文件，就不用-f指定了

```shell
FROM centos:7
LABEL author="Yingxu"

COPY readme.txt /usr/local/readme.txt

ADD apache-tomcat-9.0.70.tar.gz /usr/local/
ADD jdk-8u11-linux-x64.rpm /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.70
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.70
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.70/bin/startup.sh && tail -f /usr/local/apache-tomcat-9.0.70/logs/catalina.out
```

3.构建镜像

```shell
# docker build -t diytomcat
```

4.启动镜像

5.访问测试

6.发布项目（由于做了卷挂载，我们直接在本地编写项目就可以发布了！）

#### **小结**

![image-20221212091956117](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212091956117.png)



**Docker网络**

------

Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

#### 理解Docker 0

------

>测试

![image-20221212154554177](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212154554177.png)

三个网络

```shell
#问题：docker是如何处理容器网络访问的呢？
```

![image-20221212154956743](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212154956743.png)

```shell
# > docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址  ip addr ， 发现容器启动的时候会得到一个 eth0@if13 ip地址，docker分配的！
> docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
12: eth0@if13: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.4/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

# 思考：linux能不能ping通容器内部！
> ping 172.17.0.4
PING 172.17.0.4 (172.17.0.4) 56(84) 字节的数据。
64 字节，来自 172.17.0.4: icmp_seq=1 ttl=64 时间=0.497 毫秒
64 字节，来自 172.17.0.4: icmp_seq=2 ttl=64 时间=0.085 毫秒

# linux 可以 ping 通 docker 容器内部
```

>原理

1.我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0桥接模式，使用的技术是evth-pair技术！

再次测试ip addr

![image-20221212160851650](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212160851650.png)

2.再启动一个容器测试，发现又多了一对网卡！

![image-20221212161210544](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212161210544.png)

```shell
# 我们发现这个容器带来网卡，都是一对一对的
# evth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连
# 正因为有这个特性， evth-pair 充当一个桥梁，连接各种虚拟网络设备的
# OpenStac，Docker容器之间的连接，OVS的连接，都是使用了 evth-pair 技术
```

3.我们来测试下 tomcat01 和 tomcat02 是否可以ping通！

```shell
> docker exec -it tomcat02 ping 172.17.0.4
# 结论：容器和容器之间是可以互相ping通的！
```

绘制一个网络模型图：

![image-20221212162645217](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212162645217.png)

结论： tomcat01 和 tomcat02 是公用的一个路由器，docker0

所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用IP



255.255.0.1/24    域   局域网！

0000000.0000000.0000000.0000000

**255.255.255.255**

255.255.0.0



>小结

Docker使用的是Linux的桥接，宿主机是一个Docker容器的网桥 docker0.

![image-20221212163446949](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212163446949.png)

Docker中的所有网络接口都是虚拟的。虚拟的转发效率高！（内网传递文件！）

只要容器删除，对应网桥一对就没了！



![image-20221212165657987](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212165657987.png)



#### --**link**

>思考一个场景，我们编写了一个微服务，database url=ip：，项目不重启，数据库ip换掉了，我们希望可以处理这个问题，可以通过名字来访问容器！

```shell
> docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

# 如何可以解决呢？
> docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

# 通过--link 既可以解决网络连通问题
> docker run -d -P --name tomcat03 --link tomcat02 tomcat:7.0
a51cae1d652120a73879440af460d17c53f5533cc1f839c9a2a824f82b4d6696
> docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.5) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.5): icmp_seq=1 ttl=64 time=0.168 ms
64 bytes from tomcat02 (172.17.0.5): icmp_seq=2 ttl=64 time=0.306 ms
64 bytes from tomcat02 (172.17.0.5): icmp_seq=3 ttl=64 time=0.072 ms
--- tomcat02 ping statistics ---
3 packets transmitted, 19 received, 0% packet loss, time 437ms
rtt min/avg/max/mdev = 0.062/0.098/0.306/0.054 ms

# 反向可以ping通吗？
> docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known
```

探究：inspect！

![image-20221212165851552](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212165851552.png)

其实这个tomcat03就是本地配置了tomcat02的配置

```shell
# 查看 hosts 配置 ，在这里原理发现！
> docker exec -it tomcat03 cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.4      tomcat02 6989c9585a6f
172.17.0.5      a51cae1d6521
```

本质探究：--link 就是我们再hosts配置中增加了一个172.17.0.4      tomcat02    6989c9585a6f

我们现在玩 Docker 已经不建议用 --link 了！

自定义网络！ 不适用Docker 0 ！

Docker 0 问题： 他不支持容器名连接访问！



#### **centos更新出错解决方法**

------

在学习使用 docker 技术过程中，基于 centos 镜像自定义新的镜像，其中基础镜像 centos 需要提前安装好 [vim](https://so.csdn.net/so/search?q=vim&spm=1001.2101.3001.7020) 和 net-tools，然而在刚开始通过 `yum -y install vim` 安装 vim 时，便出现了错误提示信息：

```shell
Error: Failed to download metadata for repo 'appstream': Cannot prepare internal mirrorlist: No URLs in mirrorlist
```



**问题分析**

✨第一种可能的情况便是网络连接问题。检查是否可以连接外部网络，可以使用 ping baidu.com 查看是否有丢包情况。如果丢包，则进一步检查网络连接是否正常；如果没有丢包，继续阅读下文

✨那么第二种情况，便是 CentOS 已经停止维护的问题。2020 年 12 月 8 号，CentOS 官方宣布了停止维护 CentOS Linux 的计划，并推出了 CentOS Stream 项目，CentOS Linux 8 作为 RHEL 8 的复刻版本，生命周期缩短，于 2021 年 12 月 31 日停止更新并停止维护（EOL），更多的信息可以查看 CentOS 官方公告。如果需要更新 CentOS，需要将镜像从 mirror.centos.org 更改为 vault.centos.org


**针对上面提到的第二种情况，给出的解决方法如下**：



 **首先**，进入到 yum 的 repos 目录

```shell
cd /etc/yum.repos.d/
```

**其次**，修改 centos 文件内容

```shell
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*

sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
```

**然后**，生成缓存更新（第一次更新，速度稍微有点慢，耐心等待两分钟左右）

```shell
yum makecache
```

**最后**，运行 yum update 并重新安装 vim

```shell
yum update -y
yum -y install vim
```





#### **自定义网络**

------



>查看所有的Docker网络

```shell
> docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5eb325063f46        bridge              bridge              local
3a648b0f05ae        host                host                local
909301f478ae        none                null                local
```

##### **网络模式**

Bridge：桥接模式，docker的默认桥接网络（自己创建也是用Bridge模式）

None：不使用任何网络

Host：主机网络，可以使用宿主机的网络栈

Container：容器网络连通！（用的少！局限很大）

**测试**

```shell
# 我们直接启动的命令 --net bridge，而这个就是我们的docker0
~ > docker run -d -P --name tomcat01 tomcat
~ > docker run -d -P --name tomcat01 --net bridge tomcat

# docker0的特点： 默认，域名不能访问， --link可以打通连接！

# 我们可以自定义一个网络！
# --driver bridge
# --subnet 10.1.0.0/16    10.1.0.2 ~ 10.1.255.254
# --gateway 10.1.0.1
> docker network create --driver bridge --subnet 10.1.0.0/16 --gateway 10.1.0.1 mynet
35c5ae47e38498b1de3616a22342fc3d185c610be45a2e0d3312e147e029d5e3

> docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5eb325063f46        bridge              bridge              local
3a648b0f05ae        host                host                local
35c5ae47e384        mynet               bridge              local
909301f478ae        none                null                local
```

我们自己的网络就创建好了！

![image-20221212195636652](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212195636652.png)

```shell
> docker run -d -P --name tomcat-net-01 --net mynet tomcat:7.0
aba4137a54e045b70193709b5325c2c3bbc7242ae2516e66e3343c03141a6d4b
> docker run -d -P --name tomcat-net-02 --net mynet tomcat:7.0
b0daf2cd61d59a048be2b163b6a2cd60271c68444130754f7345cce0a9e9ade5
> docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "2bce5ab4324e2ef57f10e8cfece4b1e1877c5a03bf604ef37315174b456fdb8a",
        "Created": "2022-12-12T19:51:26.496523969+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "c8deb07f16382685295a825689ffc0ce6e10b8b47d451c8f1fbfdd0fa5d93432": {
                "Name": "tomcat-net-01",
                "EndpointID": "92e332b0ff07770a4a2cb6dfae748664fa12e8dbe68f2e5517e4e2795729d6e4",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "dfe795fb89273b2e0dbf8f8b3bbdc359e6b40a175e5f2f49d0aa4c6aff048735": {
                "Name": "tomcat-net-02",
                "EndpointID": "5907a775738607c92aff6f21e4ff35f7adaa1136e8af91fe0e1ec3ce22e4fc9f",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

# 再次测试ping连接
> docker exec -it tomcat-net-01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.073 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.085 ms

--- 192.168.0.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 127ms
rtt min/avg/max/mdev = 0.063/0.074/0.085/0.007 ms

#现在不适用--link也可以 ping 名字了！
> docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.082 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.098 ms

--- tomcat-net-02 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 54ms
rtt min/avg/max/mdev = 0.082/0.093/0.099/0.007 ms

#没有ping命令的进去安装下
apt update && apt install -y iproute2 && apt install -y iputils-ping

```

我们自定义的网络docker都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络！

好处：

redis - 不同的集群使用不同的网络，保证集群是安全和健康的

mysql - 不同的集群使用不同的网络，保证集群是安全和健康的





#### **网络连通**

------

![image-20221212200825958](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212200825958.png)

![image-20221212201006207](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212201006207.png)

```shell
# 测试打通 tomcat01 - mynet

# 连通之后就是将 tomcat01 放到了 mynet 网络下！

# 一个容器两个ip地址！    
# 阿里云服务： 公网ip  私网ip
```

![image-20221212201307741](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212201307741.png)

```shell
# 01 连通Ok
> docker exec -it tomcat01 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.127 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.245 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.080 ms
--- tomcat-net-01 ping statistics ---

3 packets transmitted, 3 received, 0% packet loss, time 70ms
rtt min/avg/max/mdev = 0.080/0.150/0.245/0.070 ms

# 发现 02 是依旧打不通的
> docker exec -it tomcat02 ping tomcat-net-01
ping: tomcat-net-01: Name or service not known
```

**结论：假设要跨网络操作别人，就需要docker network connect 连通！**



#### **实战：部署Redis集群**

------

![image-20221212201823000](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212201823000.png)

shell脚本！

```shell
# 创建网卡
docker network create redis --subnet 172.38.0.0/16

# 通过脚本创建六个redis配置
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat  << EOF >> /mydata/redis/node-${port}/conf/redis.conf
port 6379 
bind 0.0.0.0
cluster-enabled yes 
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

# 运行容器    根据不同的容器来进行 port 配置
docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

# 示例
docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /mydata/redis/node-1/data:/data \
-v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
-v /mydata/redis/node-2/data:/data \
-v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.12 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6373:6379 -p 16373:16379 --name redis-3 \
-v /mydata/redis/node-3/data:/data \
-v /mydata/redis/node-3/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.13 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6374:6379 -p 16374:16379 --name redis-4 \
-v /mydata/redis/node-4/data:/data \
-v /mydata/redis/node-4/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.14 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6375:6379 -p 16375:16379 --name redis-5 \
-v /mydata/redis/node-5/data:/data \
-v /mydata/redis/node-5/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.15 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6376:6379 -p 16376:16379 --name redis-6 \
-v /mydata/redis/node-6/data:/data \
-v /mydata/redis/node-6/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.16 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

# 创建集群
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1

>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: 75707adfa6e9c817f730b8d44ee90b5565e8f799 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: ae756f65bed20369b5f896b02617d0bc2ad5314c 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 8f95cbcc213ad8cccbf8325e2cc64bb5d3de1ac7 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: aa2e0fd7bd417172abea448545684264bc030e6c 172.38.0.14:6379
   replicates 8f95cbcc213ad8cccbf8325e2cc64bb5d3de1ac7
S: a35b4af910bce4d53c90a307418ac4a3aa65665c 172.38.0.15:6379
   replicates 75707adfa6e9c817f730b8d44ee90b5565e8f799
S: 0e831848486d6995ee4e21c5a0d5de9afe765a05 172.38.0.16:6379
   replicates ae756f65bed20369b5f896b02617d0bc2ad5314c
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: 75707adfa6e9c817f730b8d44ee90b5565e8f799 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 0e831848486d6995ee4e21c5a0d5de9afe765a05 172.38.0.16:6379
   slots: (0 slots) slave
   replicates ae756f65bed20369b5f896b02617d0bc2ad5314c
S: a35b4af910bce4d53c90a307418ac4a3aa65665c 172.38.0.15:6379
   slots: (0 slots) slave
   replicates 75707adfa6e9c817f730b8d44ee90b5565e8f799
M: ae756f65bed20369b5f896b02617d0bc2ad5314c 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: aa2e0fd7bd417172abea448545684264bc030e6c 172.38.0.14:6379
   slots: (0 slots) slave
   replicates 8f95cbcc213ad8cccbf8325e2cc64bb5d3de1ac7
M: 8f95cbcc213ad8cccbf8325e2cc64bb5d3de1ac7 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

docker搭建redis集群完成！

![image-20221212205043021](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221212205043021.png)

我们使用了docker之后，所有的技术都会慢慢的变得简单起来！



#### **Springboot微服务打包Docker镜像**

------

1.架构springboot项目

2.打包应用

3.编写dockerfile

4.构建镜像

5.发布运行



## **Docker Compose**

------

Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。



### **Docker Compose是什么？**

------

compose、machine 和 swarm 是docker 原生提供的三大编排工具。简称docker三剑客。

Docker Compose能够在 Docker 节点上，以单引擎模式(Single-Engine Mode)进行多容器应用的部 署和管理。多数的现代应用通过多个更小的微服务互相协同来组成一个完整可用的应用。

部署和管理繁多的服务是困难的。而这正是 Docker Compose 要解决的问题。Docker Compose 并不 是通过脚本和各种冗长的 docker 命令来将应用组件组织起来，而是通过一个声明式的配置文件描述整 个应用，从而使用一条命令完成部署。应用部署成功后，还可以通过一系列简单的命令实现对其完整声 明周期的管理。甚至，配置文件还可以置于版本控制系统中进行存储和管理。



### Compose 使用的三个步骤：

------



- 使用 Dockerfile 定义应用程序的环境。
- 使用 docker-compose.yml 定义构成应用程序的服务，这样它们可以在隔离环境中一起运行。
- 最后，执行 docker-compose up 命令来启动并运行整个应用程序。



#### **安装 docker compose**

------

Docker的社区版（Docker Community Edition）叫做docker-ce。老版本的Docker包叫做docker或者docker-engine，如果安装了老版本的docker得先卸载然后再安装新版本的docker。docker的发展非常迅速，apt源的更新往往比较滞后。所以docker官网推荐的安装方式都是下载docker安装脚本安装。 

```shell
# 卸载老旧的版本（若未安装过可省略此步）
$ sudo apt-get remove docker docker-engine docker.io

# 安装最新的docker
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh

# 确认Docker成功最新的版本
$ sudo docker run hello-world

# 下载docker compose   三种方法
https://github.com/docker/compose 

sudo curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# 添加可执行权限（安装方式一中的）
mv /data/docker-compose-Linux-x86_64 /usr/local/bin/docker-compose

cp /data/docker-compose-Linux-x86_64 /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

开发环境可以授予最高权限

chmod 777 /usr/local/bin/docker-compose

# 添加可执行权限（安装方式二和三中的）
sudo chmod +x /usr/local/bin/docker-compose

```



#### **检查安装情况以及版本**

------

```shell
docker-compose -v

docker-compose --version

docker-compose version
```



#### **卸载docker compose**

------

```shell
rm -rf /usr/local/bin/docker-compose

reboot
```



### **yml配置文件以及常用指令**

------

Docker Compose 使用 YAML 文件来定义多服务的应用。YAML 是 JSON 的一个子集，因此也可以使用JSON。

Docker Compose 默认使用文件名 docker-compose.yml。当然，也可以使用 -f 参数指定具体文件。



#### **yaml文件级**

------

Docker Compose 的 YAML 文件包含 4 个一级 key:version、services、networks、volumes

- version 是必须指定的，而且总是位于文件的第一行。它定义了 Compose 文件格式(主要是 API)的版本。注意，version 并非定义 Docker Compose 或 Docker 引擎的版本号。
- services 用于定义不同的应用服务。上边的例子定义了两个服务:一个名为 lagou-mysql数据库服 务以及一个名为lagou-eureka的微服。Docker Compose 会将每个服务部署在各自的容器中。
- networks 用于指引 Docker 创建新的网络。默认情况下，Docker Compose 会创建 bridge 网络。 这是一种单主机网络，只能够实现同一主机上容器的连接。当然，也可以使用 driver 属性来指定不 同的网络类型。
- volumes 用于指引 Docker 来创建新的卷。



#### **配置文件**

------

```shell
version: '3'
services:
  mysql:
    build:
      context: ./mysql
    environment:
      MYSQL_ROOT_PASSWORD: admin
    restart: always
    container_name: mysql
    volumes:
    - /data/edu-bom/mysql/test:/var/lib/mysql
    image: mysql/mysql:5.7
    ports:
      - 3306:3306
    networks:
      net:
  eureka:
    build:
      context: ./edu-eureka-boot
    restart: always
    ports:
      - 8761:8761
    container_name: edu-eureka-boot
    hostname: edu-eureka-boot
    image: edu/edu-eureka-boot:1.0
    depends_on:
      - mysql
    networks:
      net:
networks:
    net:
volumes:
    vol:

```



### **Docker Compose 常用命令**

------

```shell
# 启动服务
docker-compose up -d

# 停止服务
docker-compose down

# 列出所有运行容器
docker-compose ps

# 查看服务日志
docker-compose logs

# 构建或者重新构建服务
docker-compose build

# 启动服务
docker-compose start

# 停止已运行的服务
docker-compose stop

# 重启服务
docker-compose restart

```



## **Docker Swarm**

------



### 工作模式

------

![image-20221213155841263](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221213155841263.png)



### 搭建集群

------

![image-20221213160030155](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221213160030155.png)

![image-20221213160209332](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221213160209332.png)

![image-20221213160705854](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221213160705854.png)

初始化节点  **docker swarm init**
docker swarm join 加入一个节点！

```shell
#获取令牌    获取到的令牌后，复制使用可以让其他服务器加入集群
docker swarm join-token manager
docker swarm join-token worker
```

![image-20221213161045791](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221213161045791.png)



把后面的节点都搭建进去！

![image-20221213161341079](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20221213161341079.png)



1.生成主节点 init

2.加入 （管理者 manager  工作者 worker）



#### **拓展到K8S**

------

#### **云原生时代**

Go语言！ 必须掌握！ java、Go!

Docker 是 Go 开发的

K8S 也是 Go 的项目

Etcd 都是 Go 的项目！

并发语言！



**Go**（又称 **Golang**）是 [Google](https://baike.baidu.com/item/Google/86964?fromModule=lemma_inlink) 的 Robert Griesemer，Rob Pike 及 Ken Thompson 开发的一种[静态](https://baike.baidu.com/item/静态?fromModule=lemma_inlink)[强类型](https://baike.baidu.com/item/强类型?fromModule=lemma_inlink)、[编译型语言](https://baike.baidu.com/item/编译型语言/9564109?fromModule=lemma_inlink)。Go 语言语法与 [C](https://baike.baidu.com/item/C/7252092?fromModule=lemma_inlink) 相近，但功能上有：内存安全，[GC](https://baike.baidu.com/item/GC/66426?fromModule=lemma_inlink)（垃圾回收），[结构形态](https://baike.baidu.com/item/结构形态/5942010?fromModule=lemma_inlink)及 CSP-style [并发计算](https://baike.baidu.com/item/并发计算/9939802?fromModule=lemma_inlink)。



罗伯特·格瑞史莫（Robert Griesemer），[罗布·派克](https://baike.baidu.com/item/罗布·派克/10983505?fromModule=lemma_inlink)（Rob Pike）及[肯·汤普逊](https://baike.baidu.com/item/肯·汤普逊/7585160?fromModule=lemma_inlink)（Ken Thompson）于2007年9月开始设计Go，稍后Ian Lance Taylor、Russ Cox加入项目。Go是基于[Inferno](https://baike.baidu.com/item/Inferno?fromModule=lemma_inlink)操作系统所开发的。Go于2009年11月正式宣布推出，成为[开放源代码](https://baike.baidu.com/item/开放源代码/114160?fromModule=lemma_inlink)项目，并在[Linux](https://baike.baidu.com/item/Linux?fromModule=lemma_inlink)及[Mac OS X](https://baike.baidu.com/item/Mac OS X?fromModule=lemma_inlink)平台上进行了实现，后来追加了Windows系统下的实现。在2016年，Go被软件评价公司TIOBE 选为“TIOBE 2016 年最佳语言”。 目前，Go每半年发布一个二级版本（即从a.x升级到a.y）。



## **CI/CD之jenkins**

------
