# 创建腾讯云CTSDB数据库
以下为初始化 CTSDB 实例的具体操作流程。

1. 登录 CTSDB 控制台，在实例列表可看到创建完成但未初始化的 CTSDB 实例。

2. 在需要初始化实例的【操作】列，单击【初始化】。
 
3. 在弹出实例初始化对话框，根据需要选择配置，完成后单击【确认】进行初始化。
 
4. 等待约两分钟后，实例状态转换为【运行中】，表明初始化完成，即可进行下一步的连接数据库操作。
 
CTSDB 实例目前仅提供 VPC 网络下的连接方式。您可以通过控制台连接实例，也可以通过 RESTful API 接口连接实例，通过 API 接口连接实例时需要提供 root 帐号的密码，以确保安全性。

CURL 连接实例创建表的示例如下，其中${user:password}是实例的用户名和密码，${vip}:${vport}是实例的 IP 和 Port，${metric_name}是新建的表名称。
```$xslt
curl -u ${user:password} -XPUT ${vip}:${vport}/_metric/${metric_name} -d'
{ 
    "tags": {
        "region": "string",
        "set":  "long",
        "host": "string"
    },
    "time": {
        "name": "timestamp",
        "format": "epoch_second"
    },
    "fields": {
        "cpu_usage":  "float"
    },
    "options": {
        "expire_day": 7,
        "refresh_interval": "10s",
        "number_of_shards": 5,
        "number_of_replicas": 1,
        "rolling_period": 1
    }
}'
```
# 腾讯云CTSDB数据库的安全性实现
以下是CTSDB提供的监控指标：
<table>
    <tr>
        <th>指标</th>
        <th>标识</th>
        <th>单位</th>
    </tr>
    <tr>
        <td>写入速度</td>
        <td>index_speed</td>
        <td>个/s</td>
    </tr>
    <tr>
        <td>平均磁盘使用率</td>
        <td>disk_usage_avg</td>
        <td>%</td>
    </tr>
    <tr>
        <td>数据当前写入总次数</td>
        <td>index_total</td>
        <td>次</td>
    </tr>
    <tr>
        <td>平均 JVM 内存使用率</td>
        <td>jvm_mem_usage_avg</td>
        <td>%</td>
    </tr>
    <tr>
        <td>最大 JVM 内存使用率</td>
        <td>jvm_mem_usage_max</td>
        <td>%</td>
    </tr>
    <tr>
        <td>平均 CPU 使用率</td>
        <td>cpu_usage_avg</td>
        <td>%</td>
    </tr>
    <tr>
        <td>最大 CPU 使用率</td>
        <td>cpu_usage_max</td>
        <td>%</td>
    </tr>
    <tr>
        <td>查询拒绝率</td>
        <td>search_rejected_completed_percent</td>
        <td>%</td>
    </tr>
    <tr>
        <td>写入拒绝率</td>
        <td>bulk_rejected_completed_percent</td>
        <td>%</td>
    </tr>
</table>

# 腾讯云数据库CTSDB常见问题

1.如何连接 CTSDB？

CTSDB 提供基于 HTTP 协议，以 JSON 为数据交互格式的 RESTful API 供用户访问，发送请求的同时需加上实例的用户名和密码。

2.CTSDB 操作起来简单吗？

是的，CTSDB 操作起来很简单。您可以在界面上查看到实例的详情，并能够进行相应的管控操作，包括初始化，修改实例名等，并且可以通过监控实时查看实例的健康状态。您可以通过 RESTful API 进行几乎所有类型的数据类操作。API 接口完全兼容 Elasticsearch 协议。

3.CTSDB 如何保证高性能查询？

通过实现一种倒排索引的算法来加速任意维度查询。

4.CTSDB 相比较关系型数据库的优势是什么？

在海量的时序数据场景下关系型数据库会存在如下问题：

(1)	存储成本大：对于时序数据压缩不佳，需占用大量机器资源。

(2)	维护成本高：单机系统，需要在上层人工的分库分表，维护成本高。

(3)	写入吞吐低：单机写入吞吐低，很难满足时序数据千万级的写入压力。

(4)	查询性能差：适用于交易处理，海量数据的聚合分析性能差。

(5)	这些问题上 CTSDB 优势如下：

(6)	存储成本低：利用时间递增、维度重复、指标平滑变化的特性，合理选择编码压缩算法，提高数据压缩比。通过数据上卷（Rollup），对历史数据做聚合，节省存储空间。

(7)	高并发写入：数据先写入内存，再周期性的 Dump 为不可变的文件存储，并且可以通过批量写入数据，降低网络开销。

(8)	低查询延时，高查询并发：优化常见的查询模式，通过索引等技术降低查询延时。通过缓存、routing 等技术提高查询并发。

5.基础网络 CVM 如何连接 CTSDB？

目前 CTSDB 只提供 VPC 网络的方式，如果您的 CVM 实例在基础网络，则可以通过基础网络与 VPC 互通操作来打通网络，连接 CTSDB。
