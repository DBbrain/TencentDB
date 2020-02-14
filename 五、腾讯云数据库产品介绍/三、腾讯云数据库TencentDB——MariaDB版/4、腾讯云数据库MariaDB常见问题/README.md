# 腾讯云数据库MariaDB常见问题
## 1、数据同步问题
1.任务校验失败

可能原因：

(1)	目标实例帐号密码有误。

(2)	网络不通，例如，防火墙或安全组没有放通同步工具出口 IP，当前不支持跨网络。

(3)	目标实例地实例不存在。

修复方法：检查上述原因做对应调整。

2.同步任务延迟较大

可能原因：

(1)	源端大事务造成。数据同步是从备机同步数据，因 binlog 中，每个事务的时间戳是事务的开始时间，当有大事务时，即便是并发同步，数据同步上报的时间戳仍然是事务的开始时间。

(2)	备机延迟。例如，DDL 重放，只读帐户造成备机压力过大等造成的备机延迟，会导致数据库同步的延迟。

修复方法：检查当前是否存在大事务、批处理。如果等待一段时间后仍然延迟较大，建议联系工作人员排查处理。

3.同步数据多了数据

可能原因：目标端没有锁写，被写入了部分数据；对应表可能缺少主键，当同步工具重发请求时，目标端重复写入部分数据；

修复方法：给源表增加主键，删除目标实例表中数据，重新同步。或手动删除缺少数据。

4.目标库可以写入数据

同步不会锁住目标库，目标库可以正常读写数据，因此请谨慎操作目标库。

5.DDL 重放失败

可能原因：源和目标数据库版本不同，DDL 语法有差异。

修复方法：手动在目标重新执行 DDL 语句。

6.数据库同步失败，对应表中没有任何数据或只有一部分数据

可能原因：修改了同步所需的帐号，或修改了目标库的表结构。

修复方法：暂停同步任务，修改对应帐号，并将目标库和源库表结构修改为一致后重新同步。

## 2、产品常见问题
1.MariaDB 是什么？

云数据库 MariaDB 定位于 OLTP 场景下高安全性的企业级云数据库，十余年来一直应用于腾讯计费业务，MariaDB 兼容 MySQL 语法，拥有诸如线程池、审计、异地容灾等高级功能，同时具有云数据库的易扩展性、简单性和性价比。

2.MariaDB 的典型应用场景有哪些？
* 场景1：数据云灾备（异地灾备）
* 场景2：业务系统上云
* 场景3：混合云
* 场景4：读写分离
* 场景5：开发测试

3.MariaDB 产品优势有哪些？
* 一致性
* 更高安全性
* 更强大的功能
* 更高可用性
* 更高性能
* 与 MySQL 兼容
* 便宜易用

4.MariaDB 分布式架构是什么样的？

分布式架构。

5.MariaDB 标准与认证是什么？

云数据库 MariaDB 现已代表腾讯云云数据库通过多项国家或国际认证，包括但不限于：

* 软件著作权
* ISO22301 认证
* ISO27001 认证
* ISO20000 认证
* ISO9001 认证
* 可信云服务认证
* 信息安全等级保护
* STAR 认证
* MariaDB 部分功能设计标准参考：
* GBT 20273-2006 信息安全技术数据库管理系统安全技术要求
* JRT 0072-2012 金融行业信息系统信息安全等级保护测评指南

6.为什么 MariaDB 指定某些存储引擎会报错？

MariaDB 初始化参数时优先匹配一致性要求，而某些存储引擎可能会引发数据不一致，所以您在建表时，某些存储引擎可能会报错，您可以通过SHOW ENGINES命令查看当前数据库支持的存储引擎。

7.新购买2GB内存的 MariaDB，为什么初始化后只有1GB左右缓存空间?

请参见 MariaDB 控制台对应实例的“参数设置”介绍，2GB会分配1GB给 SQL 执行的线程用，例如下图的临时表变量。
 
8.6GB内存的 MariaDB，max_tmp_size 参数最多只能调到60MB左右是为什么 ？

MariaDB 系统默认值是64MB，不建议调整太大。

9.MariaDB 什么都没有操作，为什么 CPU 利用率会达到50%？

