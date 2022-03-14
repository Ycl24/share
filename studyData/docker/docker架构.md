## docker架构
[原地址](https://cloud.tencent.com/developer/article/1540097)

### docker架构总览
docker使用传统的cs架构，总架构图如下图所示。用户通过docker client与docker daemon进行通信，并将请求发送给docker daemon.

![docker架构图](./images/docker架构图.png)

从上图可以看的出，docker daemon(守护进程)是docker架构中的主要用户接口。首先，它提供了API Server用于接收来自docker client的请求，其后根据不同的请求分发给docker daemon的不同模块执行相应的工作。

### docker组件介绍

1. docker deamon

	docker deamon是docker最核心的后台进程，它负责响应来自docker client的请求，然后将这些请求翻译成系统调用完成容器管理操作，该进程会在后台启动一个API Server,负责接收由docker发送请求，接收到请求将通过docker deamon分发调度，再由具体的函数来执行请求。

2. docker client

	docker client是向docker deamon发送请求，执行相应的容器管理操作。它既是`命令行工具`docker，也可以是任何遵守docker API的客户端

3. image management(镜像管理)

	docker 通过`distribution、register、layer、image、reference`等模块实现了docker镜像管理

	- distribution负责与docker register交互，上传下载镜像以及储存v2 register有关的元数据
	- register模块负责与docker register有关的身份验证、镜像查找、镜像验证以及管理registry mirror等交互操作
	- image模块负责与镜像元数据有关的储存、查找、镜像层的索引、查找以及镜像tar包有关的导入、导出等操作
	- reference 负责储存本地所有镜像的reopsitory和tag名，并维护与镜像ID之间的映射关系
	- layer 模块负责与镜像层和容器层元数据有关的增删改查，并负责将镜像层的增删改查操作映射到实际储存镜像层文件系统的graphdriver模块

	

4. execdriver、volumedriver\graphdriver
	
	- execdriver 是对linux操作系统的namespaces、cgrouos、selinux等容器运行所需的系统操作进行的一层二次封装，其本质类似LXC.execdriver最主要的实现为libcontainer库
	- volumedriver 是volume数据卷储存操作的最终执行者。负责volume的增删改查，屏蔽不同驱动实现的区别，为上层调用者提供一个统一的接口，volumedriver的默认实现是local
	- graphdriver 是所有与容器镜像相关操作的最终执行者。graphdriver会在docker工作目录下维护一组与镜像层对应的目录，并记下镜像层之间的关系以及具体的graphdriver实现相关的元素数据。目前graphdriver的实现有aufs、btrfs、zfs、devicemapper、overkay和vfs.

5. network
	
	在docker1.9版本以前，网络是通过networkdrive模块以及libcontainer库完成的，现在这部分功能以及分离成一个libnetwork库独立维护。libnetwork抽象出了一个容器网络模型（Container Network Model，CNM），并给调用者提供了一个统一抽象接口，其目标并不仅限于docker容器。CNM模型对真实的容器网络抽象出了沙盒（sandbox）、端点（endpoint）、网络（network）这3种对象，由具体网络驱动（包括内置的Bridge、Host、None和overlay驱动以及通过插件配置的外部驱动）操作对象，并通过网络控制器提供一个统一接口供调用者管理网络。网络驱动负责实现具体的操作，包括创建容器通信所需要的网络，容器的network namespaces,这个网络所需的虚拟网卡，分配通信所需的IP,服务访问的端口和容器与宿主机之间的端口映射，设置Host、resolv.conf、iptabless等




