## cassandra命令操作

### 键空间操作

1. 创建键空间

键空间是一个对象，用于保存列系列和用户定义的类型，键空间类似于RDBMS数据库, 其中包含列族, 索引, 用户定义的类型, 数据中心意识, 键空间中使用的策略, 复制因子等。

在cassandra中，`创建键空间`命令用于创建空间。

> create keyspace {$键空间名称} with {$属性}

比如

> create keyspace test with replication = {'class':'SimpleStrategy', 'replication_factor' : 3}; 

##### cassandra keyspace的不同组件 
- 简单策略：对于一个数据中心, 使用简单策略。在此策略中, 第一个副本放置在选定节点上, 其余节点在环中按顺时针方向放置, 而无需考虑机架或节点的位置。
- 网络拓扑策略：此策略用于多个数据中心的情况。在此策略中, 你必须分别为每个数据中心提供复制因子。

> 复制因子：复制因子是放置在不同节点上的数据的副本数。多于两个的复制因子可以使单点故障都没有。因此, 3是良好的复制因子。



> create keyspace test with replication = {'class':'SimpleStrategy', 'replication_factor' : 3}; 

 - class：keyspace的策略类型，默认SimpleStrategy
 - feplication_factor : 复制因数/副本数。 表示一份数据在一个DC 之中包含几份。常用奇数~ 比如我们项目组设置的  replication_factor=3
 - Replica placement strategy : 复制策略。 默认的是SimpleStrategy. 如果是单机架、单数据中心的模式，保持使用SimpleStrtegy即可。

2. 验证键空间是否存在
	
	1. 查看所有键空间

	> describe keyspaces
	
	2. 查看单个键空间
	
	> describe keyspace {$name}


3. 修改键空间

> alter keyspace test2 with replication =  {'class': 'SimpleStrategy', 'replication_factor': '1'}


4. 持久写入

默认情况下, 表的persistent_writes属性设置为true, 也可以将此属性设置为false。但是, 不能将此属性设置为单纯形策略。

例：

让我们以一个示例来看下耐用型写入属性的用法。

> CREATE KEYSPACE sssit
WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 3 }
 AND DURABLE_WRITES = false;

5. 删除键空间

> drop keyspace {$name}



### 键空间下表操作

复杂更新，对普集合类型字段进行更新，可对json格式的数据内部编辑，[可参考](https://blog.csdn.net/zhangcongyi420/article/details/119982931)

1. 进入键空间

> use {$name}

2. 创建表

> create table {$表名} ({$表字段和属性})

比如：

>  create table abc ( id int primary key, name varchar, age int );

3. 查看所有表列表

> describe tables


4. 查看表结构

> describe table {$表名称}

比如

> describe table abc

5. 创建数据

> insert into {$表名称} ($表字段) values ($表字段对应的值)

比如：

>  insert into abc (id,age,name) values (1,24,'帅哥');

这里反复添加相同的id,只name值不同，会造成覆盖id=1的数据
 
 - 创建带有过期时间的数据，并在过期之后自动删除
	
	- cassandra支持插入数据时候设定一个过期时间，只需要在插入语句后面加上TTL关键字进行标识即可

		> insert into {$表名称} ($表字段) values ($表字段对应的值) USING TTL {秒值}

		比如30秒之后，这条数据被自动删除了
		
		> insert into abc (id,age,name) values (1,24,'帅哥') USING TTL 30;

6. 编辑数据

> update {$表名} set {$字段值需要修改的字段名}={$修改值} where {$条件字段}={值}

比如:

>  update abc set age = 25 where id =3; 

cassandra和mysql不同的是，被更新的这一列数据如果不存在时，会添加进去



7. 查看数据

> select * from {$表名} {搜索条件}

比如:

> select * from abc;


8. 删除数据

> delete from {表名} {条件}

比如：

> delete from abc where id = 3;


9. 删除表

> drop table {表名}


10. 截断表（清空表数据）

> truncate {表名}

比如:

> truncate abc;

11. 创建表索引

> create index {键空间名} on {表名} ({字段})

比如:

> create index test on abc (age)

查看索引是否加上

> describe table abc

12. 删除索引

这里删除索引是对键空间下的所有表索引都删除

> drop index {键空间名}

比如

> drop index test

如何单独删除某一个表下的索引?


13. 批量处理sql

> begin batch {添加语句}; {编辑语句}; apply batch

比如:

>  begin batch insert into abc1 (id,age,name) values (3,24,'帅哥');  insert into abc1 (id,age,name) values (2,24,'帅哥'); apply batch;


14. 表添加字段

> alter table {表名} add {字段名} {字段属性}

比如:

> alter table abc add user_name text;

15. 删除表字段

> alter table {表明} drop {字段名}

比如:

> alter table abc drop user_name;


















