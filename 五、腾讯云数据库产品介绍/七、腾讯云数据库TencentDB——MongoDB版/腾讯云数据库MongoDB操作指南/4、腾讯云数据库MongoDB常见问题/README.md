# 腾讯云数据库MongoDB常见问题
## 1、功能特性问题

1.如何获取实例的慢日志？

可使用慢日志查询功能中获取慢查询详情。

2.MongoDB 是否支持外网访问？

暂不支持，用户如有外网需求，需要直接搭建代理，购买 CVM，通过内网的方式进行访问。

3.MongoDB 是否支持无密码访问？

基于安全原因，MongoDB 不支持无密码访问。

4.如何设置从库 dump？

在 mongodump 的参数中设置 readPreference=secondaryPreferred。

5.云数据库 MongoDB 是否支持动态添加 Secondary 节点？

目前暂不支持。

6.oplog 大小是多少，是否支持调整？

oplog 大小默认为实例容量的10%，用户可在控制台调整其大小，最小为实例容量的10%，最大为实例容量的90%。

7.购买的容量是否包含 oplog？

由于 oplog 存在 MongoDB 数据库内部，所以会占用实例的购买容量，默认是10%。

8.当前开放了哪些角色权限？

当前只开放 RoleDBAdminAny和RoleReadWriteAny 两种角色的权限，暂时不开放 root 权限，后续会逐步放开一些权限，以及开放更多便捷实用的管理控制台功能来代替某些特殊权限的调用。

9.磁盘使用率达到100%会发生什么？

此时实例将处于封禁状态，该状态下不可写入数据，只能做读操作，尝试写入数据的连接将会被关闭。请及时关注自身业务发展和实例使用情况，当容量使用达到一定阈值时请适当扩容。

10.MongoDB 的监控里内存占用比很高？

MongoDB 采用一种贪婪策略会尽量分配可用的内存用作缓存，以提高性能。

11.MongoDB 目前支持哪些引擎？

目前支持 WiredTiger 和 Rocks 两种引擎。

12.MongoDB 是否支持维护时间窗？

目前暂不支持。

13.为什么 MongoDB 删除数据后没有回收空间？

除了直接删除 db 或者表，其他情况下删除数据 MongoDB 并不会回收空间。

## 2、分片集群问题

1.MongoDB 是否支持分片（sharding）？

支持。

2.什么是 MongoDB 分片集群？

云数据库 MongoDB 目前已经支持分片功能。
* 分片集群将数据按照片键分布存储在多台物理机上，平滑的扩展能力，非常适用于 TB 或 PB 级的数据存储场景。
* 分片集群支持实例级别的备份和回档来保证数据高可靠。每个分片内采用多节点自动容灾的机制，保证服务高可用。
* 可以使用腾讯云 MongoDB 分片功能便捷高效的搭建海量分布式存储系统。

3.如何创建 MongoDB 分片集群？

登录MongoDB 购买页 ，在【实例类型】选择【分片集群】，按需选择分片的片数，片内节点数，以及节点规格。
每个分片都是多节点的副本集，片内多节点自动容灾，保证服务高可用。

4.如何查询 MongoDB 分片集群的信息？

在控制台中可以查看分片集群实例的详细信息，如分片的构成，片节点的规格和已使用容量，同时也可以在控制台上进行实例的续费管理以及扩容等操作。

5.MongoDB 分片集群扩容方式有哪些？

目前只支持将所有节点进行统一扩容，暂不支持通过添加节点的方式进行扩容。
在控制台实例列表页单击【扩容】，选择需要扩到的容量规格，单击【升级】。

6.MongoDB 如何实现分片集群实例监控？

云数据库 MongoDB 分片集群实例提供三个维度的监控指标，来进行整个集群的数据监控。
* 实例维度
* 片维度
* 节点维度

提供操作请求，容量使用，负载等多项指标的监控数据，可在实例的【系统监控】页查看。

7.MongoDB 的分片策略是什么？

* 支持 hash key 的分片机制。
* 支持联合字段的 shard key。
* 分片实例下所有数据集合必须使用分片，建议把不分片的数据放到单独的副本集实例下。

8.MongoDB 分片认证机制是什么？

MongoDB 完全兼容支持 SCRAM-SHA-1 和 MONGODB-CR 两种机制。

## 3、实例相关问题
1.MongoDB 如何查看实例详情？

在实例列表，单击实例名可以进入详细信息页面查看实例详情。

2.如何访问 MongoDB 实例？

