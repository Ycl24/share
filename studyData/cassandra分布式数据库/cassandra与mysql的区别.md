### canssandra与mysql的区别

1. 数据类型
	- `cassandra`：是NoSQL数据库，支持各种数据，包括键值，文档和图形格式。它可以储存非结构化和半结构化的数据。它遵循对等体系结构。
	- `mysql`：是SQL数据库，支持储存不同表之间的相关数据，储存结构化数据，这些表可以通过多个键相互链接。它遵循主/从架构。

2. 可扩展性
	- `cassandra`: 同时支持水平和垂直缩放，由于集群节点模型的缘故，水平缩放是有可能的，数据在集群中的不同节点之间进行分区
	- `mysql`: 支持垂直扩展的支持，借助主/从复制和分片等其他方法，可以实现水平缩放。

3. 写作表现
 	- `cassandra`： 使用仅追加模型，插入和更新没有区别，如果插入与现有的数据存在时，则该数据被替换，如果更新一条不存在的数据，则创建该数据。
	-  `mysql`: curd操作都需要进行搜索，如果要使用表中已存在的主键更新记录。

4. 交易
与任何其他RDBMS数据库一样，MySQL提供ACID事务

`原子性`
`一致性`
`隔离`
`耐用性`

鉴于Cassandra在提供ACID交易方面存在局限性。为了提供高读取性能，您会看到有数据重复。一种实现一致性的方法是调整Cassandra不允许重复数据，但是这会杀死Cassandra的王牌-可用性。因此，对于高度基于ACID事务的系统（例如银行系统），选择任何NoSQL数据库都是不可行的。


总结

|比较基础  | cassandra  | mysql  |
|:----------|:----------|:----------|
| 数据库类型    | NoSQL，对等体系结构    | RDBS,主/从    |
| 可扩展性    | 水平和垂直可扩展   | 通过主/从复制或分片，可水平、垂直扩展   |
| 资料模型   | 不鼓励使用基于查询的模型的JOINS,每个查询从一个表中获取    | 多个表，查询数据需要关联    |
| 阅读表现    | O(1)   | 需要使用JOIN多个表中读取，结果为 O(log(n))    |
| 写性能    | 附加模型提供了高效的书写性能    | 写入首先需要搜索，这会降低写入性能    |
| 交易    | 未提供ACID属性，可以对其进行调整以支持ACID属性。    | 提供ACID交易   |