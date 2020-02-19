# MySQL数据迁移
数据传输服务 DTS 支持数据迁移功能，提供自建 MySQL 数据库到云数据库 TencentDB 的连续数据复制，用户可在不停服的情况下对数据进行在线热迁移，支持具有公网 IP/Port 或专线接入腾讯云的本地 IDC 或腾讯云 CVM 上 MySQL 数据库迁移。现已支持 MySQL 5.7 数据传输服务。

DTS 数据迁移任务分为冷备数据导出和增量数据同步两步，其中，冷备数据导出以及迁移后的数据对比过程会对源库负载产生一定的影响，建议在业务低峰期或在备库上做数据库迁移。指定库表迁移，如 lower_case_table_name 在迁移校验任务会检查源和目标的此项配置是否一致，不一致报错，会提前避免 lower_case_table_name 引起的重启问题。整实例迁移，迁移配置，如源实例一些必须要重启的参数（如 lower_case_table_name）与目标实例不同，设置需要重启目标实例。导入冷备，阿里云迁移重建主从， 需要重启目标实例。部分场景需要具有源实例的 super 权限。

大部分场景对源实例无 super 权限要求，仅以下场景中需具有源实例的 super 权限。
* 用户在“数据一致性检测”中选择了“全量检测”校验模式。
* 若在 binlog 同步过程中，用户在源实例创建了 Event，且这个 Event 指定了非用于 DTS 数据迁移的账号做 DEFINER，此时不具备 super 权限将会报错

支持迁移的内容有以下几点：
* 支持基础网络、VPC 网络的 CVM 自建 MySQL 数据库迁移至 TencentDB 实例。
* 支持具有公网 IP/Port 的 MySQL 数据库迁移至 TencentDB 实例。
* 支持 VPN 接入、专线接入腾讯云的 MySQL 数据库迁移至 TencentDB 实例。

## 1、迁移前预先检查项

在我们迁移前要有一些预先检查项：

1.检查目标 TencentDB 实例是否有同名库表，避免冲突。

2.检查数据库版本，可支持 MySQL 5.1/5.5/5.6/5.7 版本迁移上云；由于目前腾讯云 TencentDB 已不再支持 MySQL 5.1 版本，因此我们推荐您在迁移前完成 MySQL 5.1 升级到 MySQL 5.5，然后再迁移至 TencentDB for MySQL 5.5。

3.检查目标 TencentDB 实例容量必须大于源实例。

4.在源 MySQL 数据库上创建迁移账号（若有已授权可用于数据迁移的账号，也可不创建）。
```apacheconfig
GRANT ALL PRIVILEGES ON *.* TO "迁移账号"@"%" IDENTIFIED BY "迁移密码";
FLUSH PRIVILEGES;    
```
5.确认源库 MySQL 变量。

6.通过 SHOW GLOBAL VARIABLES LIKE 'XXX';查看 MySQL 全局变量，确认当前状态是否可以进行迁移：
```apacheconfig
server_id > 1      
log_bin = ON;            
binlog_format = ROW/MIXED           
binlog_row_image = FULL            
innodb_stats_on_metadata = 0            
wait_timeout 建议大于或等于3600秒，务必小于7200秒            
interactive_timeout 与wait_timeout设置相同时长            
如果源实例为slave角色，需要在源实例中确认以下参数：           
log_slave_updates = 1           
```

7.修改变量值。

8.修改源库 MySQL 配置文件my.cnf，需重启：
```apacheconfig
log-bin=[自定义binlog文件名]
```

9.b. 动态修改配置：
```apacheconfig
set global server_id = 99;                
set global binlog_format=ROW;              
set global binlog_row_image=FULL;                
set global innodb_stats_on_metadata = 0;
```