云数据库 MongoDB 提供多种语言连接方式，如 Shell，PHP，Node.js，Java，Python。

3.MongoDB 的实例范围是多少？是否支持升级连接数？

连接数和实例规格相关，可以通过升级规格以获取更大的连接数。

4.MongoDB 的升级实例规格花费时间与实例已用容量有关吗？

升级实例规格所需的时间取决于实例已用容量，升级期间实例会发生一次切主，切主期间会出现短暂的不可访问，大约十秒左右。

5.MongoDB 创建实例的流程？

可通过购买页按需选择规格大小和时长，单击【立即购买】创建实例。

6.如何在项目中查找 MongoDB 已分配项目的实例？

查找已分配项目的实例，可参考接口 DescribeMongoDBInstances 查询副本集实例列表。

7.MongoDB 实例的连接数规格是多少？是否支持升级连接数？

连接数和实例规格相关，可以通过升级规格以获取更大的连接数。

8.MongoDB 如何得到实例的慢查询？

请使用官网的慢查询功能中获取慢查询详情。

9.MongoDB 查询可创建的实例规格?

可以通过DescribeMongoDBProduct接口查询可创建的实例规格。

## 4、回档备份问题

1.MongoDB 每日自动备份和手动备份如何操作？

云数据库 MongoDB 支持两种备份方式，一种是每日自动备份，一种是手动备份。备份数据默认保留7天。
* 自动备份
实例可提供每天一次的自动备份，您可以在腾讯云 MongoDB 控制台 实例详情页的【回档与备份】中查看。
* 手动备份
在实例详情页的【回档与备份】中，单击右上角的【手动备份】，在弹框中输入备份的备注，提交后即可完成手动备份。

2.是否支持备份文件下载？

目前暂不支持。

3.回档后进行了替换操作，是否还可以再次进行回档？

不可以，替换后原备份文件已经不再适用于替换后实例，无法再次进行回档，用户选择替换操作前务必确认。

4.MongoDB 回档的时间取决于什么？

回档是基于最近的一次全量备份的镜像 +oplog 进行的，回档的时间取决于回放 oplog 的量。
如果全量备份的时间点距离回档的时间很久，就需要较长的时间进行 oplog 的回放。

5.MongoDB 回档后，转正操作和替换操作的区别？

转正是把回档后的临时实例转化为一个全新的实例运行，该实例与原实例无任何对应关系。默认会为临时实例设置2天的有效期，请及时续费。
替换是用临时实例数据覆盖当前实例的数据，替换后实例的备份文件会删除，不可再次回档到替换之前，请谨慎操作。

6.MongoDB 回档后进行了替换操作，是否会删除备份文件？

替换后，原备份文件已经不再适用于替换后实例，因此回档过程会删除备份文件。

7.MongoDB 如何完成副本集实例的备份回档？

副本集实例目前支持实例级别和库表级别的备份和回档。
* 备份
在控制台实例详情页单击【手动备份】，或在备份与回档页进行自动备份设置。
* 回档
在回档操作过程中，需要输入需要回档到的日期，目前支持5日内的任意时间回档，但前提是只能选择两次备份（成功且非 oplog 写满状态）之间的时间点进行回档，如果没有满足的备份请执行一次 手动备份 。

8.MongoDB 如何完成分片集群实例的备份回档？

分片集群实例目前支持实例级别的备份和回档。
* 备份
在控制台实例详情页单击【手动备份】，或在备份与回档页进行自动备份设置。
* 回档
实例的 oplog 空间为固定集合（Capped Collection），当集合空间用完后，再插入的元素就会覆盖最初始的头部的元素。为了避免 oplog 空间被覆盖导致备份和恢复失败，请根据业务详情合理设置 oplog 空间大小。当业务写入、删除和更新操作频繁时，为了防止两次备份时间点之间的 oplog 被覆盖，可以设置每天备份多次。两次备份时间点之间的 oplog 空间被覆盖，可能无法保证数据恢复的时间点。

## 5、连接相关问题

1.MongoDB 连接断开怎么操作？

请参见连接示例排除认证问题。

2.MongoDB 出现“Remote server has closed the connection”信息？

首先参见连接示例排除认证问题 ，如果能连上但是依然会出现这个问题，可能需要实现一个重连机制。

3.WiredTiger 3.2 存在锁表问题，云版本 MongoDB 是否存在类似问题？

