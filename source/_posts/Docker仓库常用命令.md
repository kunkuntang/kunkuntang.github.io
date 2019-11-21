title: Docker仓库常用命令
author: Tommi_Max
tags:
  - 备忘翻阅
categories:
  - Docker
date: 2019-11-21 15:23:00
---
### 登录

```
docker login
```

通过执行`docker login`命令来输入用户名、密码和邮箱来完成注册和登录。注册成功后，本地用户目录的`.dockercfg`中将保存着用户认证的信息。

### 从仓库拉取镜像

```
docker pull [镜像名]
```

### 搜索镜像

```
docker search [想要搜索的镜像名]
```

例如：

```
C:\Users\kunta>docker search centos
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
centos                             The official build of CentOS.                   5605                [OK]             
ansible/centos7-ansible            Ansible on Centos7                              123                                     [OK]
jdeathe/centos-ssh                 OpenSSH / Supervisor / EPEL/IUS/SCL Repos - …   113                                     [OK]
consol/centos-xfce-vnc             Centos container with "headless" VNC session…   99                                      [OK]
centos/mysql-57-centos7            MySQL 5.7 SQL database server                   63                                   
imagine10255/centos6-lnmp-php56    centos6-lnmp-php56                              57                                      [OK]
tutum/centos                       Simple CentOS docker image with SSH access      45                                   
centos/postgresql-96-centos7       PostgreSQL is an advanced Object-Relational …   39                                  
kinogmt/centos-ssh                 CentOS with SSH                                 29                                   
```

我们可以根据镜像是否为官方提供的依据，分为两类。一种是类似centos这样的基础镜像，称为基础或根镜像。这些镜像是由Docker公司创建、验证、支持、提供的。

另外一种是类似`ansible/centos7-ansible`这样类型的镜像，它是由其它非官方的用户或组织提供的，一般是通过在基础镜像上添加了某些功能然后再提交发布上来供大家使用。像`ansible/centos7-ansible`这个镜像，它是由名为`ansible`这个用户或组织维护的，带有用户名为前缀，表明是某个用户的仓库。

### 自动创建

自动创建( Automated Builds)功能对于需要经常升级镜像内程序来说十分方便。有时候，用户创建了镜像，安装了某个软件，如果软件发布新版本则需要手动更新镜像。

而自动创建功能使得用户通过DockerHub指定跟踪个目标网站(目前支持GitHub或BitBucket)上的项目，一旦项目发现新的提交，则自动执行创建。

要配置自动创建，包括如下的步骤:
1. 创建并登录Docker Hub, 以及目标网站; *在目标网站中连接帐户到Docker Hub。
2. 在Docker Hub中配置一个 自动创建。
3. 选取一个目标网站中的项目(需要含Dockerfile)和分支。
4. 指定Dockerfile的位置，并提交创建。

之后，可以在DockerHub的“自动创建"页面中跟踪每次创建的状态。


### 创建和使用私有仓库、

安装Docker后，可以通过官方提供的registry镜像来简单搭建一-套本地私有仓库环境:

```
docker run -d -p 5000:5000 registry
```

> docker run 命令执行时如果发现本地没有对应的镜像，则会先去拉取镜像再运行。

默认情况下，会将仓库创建在容器的/tmp/registry目录下。可以通过-v参数来将镜像文件存放在本地的指定路径上。

例如下面的例子将上传的镜像放到/opt/data/registry目录:

```
docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry 
```

此时，在本地将启动- 个私有仓库服务，监听端口为5000。

### 实战

首先在一台服务器机子上搭建好私有仓库，其地址为10.0.2.2:5000。然后再试图从机子上上传和下载镜像。

在本地上查看已有镜像：

```
C:\Users\kunta>docker images
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
hub.c.163.com/kuntang/lingermarket   latest              c7a70a3810cf        23 months ago       418MB
ubuntu2                              16.04               1196ea15dad6        2 years ago         336MB
ubuntu                               latest              1196ea15dad6        2 years ago         336MB
hub.c.163.com/public/ubuntu          16.04-tools         1196ea15dad6        2 years ago         336MB
hub.c.163.com/public/centos          6.7-tools           b2ab0ed558bb        2 years ago         602MB
```

使用`docker tag`命令把镜像的仓库标记为私有仓库机子地址（格式为：docker tag IMAGE[:TAG] [REGISTRYHOST/]NAME[:TAG]）：

```
docker tag ubuntu2:16.04 10.0.2.2:5000/test
docker images

```

使用`docker push`命令上传镜像：

```
docker push 10.0.2.2:5000/test
```

用`curl`查看私有仓库的镜像

```
curl http://10.0.2.2:5000/v1/search
```

可以看出镜像已经上次成功。

现在可以到任意一台能访问到10.0.2.2地址的机器去下载这个镜像了:

```
docker pull 10.0.2.2:5000/test
```

---

## 更多阅读

Docker系列文章：

1. [Docker中镜像、容器的常用命令](https://juejin.im/post/5d8820cae51d453b1e478b91)
2. [Docker仓库常用命令](https://juejin.im/post/5d9f53d26fb9a04e3142120c)
3. [Docker中容器的网络配置常用命令](https://juejin.im/post/5d9f4f86e51d4577f3534ead)