因为腾讯云数据库架构设计，每隔5分钟会进行一次 binlog 日志和慢查询分析上传，所以每隔5分钟会有1分钟 CPU 利用率比较高。
控制台监控界面展示的是5分钟内的最大值，导致展示利用率很高，实际上并没有那么高。

10.为什么 MariaDB 的 CPU 利用率会超过100%？

MariaDB 默认使用闲时超用的策略，即允许您的业务抢占一部分额外的、空闲的 CPU 资源；因此，当您的实例超过默认给您分配的 CPU 核数时，CPU 利用率监控视图会显示超过100%，这个是正常的。
若您的 CPU 负载长期高于60%，则建议您尽快扩容数据库。

11.购买了16GB内存，MariaDB 监控上显示内存基本用完了，但业务未受影响，是怎么回事？

数据库的内存分配机制会尽可能的使用空余内存，以提高缓存命中，而不是从磁盘读取数据，因此，内存消耗殆尽是正常现象。一般来说，您只需要关心您业务性能是否被影响。

12.MariaDB 标准版（一主一从），为什么只看到一个 IP 地址？

从服务器不提供使用 IP 地址，您若需要可以购买只读实例 。

13.MariaDB 删除表中的内容，为什么可用磁盘空间并未因删除数据而增加？

删除数据不会释放物理可用空间（其他数据库类似），可用利用 percona-tookit 工具对需要的表做 alter table xxxx engine=innodb 操作。

14.MariaDB 下载链接只有15分钟，但数据量较大15分钟没下载完怎么办？

为确保下载链接安全，下载链接 URL 有效期仅15分钟；但如果已经开始下载，则下载时连接持续有效（复制无效）。

15.MariaDB 可用缓存空间为什么会逐渐下降，甚至到0或-1？

实际采集为 Innodb_buffer 的可用空间，因数据库通常采用 LRU 调度方案，正常情况下该值将趋于0，无需担心。请优先看缓存命中率是否过小，例如低于90%。
处理大事务时，该值可能为负，即数据库内存使用超过实际分配值。这是因为，我们为保证您的业务正常运行，在物理空间中空闲了部分允许您超用的内存，因此确实会超用。

16.MariaDB 在初始化时，字符集以及字节数设置完成可以更改吗？

字符集可以在参数设置中修改 character_set_server 或在建表时指定字符集；innodb_page_size 参数需要提交工单（重装实例）才能修改。

17.MariaDB 连接数过大会有什么问题，或新增业务请求无法正常连接到数据库，应该如何避免？

tdsql 运行客户端默认最大连接数4096，连接数满后，会导致新的连接被拒绝，请查看监控指标：活跃连接数和连接利用率。可根据以下几种情况分析：

* 如果客户端是短连接应用，请查看是否有连接未关闭的情况（这种情况活跃连接数通常是线性增长到4096），如果查询请求量等指标同步徒增，请查看是否有请求徒增的情况 。
* 如果客户端是长连接应用，请查看所有到 TDSQL 实例的连接池的连接数配置，如果监控指标中的连接利用率很低，说明连接池连接数配置偏大。

18.MariaDB 怎么知道读写分离确实走了从机，应该怎么验证？

可以查看从机查询数（SELECT），控制台有对应监控参数，使用后该值大于0。

19.MariaDB 监控的汇总、主节点和备节点数据有什么区别呢？为什么有些监控值明显不一样？

汇总数据是整个实例的监控数据汇总，可能是主节点或主从节点的数值之和等。主节点和备节点则是单个节点的数据，因此肯定是有不一样。

## 3、账号登陆问题

1. 内网如何访问 MariaDB 数据库？

内网访问：使用腾讯云中与 MariaDB 数据库网络相通的 CVM，来访问 MariaDB 数据库的内网地址。

2.外网如何访问 MariaDB 数据库？

外网访问：在外网的 Windows 或者 Linux 主机中，安装数据库客户端来访问腾讯云数据库 MariaDB 的外网地址。

3.MariaDB 数据库登录帐号密码忘记了，怎么办?

可通过MariaDB控制台实例详情页的“帐号管理”页，在“操作”列，选择【更多】>【重置密码】进行重置密码。

4.MariaDB 中的帐号权限，可以针对某个表的某个字段设置，设置成只写不读吗？

MariaDB 权限设置最小粒度为表，不是字段；此处与 MySQL 完全兼容。