需要根据具体问题分析，例如默认建索引肯定会加全局锁，以及用户执行 fsynclock 命令也是会加锁的。
锁是数据库的一个功能，处理并发访问的一系列问题，正常的加锁是必须的，只要不影响业务正常运行就可以。

4.MongoDB 应该选哪个版本的驱动程序？

推荐使用最新版本，例如 PHP 可以选择 mongo-1.6 及以上。

5.MongoDB 提供哪些语言连接方式？

云数据库 MongoDB 提供多种语言连接方式，例如 Shell、PHP、Node.js、Java、Python。

6.云数据库 MongoDB 版支持哪些语言的客户端进行连接？

云数据库 MongoDB 版针对客户端连接完全兼容 MongoDB，只要是官方 MongoDB 版支持的客户端，
云数据库全部支持。例如 C、C++、C#、Java、Node.js、Python、PHP、Perl 等。

7.用 meteor 等各类框架、类库无法连接腾讯云 MongoDB，如何处理？

一般来说都是连接方式、URI 拼接错误，请先检查核实。

8.在 PHP 中，如何设置 MongoDB 最大连接数？

MongoDB 驱动可以通过在连接 URL 中配置 maxPoolSize 参数控制连接数。
MongoDB 驱动可以通过 Mongo::setPoolSize() 方法设置连接数。

9.MongoDB 连接数限制是多少？

<table>
    <tr>
        <th>内存规格</th>
        <th>连接数个数上限</th>
    </tr>
    <tr>
        <td>2GB</td>
        <td>1500</td>
    </tr>
    <tr>
        <td>4GB</td>
        <td>1500</td>
    </tr>
    <tr>
        <td>6GB</td>
        <td>1500</td>
    </tr>
    <tr>
        <td>8GB</td>
        <td>1500</td>
    </tr>
    <tr>
        <td>12GB</td>
        <td>1500</td>
    </tr>
    <tr>
        <td>16GB</td>
        <td>2500</td>
    </tr>
    <tr>
        <td>24GB</td>
        <td>3500</td>
    </tr>
    <tr>
        <td>32GB</td>
        <td>4500</td>
    </tr>
    <tr>
        <td>48GB</td>
        <td>6000</td>
    </tr>
    <tr>
        <td>64GB</td>
        <td>9000</td>
    </tr>
    <tr>
        <td>128GB</td>
        <td>15000</td>
    </tr>
    <tr>
        <td>240GB</td>
        <td>15000</td>
    </tr>
    <tr>
        <td>512GB</td>
        <td>15000</td>
    </tr>
</table> 

连接数上限是针对实例级别的，不是节点级别的。

10.手动重连 MongoDB 怎么操作？

腾讯云 MongoDB 数据库服务提供的不是简单的 mongod 访问，给到用户访问的是一个负载均衡 IP，此 IP 后面是连接到一系列类似 mongos 一样存在的路由接入层。
客户端驱动会透过负载均衡 IP 与接入机建立一个长连接，当此连接处于长期间活跃状态时，腾讯云不会对其做任何干预，但是当长连接闲置时间超过1天时（此时间会随着版本优化而调整），路由接入层会踢掉该连接。
一般来说，客户端驱动会实现一个自动重连的过程，但是也有部分语言的驱动并没有实现。对于没有实现自动重连的语言驱动，当用户使用一个已经被踢掉的连接来尝试与腾讯云 MongoDB 服务通信时可能会得到 “Remote server has closed the connection” 之类的错误信息，所以需要手动进行重连，这里给出一个 PHP 重连的 demo。
基于 PHP mongo 驱动的重连实现
 
11.如何使用 mongoose 连接云数据库 MonogoDB？

mongoose 连接腾讯云 MonogoDB 参数如下：
```apacheconfig
var dbUri = " mongodb:// " + user + " : " +password + " @ " +host + ":" +port + " / " + dbName;
var opts = {
auth：｛
        authMechanism : ' MONDODB-CR'
    ｝
};
var connection = mongodb.createConnection(dbUri, opts);
```

12.MongoDB 支持外网连接吗？

MongoDB 目前只支持内网连接。暂不支持开通外网访问，如果您要在本地连接 MongoDB，可以使用与 MongoDB 同一账号同一内网下的服务器做端口转发实现。

## 6、数据迁移问题

1.从 MongoDB 数据库中导出数据，如何设置参数？

mongodump 的参数中设置 --readPreference=secondaryPreferred。

2.MongoDB 支持哪些数据迁移？

目前支持两类迁移：云数据库 CVM 自建实例迁移、外网实例迁移。

