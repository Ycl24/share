### cassandra安装

[在线链接](https://www.w3cschool.cn/cassandra/cassandra_installation.html)

#### linux安装 cassandra

1. 预安装设置
> 在linux环境中安装cassandra之前，我们需要使用shh（安全shell）设置linux.

2. 创建用户
> 建议创建hadoop一个单独用户，便于hadoop文件系统与unix文件系统隔离

> `useradd {$username}` {$username}是用户名

> `passwd {$username}` 打开现有的账号

> `New passwd` 输入新的密码

3. SSH设置和密钥生成

> 需要SSH设置才能在集群上执行不同的操作，例如：启动，停止和分布式守护程序shell操作。要对hadoop的不同用户进行身份验证，需要为hadoop用户提供公钥和私钥，并与不同的用户共享。

以下命令用于使用SSH生成键值对：

- 将公钥表单id_rsa.pub复制到authorized_keys
- 并提供所有者
- 分别对authorized_keys文件的读写权限

> ssh-keygen -t rsa

> cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

> chmod 0600 ~/.ssh/authorized_keys

验证SSH

> ssh localhost


4. 安装java(java是cassandra 的主要先决条件)

	1. 检测是否安装java
		> java version

	2. 没有安装

		- 在linux下载java [java sdk官网](https://www.oracle.com/java/technologies/downloads/) [JDK <latest version> - X64.tar.gz](https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz)
		- 找到下载tar，并解压 `tar zxf {$path}` {$path} 下载tar的路径
		- 要使java对所有用户可见，您必须将解压的文件夹移动到`/usr/local`下
		- 设置PATH和JAVA_HOME变量，请将以下命令添加到`~/.bashrc`中

			> export JAVA_HOME=/usr/local/jdk1.7.0_71 

			> export PATH=$PATH:$JAVA_HOME/bin

		- 将保存`source ~/.bashrc`信息

	3. 	检测java 
		> java version

5. 设置路径

> 在`~/.bashrc`中设置`cassandra`路径的路径，因hadoop文件系统与unix文件系统隔离，需要进入hadoop用户下设置

> [hadoop@linux ~]$ gedit ~/.bashrc

> 遇到问题 `(gedit:2222): Gtk-WARNING **: 16:06:09.609: cannot open display:` 

> 解决办法：gedit ~/.bashrc 换成 nano ~/.bashrc 执行

添加如下命令，并保存

> export CASSANDRA_HOME=~/cassandra

> export PATH=$PATH:$CASSANDRA_HOME/bin


6. 下载Cassandra

> Apache Cassandra可用的[下载链接](https://cassandra.apache.org/_/download.html)，Cassandra使用以下命令。

- 要使cassandra对所有用户可见，创建文件夹`/usr/local/cassandra`，并将下载的tar下文件夹内的文件解压到目录中。


## 因java下载的版本问题导致，cassandra运行报错，需要下载java8版本




## docker安装cassandra集群

> 假设第一台服务器的ip地址118.126.91.132，还一台自己本地192.168.1.102

1. 在第一台服务器上执行如下命令
 
>   docker run --name some-cassandra -d -e CASSANDRA_BROADCAST_ADDRESS=118.126.91.132 -p 7000:7000 cassandra:4.0

2. 在第二台服务器上执行如下命令

>  docker run --name some-cassandra -d -e CASSANDRA_BROADCAST_ADDRESS=192.168.1.102  -p 7000:7000 -e CASSANDRA_SEEDS=118.126.91.132 cassandra:4.0


可以添加的参数有很多

 - CASSANDRA_LISTEN_ADDRESS:监听进来的连接地址，在docker中默认使用的就是container的ip address

 - CASSANDRA_BROADCAST_ADDRESS:广播给别的node的地址。

 - CASSANDRA_SEEDS：cassandra bootstrap时通信的节点

 - CASSANDRA_DC:multi datacenter 时需要指定datacenter 名

 - CASSANDRA_RACK：cassandra机架号

 - CASSANDRA_ENDPOITN_SNITCH:网络拓扑策略，用来找寻节点，生产环境一般建议使用GossipingPropertyFileSnitch


3. 在任意一台服务器上进入cassandra的命令行

> docker exec -it some-cassandra bash

> cqlsh

































