docker 中文版地址: https://www.docker.org.cn
# 什么是docker?

> docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的`容器`。

开发者在笔记本上编译测试通过的容器可以批量地在生产环境部署，包括vms(虚拟机)、bare meral(裸金属服务器（Bare Metal Server）之所以有这么个奇怪的名字，主要是为了和虚拟化服务器划清界限,完全专用的服务器)、OpenStack 是一个利用虚拟资源池构建和管理私有云和公共云的平台

#### docker通常用于如下场景:

- web应用自动化打包和发布
- 自动化测试和持续集成、发布
- 在服务器环境中部署和调整数据库或其它后台应用
- 从头编译或者扩展现有的Openshift或Cloud Foundry平台来搭建自己的Paas环境


# 准备工作

docker系统有两个程序:docker服务端和客户端
> 服务端：是一个服务进程，管理着所有的容器

> 客服端：扮演者docker服务端的远程操控者，可以用来控制docker的服务端进程，大部分下,docker服务端和客户端在同一台机器下

# 查看docker官网可用的镜像
使用docker最简单的方式莫过于从现有的容器镜像开始，docker官方专门储存了所有的镜像，网址是: https://index.docker.io/ ,通过这个网址可以查找到自己需要的镜像，或者使用命令工具要搜索

#### 使用命令工具要搜索镜像
命令格式: `docker search {$name}` {$name} 镜像名称
> docker search redis

# 下载容器镜像
命令格式: `docker pull {$name}`

# 在容器中运行hello word
docker容器可以理解为沙盒中运行的进程，这个沙盒包括了该进程运行所必须的资源，包括文件系统、系统类库、shell环境等，但这个沙盒默认是不会运行任何的程序的，你需要在沙盒中运行一个进程来运行某个容器，这个进程是该容器的唯一进程，所以当该进程结束的时候，该进程下的容器也会停止。

#### 使用命令工具,在容器中输出hello word
命令格式: `docker run {$image} echo "hello word"` {$image} 镜像名称

docker run 命令有2个参数，一个是镜像名称，一个是要在镜像中运行的命令
> docker run redis echi "hello word"

# 在容器中安装扩展程序
命令格式: `docker run {$image} apt-get install -y {$name}` {$image} 镜像名称 {$name}扩展名称

在ubuntn中用apt-get命令来安装扩展，在执行apt-get时候，要带上-y参数，不带话apt-get会进入交互模式，需要用户输入命令来进行确认，但docker环境中无法响应这种交互

> docker run redis apt-get install -y ping

或者进入容器中去安装扩展

命名格式: `docker exec -it {$imageId} bash` {$imageid} 容器id
> docker exec -it f6c471c19ba7 bash

# 保存对容器的修改
当你对某一个容器做了修改之后(通过在容器中运行某一个命令)，可以把对容器的修改保存下来，这样下次可以从保存后的最新状态运行容器，docker中保存状态的过程为`commiting`，它保存的新旧状态之前的区别，从而产生一个新的版本

命令格式: `docker commit {$content} {$name}` {$name} 容器名称 {$content} 提交内容描述

> docker commit "redis echo输出hello word内容" redis

# 运行新的镜像
> docker run redis ping www.baidu.com

# 查看镜像
`docker ps` 查看正在运行中的容器列表，`docker inspect {$name}` 查看容器更加详细的容器信息。{$name} 容器名称 或者 容器id
> docker inspect redis/f6c471c19ba7

# 发布镜像
将自己编译好的镜像发布docker官方索引网站上，docker的index网站
- 需要登录docker,docker login
- docker tag 新的镜像打tag,没有打tag,默认是latest
- docker push {$image}:{$tag}

`docker push {$name}:{$tag}` {$name} 容器名称
> docker push redis:12222