3.使用 mongodump（整库）或者 mongoexport（单个集合），如何把 MongoDB 的数据导出到本地？

在CVM 中可用 MongoDB 提供的 shell 客户端连接云数据库 MongoDB 进行数据导出，请注意使用最新版本的 MongoDB 客户端套件。
MongoDB 官方提供了两套数据导出工具，一般来说，进行整库导出时使用 mongodump，操作的数据是 BSON 格式，进行大量 dump 效率较高；进行单个集合导出时使用 mongoexport，操作的数据是 JSON 格式，可读性较高。
(1)	使用 mongodump 进行整库导出备份，导出命令如下：
 ```apacheconfig
mongodump --host 10.66.187.127:27017 -u mongouser -p thepasswordA1 --authenticationDatabase=admin --db=testdb -o /data/dump_testdb
```
(2)	使用 mongoexport 进行单个集合导出备份，导出命令如下：
```apacheconfig
mongoexport --host 10.66.187.127:27017 -u mongouser -p thepasswordA1 --authenticationDatabase=admin --db=testdb --collection=testcollection  -o /data/export_testdb_testcollection.json
说明：您也可以加上 -f 参数指定需要的字段，-q 参数指定一个查询条件来限定要导出的数据。
```
(3)	关于 rwuser 和 mongouser 用户名在写导出命令时的参数说明

在连接示例文档中有说明，腾讯云 MongoDB 默认提供了 rwuser 和 mongouser 两个用户名，分别支持 MONGODB-CR 和 SCRAM-SHA-1 两种认证方式。
* 对于 mongouser 以及在控制台创建的所有新用户，在使用导出命令工具时按照上文示例操作即可。
* 对于 rwuser，需要在每个命令里加入参数 --authenticationMechanism=MONGODB-CR 。

mongodump 示例说明：
```apacheconfig
mongodump --host 10.66.187.127:27017 -u rwuser -p thepasswordA1 --authenticationDatabase=admin --authenticationMechanism=MONGODB-CR --db=testdb -o /data/dump_testdb
```
4.使用 mongorestore（整库）或者 mongoimport（单个集合），如何把数据从本地导入到 MongoDB？

在 CVM 中可用 MongoDB 提供的shell 客户端 连接云数据库 MongoDB 进行数据导入，请注意使用最新版本的 MongoDB 客户端套件。

MongoDB 官方提供了两套数据导入工具，一般来说，进行整库导出时使用 mongorestore，操作的数据是 BSON 格式，进行大量 mongorestore 效率较高；进行单个集合导出时使用 mongoimport，操作的数据是 JSON 格式，可读性较高。

(1)	使用 mongorestore 进行整库导入备份,导入命令如下：
```apacheconfig
mongorestore --host 10.66.187.127:27017 -u mongouser -p thepasswordA1 --authenticationDatabase=admin --dir=/data/dump_testdb
```
(2)	2. 使用mongoimport进行单个集合导入备份，导入命令如下：
```apacheconfig
mongoimport --host 10.66.187.127:27017 -u mongouser -p thepasswordA1 --authenticationDatabase=admin --db=testdb --collection=testcollection2  --file=/data/export_testdb_testcollection.json
```
(3)	关于 rwuser 和 mongouser 用户名在写导入命令时的参数说明

在 连接示例 文档有说明，腾讯云 MongoDB 默认提供了 rwuser 和 mongouser 两个用户名，分别支持 MONGODB-CR 和 SCRAM-SHA-1 两种认证方式。

* 对于 mongouser 以及在控制台创建的所有新用户，在使用导入命令工具时按照上文示例操作即可。
* 对于 rwuser，需要在每个命令里加入参数 --authenticationMechanism=MONGODB-CR。

用 mongorestore 示例：
```apacheconfig
mongorestore --host 10.66.187.127:27017 -u rwuser -p thepasswordA1 --authenticationDatabase=admin --authenticationMechanism=MONGODB-CR --db=testdb -o /data/dump_testdb
```
5.为什么数据导入到 MongoDB 实例后，占用空间比自建的 MongoDB 小？

可能存在以下几个原因：
* 原始数据库长时间运行积累了大量的增删改操作。
* 写操作时 MongoDB 出于性能考虑在空间分配时分配了大于实际数据的空间。
* 删除数据后原空间没有被再次利用。
* 综合下来导致整个数据库空间的空洞率较高，而导入数据时相当于做了一次类似磁盘整理的操作，使导入后的数据保存得相对紧凑，所以看起来数据变小了。






