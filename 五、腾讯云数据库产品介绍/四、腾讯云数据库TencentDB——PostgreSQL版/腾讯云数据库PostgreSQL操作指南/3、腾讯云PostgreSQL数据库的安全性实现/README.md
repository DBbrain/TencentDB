# 腾讯云PostgreSQL数据库的安全性实现

为方便用户查看和掌握实例的运行信息，云数据库 PostgreSQL 提供了丰富的性能监控项，用户可登录 PostgreSQL 控制台 ，在对应实例管理页的【系统监控】页签查看。

监控指标：

<table>
    <tr>
        <th>指标中文名</th>
        <th>指标英文名</th>
        <th>指标含义</th>
        <th>指标单位</th>
    </tr>
    <tr>
        <td>CPU 利用率</td>
        <td>cpu</td>
        <td>实例 CPU 使用率，由于在闲时采用灵活的 CPU 限制策略，CPU 利用率可能大于100%</td>
        <td>%</td>
    </tr>
    <tr>
        <td>已用存储空间</td>
        <td>storage</td>
        <td>实例占用磁盘的可用空间</td>
        <td>GB</td>
    </tr>
    <tr>
        <td>磁盘 IOPS</td>
        <td>iops</td>
        <td>实例的 IOPS（每秒的请求次数)</td>
        <td>次/秒</td>
    </tr>
    <tr>
        <td>输入流量</td>
        <td>in_flow</td>
        <td>实例读写输入的流量</td>
        <td>KB/秒</td>
    </tr>
    <tr>
        <td>输出流量</td>
        <td>out_flow</td>
        <td>实例读写输出的流量</td>
        <td>KB/秒</td>
    </tr>
    <tr>
        <td>连接数</td>
        <td>connections</td>
        <td>实例的活跃连接历史变化趋势</td>
        <td>个</td>
    </tr>
    <tr>
        <td>请求数</td>
        <td>read_write_calls</td>
        <td>读写（增删改查）请求每分钟总数</td>
        <td>次/分钟</td>
    </tr>
    <tr>
        <td>读请求数</td>
        <td>read_calls</td>
        <td>读请求每分钟总数</td>
        <td>次/分钟</td>
    </tr>
    <tr>
        <td>写请求数</td>
        <td>write_calls</td>
        <td>写请求每分钟总数</td>
        <td>次/分钟</td>
    </tr>
    <tr>
        <td>其他请求数</td>
        <td>other_calls</td>
        <td>除了读和写以外的请求总数（如 Drop），按分钟累加</td>
        <td>次/分钟</td>
    </tr>
    <tr>
        <td>缓冲区缓存命中率</td>
        <td>hit_percent</td>
        <td>数据缓存命中率</td>
        <td>%</td>
    </tr>
    <tr>
        <td>平均执行时延</td>
        <td>sql_runtime_avg</td>
        <td>所有 SQL 请求的平均执行时间，不包含事务里面的 SQL</td>
        <td>ms</td>
    </tr>
    <tr>
        <td>最长 TOP10 执行时延</td>
        <td>sql_runtime_avg</td>
        <td>执行时间最长的 TOP10 的 SQL 的平均值</td>
        <td>ms</td>
    </tr>
    <tr>
        <td>最短 TOP10 执行时延</td>
        <td>sql_runtime_min</td>
        <td>执行时间最短的 TOP10 的 SQL 的平均值</td>
        <td>ms</td>
    </tr>
    <tr>
        <td>剩余 XID 数量</td>
        <td>remain_xid</td>
        <td>剩余的 Transaction Id 数量，Transaction Id 最大有2^32个，小于1000000建议手工执行 vacuum full</td>
        <td>个</td>
    </tr>
    <tr>
        <td>主备 XLOG 同步差异</td>
        <td>xlog_diff</td>
        <td>每分钟采样，主备 XLOG 的同步的大小差异，代表着同步的延迟，越小越好</td>
        <td>byte</td>
    </tr>
</table>
pg_stat_statements 模块：

您还可以通过 pg_stat_statements 视图查询 pg 详细性能指标。pg_stat_statements 模块提供追踪一个服务器所执行的所有 SQL 语句的执行统计信息的方法，可以用于统计数据库的资源开销，分析 TOP SQL。
```$xslt
select * from pg_stat_statements；
```









