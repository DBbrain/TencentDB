# 腾讯云SQLServer数据库的安全性实现
云数据库 SQL Serve 系统监控支持 SQL Server 常见的25种参数，您可以通过配置 SSMS 的计数器，额外统计其他参数。

**常见参数**：

<table>
    <tr>
        <th>指标</th>
        <th>描述（单位）</th>
        <th>五分钟指标取值</th>
        <th>指标优化建议</th>
    </tr>
    <tr>
        <td>CPU 利用率（Total Processor Time）</td>
        <td>实际 CPU 消耗的百分比（%）</td>
        <td>max</td>
        <td>< 30% 好< 60% 正常> 60% 需关注</td>
    </tr>
    <tr>
        <td>事务数（Transaction/sec）</td>
        <td>平均每秒的事务数（次/秒）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>连接数（User Connection）</td>
        <td>平均每秒用户连接数据库的个数（个）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>请求数（Batch Requests/sec）</td>
        <td>-（次/秒）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>每秒登录次数（Logins/sec）</td>
        <td>每秒登录次数（次/秒）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>每秒登出次数（Logouts/sec）</td>
        <td>每秒登出次数（次/秒）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>已用存储空间（Storage Space）</td>
        <td>实例数据库文件和日志文件占用的空间总和（GB）</td>
        <td>max</td>
        <td>< 30% 好< 60% 正常> 60% 需关注</td>
    </tr>
    <tr>
        <td>输入流量（Network Receive Throughput）</td>
        <td>所有连接输入包大小总和（MB/s）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>输出流量（Network Transmit Throughput）</td>
        <td>所有连接输出包大小总和（MB/s）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>磁盘 IOPS（IPOS）</td>
        <td>磁盘读写次数（次/秒）</td>
        <td>max</td>
        <td>根据实例规格中心 IOPS 建议< 30% 好< 60% 正常> 60% 需关注</td>
    </tr>
    <tr>
        <td>读取磁盘次数（Read IOPS）</td>
        <td>每秒读取磁盘次数（次/秒）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
    <tr>
        <td>写入磁盘次数（Write IOPS）</td>
        <td>每秒写入磁盘次数（次/秒）</td>
        <td>max</td>
        <td>参考业务需要</td>
    </tr>
</table>

**性能优化参数**：

<table>
    <tr>
        <th>指标</th>
        <th>描述（/单位）</th>
        <th>五分钟指标取值</th>
        <th>指标优化建议</th>
    </tr>
    <tr>
       <td>慢查询（SlowQuery）</td> 
       <td>运行时间超过一秒的查询数量（个）</td> 
       <td>avg</td> 
       <td>< 1 好< 10 正常> 10 需关注</td> 
    </tr>
    <tr>
       <td>阻塞数（Processes blocked）</td> 
       <td>当前阻塞数量（个）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>锁请求次数（Lock Requests/sec）</td> 
       <td>平均每秒锁请求的次数（次/秒）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>用户错误数（User Error/sec）</td> 
       <td>平均每秒错误数（次/秒）</td> 
       <td>avg</td> 
       <td>0 好> 0 需关注</td> 
    </tr>
    <tr>
       <td>锁等待次数（Lock waits）</td> 
       <td>每秒要求调用者等待的锁请求数（次/秒）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>SQL 编译数（SQL Compilation/sec）</td> 
       <td>平均每秒 SQL 编译次数（次/秒）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>SQL 重编译数（SQL Re-Compilation/sec）</td> 
       <td>平均每秒 SQL 重编译次数（次/秒）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>输入流量（Network Receive Throughput）</td> 
       <td>所有连接输入包大小总和（MB/s）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>输出流量（Network Transmit Throughput）</td> 
       <td>所有连接输出包大小总和（MB/s）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>每秒钟 SQL 做全表扫描数目（Full Scans/sec）</td> 
       <td>磁盘读写次数（次/秒）</td> 
       <td>avg</td> 
       <td>根据实例规格中心 IOPS 建议< 30% 好< 60% 正常> 60% 需关注</td> 
    </tr>
    <tr>
       <td>缓存区缓存命中率（Buffer cache hit ratio）</td> 
       <td>数据缓存（内存）命中率（%）</td> 
       <td>avg</td> 
       <td>≥ 95% 好≥ 90% 正常< 90% 需关注</td> 
    </tr>
    <tr>
       <td>每秒闩锁等待数量（Latch Waits/sec）</td> 
       <td>每秒闩锁等待数量（次/秒）</td> 
       <td>avg</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>平均锁等待延迟（Average Wait Time）</td> 
       <td>每个导致等待的锁请求的平均等待时间（ms）</td> 
       <td>max</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>平均网络 IO 延迟（Network IO waits）</td> 
       <td>平均网络 IO延迟时间（ms）</td> 
       <td>max</td> 
       <td>参考业务需要</td> 
    </tr>
    <tr>
       <td>执行缓存缓存命中率（Plan Cache：Cache Hit Ratio）</td> 
       <td>每个 SQL 有一个执行计划，执行计划的命中率（%）</td> 
       <td>max</td> 
       <td>≥ 95% 好≥ 90% 正常< 90% 需关注</td> 
    </tr>
</table>

**说明**：由于 SQL Server 的内存为全部占满使用的机制，因此无需监控直接内存容量指标，可以通过缓存命中率来查看内存使用情况。

目前支持关键系统性能指标的告警，其他指标暂不支持通过腾讯云云监控告警，您可以在云监控控制台 的【告警配置】>【告警策略】里面配置告警能力。目前已经支持对以下监控指标进行告警。
<ul>
    <li>CPU 利用率</li>
    <li>连接数</li>
    <li>输入流量</li>
    <li>输出流量</li>
    <li>IOPS</li>
    <li>存储空间</li>
</ul>