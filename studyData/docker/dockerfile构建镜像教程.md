###### docker可以通过dockerfile的内容来自动构建镜像
dockerfile 是一个包含创建镜像所有命令的文本文件，通过docker build命令可以根据dockerfile的内容来构建镜像。
### dockerfile 基本语法结构：
###### 语法都是大写
- FROM
- MAINTAINER
- RUN
- CMD
- EXPOSE
- ENV
- ADD
- COPY
- ENTRYPOINT
- VOLUME
- USER
- WORKDIR
- ONBUILD

#### FROM
用法：`FROM {$image}`

- FROM 指定构建镜像的基础源镜像，如果本地没有指定镜像，则会自动从docker公共库`pull` 镜像下来
- FROM 必须是dockerfile文件中非注释的第一行命令，即dockerfile命令从FROM语句开始
- FROM 可以在dockerfile文件中出现多次，如果需要在一个dockerfile中创建多个镜像
- 如果没有FROM指令命令指定镜像标签，则默认latest标签

#### MAINTAINER
用法：`MAINTAINER {$image}` 指定创建镜像用户,即镜像的`author`属性

#### RUN
用法两种使用方式：

- shell 格式：RUN <命令>
- exec 格式：RUN ["可执行文件", "参数1", "参数2"...]

RUN产生的缓存在下一次构建镜像是不会失效，会被重用，可以使用`--no-cache`选项，即`docker build --no-cache`，如此不会缓存

#### CMD
用法两种使用方式：

- shell 格式：CMD <命令>
- exec 格式：CMD ["可执行文件", "参数1", "参数2"...]

CMD指令就是用于指定默认的容器主进程的启动命令

#### EXPOSE
用法：`EXPOSE {$port} [{$port2}...]` {$port} 暴露的端口

`EXPOSE`指令是声明容器运行时提供服务的端口，这只是一个声明，在容器中运行时并不会因为这个声明应用就开启这个端口服务，在dockerfile的内容中声明有两个好处:

- 帮助镜像使用者理解这个镜像服务的守护端口，比方便配置映射
- 运行时使用随机端口映射时，也就是`docker run -p `时，会自动映射到`EXPOSE`的端口

要将 EXPOSE 和在运行时使用 -p <宿主端口>:<容器端口> 区分开来。-p，是映射宿主端口和容器端口，换句话说，就是将容器的对应端口服务公开给外界访问，而 EXPOSE 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。

#### ENV
用法两种使用方式：

- ENV {$key} {$value}       # 只能设置一个变量
- ENV {$key}={$value} ...   # 允许一次设置多个变量

`ENV`指令是设置环境变量

> ENV httos_proxy=http://xxx.com

#### ADD
用法：`ADD {$src}... {$dest}` {$src}源路径url {$dest}目标路径

`ADD`指令是复制文件，复制本地主机文件、目录或者远程文件URLS从并且添加到容器指定路径中，支持通过`GO`的正则模糊匹配
> ADD home?.txt /data/file/

- 路径必须是绝对路径，如果不存在，会自动创建对应的目录
- 路径必须是dockerfile所在路径的相对路径
- 如果是一个目录，只会复制目录下的内容，而本身目录不会被复制

#### COPY
用法：`COPY {$src}... {$dest}` {$src}源路径url {$dest}目标路径

> COPY home?.txt /data/file/

- 路径必须是绝对路径，如果不存在，会自动创建对应的目录
- 路径必须是dockerfile所在路径的相对路径
- 如果是一个目录，只会复制目录下的内容，而本身目录不会被复制

### ENTRYPOINT
用法两种使用方式：

- shell 格式：ENTRYPOINT <命令>
- exec 格式：ENTRYPOINT ["可执行文件", "参数1", "参数2"...]

`ENTRYPOINT`跟`RUN`指令命令一样，目的跟`CMD`一样，都是在指定的容器启动程序及参数，配置容器启动执行的命令，并且不可被`docker run `提供的参数覆盖，而`CMD`可以被覆盖，如果需要被覆盖，则可以使用`docker run --entrypoint`选项
> 每个dockerfile中只能有一个`ENTRYPOINT`,当指定多个时，只有最后一个生效

### VOLUME
用法：`VOLUME [{$path}...]` {$path}路径

之前说过，容器运行时应该尽量保存容器储存层不发生写操作，对于数据库类需要保存动态数据的应用，其数据库文件应该保存于卷`volume`中，为了防止运行时用户忘记将动态文件所保存目录挂载为卷，在`dockerfile`中，可以事先指定某些目录挂载为匿名卷，这样在运行时用户不指定挂载，其应用也可以正常的运行，不会向容器储存层写入大量数据

`VOLUME  /data`

这里`/data`目录就会在容器运行时自动挂载为匿名卷，任何向`/data`中写入的信息都不会记录到容器储存层，从而保证了容器储存层的无状态变化，当然，运行容器时可以覆盖这个挂载设置，比如:

`docker run -d -v mydata:/data xxxx`

在这行命令中，就使用了`mydata`这个命名卷挂载到`/data`这个位置，替代了`dockerfile`中定义的匿名卷的挂载配置


### USER
用法：`USER [{$user}]` {$user}用户名 

指定运行容器时的用户名或者UID,上述中`RUN` `CMD` `ENTRYPOINT`也会使用指定用户

`USER`指令和`WORKDIR`相似，都是改变环境状态并影响以后的层
- `WORKDIR`是改变工作目录
- `USER`是改变之后层的执行`RUN` `CMD` `ENTRYPOINT`这类命令的身份

注意USER是切换身份，这个用户必须事先创建好，否则无法切换

### WORKDIR
用法：`WORKDIR {$path}` {$path} 工作目录路径

`WORKDIR`指令可以来指定工作目录(或者称为当前目录)，以后各层的当前目录就被改为指定的目录，如目录不存在是，`WORKDIR`会自动创建对应目录

`WORKDIR`指令使用相对路径，那么所切换的路径与之前的`WORKDIR`有关：

`WORKDIR /a`

`WORKDIR b`

`WORKDIR c`

`RUN pwd` 输出 /a/b/c路径

### ONBUILD
（https://yeasy.gitbook.io/docker_practice/image/dockerfile/onbuild）