5.MariaDB 读写分离，只读帐号方案，需要在程序上专门设置吗？

需要设置。在可以访问从库的位置，设置通过只读账号访问从库。

6.MariaDB 的读写分离，使用只读帐号发现没有函数的权限，也就是只读帐号没有调用自定义函数或存储过程，怎么更改？

只读帐号是没有执行存储过程和自定义函数的权限的，不支持进行更改。

## 4、功能相关问题

1.MariaDB 实例如何降配？

MariaDB目前不支持降低配置 。

2.MariaDB 如何重启？

您可以在控制台更改字符集重启数据库，但是不建议操作。

3.读写分离是否需要我在程序上进行专门设置？

数据库的读写分离，不是完全自动的，需要您先在管理中心开通只读帐号，并修改程序配置，方可生效。

4.MariaDB 如何有效地删除大量数据？

该方法类似于批量插入，建议清理的时候一次清理少一点，例如一万条，循环删除。

5.MariaDB 参数设置里，没有我需要修改的数据库参数或某些参数无法修改怎么办？

云数据库控制台支持大多数常见数据库参数，并对其设置了安全阈值，如果您需要修改的参数不存在，或无法修改为指定值，请提交工单，我们会尽快为您处理。

6.如何使用 mysqldump 导入 MariaDB 数据库数据？

使用 mysqldump 工具的优点是简单易用、容易上手，缺点是停机时间较长，因此它适用于数据量不大，或者允许停机的时间较长的情况。

7.MariaDB 数据库有哪些功能限制？

(1)	不能更改 mysql、information_schema、performance_schema 和 sysdb 库中的任何数据。

(2)	无法直接通过 SQL 语句进行帐号和赋权操作，只能通过管理控制台进行。

(3)	支持常见的19种权限，少量不常见权限不支持，具体支持的权限列表如下：

* SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、REFERENCES、INDEX、ALTER
* CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、CREATE VIEW、SHOW VIEW
* CREATE ROUTINE、ALTER ROUTINE、EVENT、TRIGGER、SHOW DATABASES

(4)	不提供超级管理员帐号。

(5)	采用 InnoDB 存储引擎，暂不开放其他存储引擎。

8.如何对 MariaDB 数据库进行回档？

通过数据库回档能力，可以最大程度地减少系统损失；MariaDB 可根据备份和日志保持情况，回档到30天内的任意时刻。

## 5、性能相关问题
1.为什么我的CPU利用率会超过100%？

MariaDB(TDSQL) 默认使用闲时超用的策略，即允许您的业务抢占一部分额外的空闲的 CPU 资源；因此，当您的实例超过默认给您分配的 CPU 核数时，您的 CPU 利用率监控视图会显示超过 100%，这个是正常的。若您的 CPU 负载长期高于 60%，则建议您尽快扩容数据库

2.我购买了16G内存，监控上显示内存基本用完了，但业务未受影响，是怎么回事？

数据库的内存分配机制会尽可能的使用空余内存，以提高缓存命中，而不是从磁盘读取数据，因此，内存消耗殆尽是正常现象。一般来说，您只需要关心您业务性能是否被影响。

3.MariaDB 每张表的数据量限制（即不影响正常读写效率）是多少？

建议2000万以下，大于这个值会对 MariaDB 性能造成影响。

4.MariaDB 支持用户自己搭建的一套数据库作为 MariaDB 从库吗？

MariaDB 提供两种方案为用户解决读写分离问题： 读写分离、自建只读实例。

5.MariaDB 数据源的连接方式需要变动吗？

MariaDB 本身兼容 MySQL 协议，也兼容 MySQL 协议的连接程序，无需特别修改。

6.MariaDB 审计已支持哪些语法？

数据库审计目前已经支持绝大多数 SQL 语句。

