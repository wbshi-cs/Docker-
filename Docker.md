# Docker学习

参考链接：http://c.biancheng.net/view/3118.html  与  https://www.w3cschool.cn/docker/

## 1 介绍

### 	1.1 简介 	

​			[Docker](http://c.biancheng.net/docker/) 是一种运行于 Linux 和 Windows 上的软件，用于创建、管理和编排容器。Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

### 	1.2 应用场景

​			Web 应用的自动化打包和发布。

​			自动化测试和持续集成、发布。

​			在服务型环境中部署和调整数据库或其他的后台应用。

​			从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境。

### 	1.3 优点

​			Docker 是一个用于开发，交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的方法来快速交付，测试和部署代码，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

## 2 安装

### 	2.1 linux 安装（Ubuntu）

#### 		2.1.1 注意事项

​				Docker 要求 Ubuntu 系统的内核版本高于 3.10。 uname -r 命令查看你当前的内核版本。

#### 		2.1.2 安装

​				1、更新Ubuntu相关软件： sudo apt-get update

​				2、管理员安装时： sudo apt-get install -y docker.io   		

​				3、启动docker 后台服务： sudo service docker start

​				4、非 root 用户使用 Docker时。需要添加非 root 用户到本地 Docker Unix 组当中，如添加wbshi的用户添加到 Docker 组中： sudo usermod -aG docker wbshi

​					检查是否添加成功： cat /etc/group | grep docker

​				5、检查docker版本： docker --version

​				6、启动Docker后台服务：  sudo service docker start

## 3 Docker使用

### 	3.1 Hello World

```
1、 docker run ubuntu:18.04 /bin/echo "Hello world"  
# 解释：
# docker: Docker 的二进制执行文件。
# run:与前面的 docker 组合来运行一个容器。
# ubuntu:18.04指定要运行的镜像，Docker首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像。
# /bin/echo "Hello world": 在启动的容器里执行的命令
# 以上命令完整的意思可以解释为：Docker 以 ubuntu18.04 镜像创建一个新容器，然后在容器里执行 bin/echo "Hello world"，然后输出结果。

2、 docker run -i -t ubuntu:18.04 /bin/bash   # 通过docker的两个参数 -i -t，让docker运行的容器实现"对话"的能力

# -t:在新容器内指定一个伪终端或终端。
# -i:允许你对容器内的标准输入 (STDIN) 进行交互。

3、 docker ps  # 查看正在运行的容器及ID
4、 docker logs ID  # 命令，查看容器内的标准输出
5、 docker stop ID  # 停止容器
```

### 	3.2 Docker 容器使用

```
1、 docker run -d -P training/webapp python app.py

#  -d:让容器在后台运行。
#  -P:将容器内部使用的网络端口映射到我们使用的主机上。
#  -p 标识来绑定指定端口。 例如：

2、  docker run -d -p 5000:5000 training/webapp python app.py

3、  docker port + ID或者名字  # 查看指定容器的某个确定端口映射到宿主机的端口号

4、  docker logs + ID或者名字 # 可以查看容器内部的标准输出。

5、  docker top + ID或者名字 # 查看容器内部运行的进程。

6、  docker stop + ID或者名字 # 停止WEB应用容器

7、  docker start + ID或者名字 # 启动容器

8、  docker rm + ID或者名字 # 删除容器

```

### 	3.3 Docker 镜像使用

```
1、 docker images  # 列出本地主机上的镜像
    #  REPOSTITORY：表示镜像的仓库源
    #  TAG：镜像的标签
    #  IMAGE ID：镜像ID
    #  CREATED：镜像创建时间
    #  SIZE：镜像大小
    
2、 docker pull ubuntu:13.10 #仓库源:标签   docker pull 命令来下载本地主机上使用一个不存在的镜像
3、 docker search + 仓库源  #搜索镜像

######### 创建镜像 ##########
######  1、从已经创建的容器中更新镜像，并且提交这个镜像  #########
#  1、进入已有的容器内
docker run -t -i ubuntu:18.04 /bin/bash
#  2、更新容器，例如：
apt-get update
#  3、更新后退出
exit
#  4、docker commit来提交容器副本
docker commit -m="has update" -a="youj" 5a214d77f5d7 wbshi/ubuntu:v2
## 参数介绍：
#  -m:提交的描述信息
#  -a:指定镜像作者
#  5a214d77f5d7：容器ID,这个ID是运行容器时产生的，在左边可以看到
#  wbshi/ubuntu:v2:指定要创建的目标镜像名

######  2、创建Dockerfile文件，文件中包含一组指令来告诉 Docker 如何构建我们的镜像  #########

FROM    centos:6.7
MAINTAINER      Fisher "fisher@sudops.com"

RUN     /bin/echo 'root:123456' |chpasswd
RUN     useradd youj
RUN     /bin/echo 'youj:123456' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" &gt; /etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D

## 每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。
## 第一条FROM，指定使用哪个镜像源
## RUN 指令告诉docker 在镜像内执行命令，安装了什么。。


docker build -t youj/centos:6.7 .

#  -t ：指定要创建的目标镜像名
#  . ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径
#  之后可以用这个新的镜像来创建容器
```

### 3.4 镜像和容器的区别与联系

在 Docker 世界中，镜像实际上等价于未运行的容器。如果作为一名开发者，则可以将镜像比作类（Class）。容器是镜像的运行时实例。

## 4 Docker官方英文资源

docker官网：[http://www.docker.com](http://www.docker.com/)

Docker windows入门：https://docs.docker.com/windows/

Docker Linux 入门：https://docs.docker.com/linux/

Docker mac 入门：https://docs.docker.com/mac/

Docker 用户指引：https://docs.docker.com/engine/userguide/

Docker 官方博客：http://blog.docker.com/

Docker Hub: https://hub.docker.com/

Docker开源： https://www.docker.com/open-source

### Docker中文资源

Docker中文网站：[http://www.docker.org.cn](http://www.docker.org.cn/)

Docker安装手册：http://www.docker.org.cn/book/install.html

一小时Docker教程 ：https://blog.csphere.cn/archives/22

Docker 从入门到实践：http://dockerpool.com/static/books/docker_practice/index.html

Docker中文指南：http://www.widuu.com/chinese_docker/index.html

### 其它资源

https://segmentfault.com/t/docker

https://github.com/docker/docker

https://wiki.openstack.org/wiki/Docker

https://wiki.archlinux.org/index.php/Docker