* 已支持 DCL、DDL、DML 语句的解析。
* Insert,Replace,Select,Union,Update,Delete,CreateDatabase:,CreateEvent,CreateFunction,CreateIndex,CreateLog,
* CreateTable,CreateServer,CreateProcedure,CreateTablespace,CreateTrigger,CreateView,CreateUDF,CreateUser,
* ShowCharset,ShowCollation,ShowColumns,ShowCreate,ShowCreateDatabase,ShowDatabases,ShowEngines,ShowErrors,
* ShowEvents,ShowFunction,ShowGrants,ShowLogEvents,ShowLogs,ShowProcedure,ShowOpenTables,ShowPlugins,
* ShowProcessList,ShowMasterStatus,ShowPrivileges,ShowProfiles,ShowSlaveHosts,ShowSlaveStatus,ShowTableStatus,
* ShowWarnings,ShowVariables,ShowStatus,ShowTriggers,Call,DropProcedure,DropDatabase,DropEvent,DropFunction,
* DropIndex,DropLogfile,DropServer,DropTables,DropTablespace,DropTrigger,DropUser,DropView,AlterDatabase,
* AlterEvent,AlterFunction,AlterLogfile,AlterProcedure,AlterServer,AlterTable,AlterTablespace,AlterUser,
* AlterView,Rollback,Commit,Begin,Set,SetTrans,SetPassword,Release,Grant,RenameTable,RenameUser,Revoke,Install,StopSlave,StartSlave,StartTrans,Use,DescribeTable,DescribeStmt,Flush,Load,LoadIndex,FlushTables,Reset,CacheIndex,TruncateTable,Lock,Unlock,SavePoint,Help,Do,SubQuery,ShowTables,Execute,Deallocate,Binlog,Kill,Partition,PrepareRepairXACheckCheckSumAnalyzeChangeOptimizePurgeHandlerSignalResignal
* 事务和存储过程会被拆分为多条语句。

## 6、备份相关问题
1.MariaDB 的备份功能支持实时备份吗？

目前暂不支持实时备份。

2.MariaDB 的备份方式有哪些？

云数据库支持全量备份和增量备份方式，详细介绍请参见

3.如何解压 MariaDB 的备份和日志文件？

MariaDB 的备份文件和日志文件（binlog 文件）采用 LZ4（Extremely Fast Compression algorithm）工具进行压缩，您可以选用 LZ4 工具进行解压。

## 7、运维相关问题

1.参数设置，没有我需要修改的数据库参数或某些参数无法修改怎么办？

腾讯云数据库管理中心支持大多数常见数据库参数，并对其设置了安全阈值，如果您需要修改的参数不存在，或无法修改为指定值，请提交工单，我们会尽快为您处理。

2.购买了华东地区（上海二区）的 CVM 和（上海）的 MariaDB，网络都为基础网络，但在 CVM 上面不能 ping 通 MariaDB，是为什么？

数据库默认是禁 ping 的。如果需要检测连通性，您可以使用 telnet 来进行测试。

3.MariaDB 的 navicat mysql 8.0.x 版本连接报错怎么办？

请选用最新版本进行连接。

4.MariaDB 的 binlog dump 是什么线程？

正常的主备同步线程，常驻线程。

5.MariaDB 使用的 axel 多线程下载，备份或日志文件下载出错 ？

运营策略原因，下载不支持多线程，可以使用wget --content-disposition命令下载。

6.为什么 MariaDB 主备延迟突然变大甚至到几分钟？

检查您是否可能跑了个非常大的 sql（例如：批量插入大量数据），因为需要等从机响应，所以看起来主备延迟大。

7.MariaDB 的 SELECT INTO OUTFILE 或 ./mysqldump 想把文件导出到本地，但句子报错，如何解决？

不支持用户写入文件到实例服务器的目录下。

8.MariaDB 的 SELECT FOR UPDATE 为什么会出现类似 READ ONLY 报错？

部分 SQL 语句不能使用读写分离，SELECT FOR UPDATE 是写操作，导致出错。

9.刚迁移到 MariaDB，就出现大量慢查询或性能问题，如何排查？

您可以先查看 MariaDB 控制台实例详情页的“性能优化” > “慢查询分析”，对出现的慢查询进行具体分析。可能有如下原因：

(1)	如果出现了连接不上的问题，可能是之前也有大量慢查询影响了性能，而出现只是 MariaDB 采用线程池机制控制了活跃线程的数量，所以非常明显的感觉到连接不上。

(2)	刚迁移过来，数据还未完全缓存到内存，部分数据需要从磁盘去拉取，消耗更长，也会感觉到性能下降。

10.MariaDB 的 XA_RBTIMEOUT 错误如何解决？

应该是有大事务引起了超大的 binlog，建议为需要用到的表增加一个自增字段。















