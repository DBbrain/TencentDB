# 创建腾讯云MongoDB数据库
## 1、初始化实例操作
下面介绍通过云数据库 MongoDB 控制台初始化实例的操作：

1. 登录 MongoDB 控制台，在实例列表选择状态为【待初始化】的实例，单击【初始化密码】。
2. 在弹出的对话框，设置 mongouser 和 rwuser 的密码，单击【确认】。
3. 返回实例列表，待实例状态变为【运行中】即可正常使用。

## 2、内网访问
实例初始化后，可以通过 MongoDB shell 或者各语言驱动进行访问数据库，进行各种管理操作。需要使用 CVM 通过内网进行访问，暂不支持外网访问方式。

连接腾讯云 MongoDB 服务最低驱动版本需要3.2，请尽量使用最新版的客户端驱动以保证最好的兼容性，包括 Shell 套件、Java jar 包、PHP 扩展、Node.js 模块等，具体请参见 MongoDB 官网驱动介绍。

## 3、JavaScript Shell 交互连接
mongo shell 是 MongoDB 自带的一种交互式 JavaScript shell。可在 shell 中使用命令行与 MongoDB 实例交互。您可以使用 mongo shell 查询和更新数据或执行管理操作。mongo shell 是 MongoDB 发行版的一部分，您需要先下载或者安装 MongoDB，然后再使用 mongo shell 连接至您的 MongoDB 实例。具体连接步骤如下：
```$xslt
1. cd <mongodb installation dir>
2. ./bin/mongo -umongouser -plxh2081* 172.16.0.56:27017/admin
```
**说明**：上例中，-u 参数指定用户名，-p 参数指定密码，172.16.0.56和27017分别指定 MongoDB 实例的 IP 和端口。

## 4、传参方式连接
1.	传统方式
MongoDB 服务可以用传统的传参的方式进行连接，同时大部分的驱动程序也支持 URI 形式进行连接。MongoDB 官方推荐使用 URI 的方式连接 MongoDB 服务。典型的 URI 如下：
* 例1 mongodb://username:password@IP:27017/admin
* 例2 mongodb://username:password@IP:27017/somedb?authSource=admin
* 例3 mongodb://username:password@IP:27017/somedb?authSource=admin&readPreference=secondaryPreferred

URI 组成的各个部分解释如下：

<table>
    <tr>
        <th>组成部分</th>
        <th>含义</th>
        <th>是否必须</th>
    </tr>
    <tr>
        <td>mongodb://</td>
        <td>一个特定的字符串，表示 MognoDB 协议</td>
        <td>必须</td>
    </tr>
    <tr>
        <td>username</td>
        <td>用于登录 MongoDB 服务的用户名</td>
        <td>必须，参见本页 默认用户名</td>
    </tr>
    <tr>
        <td>password</td>
        <td>用于登录 MongoDB 服务的用户密码</td>
        <td>必须</td>
    </tr>
    <tr>
        <td>IP:27017</td>
        <td>MongoDB 服务 IP 和端口</td>
        <td>必须</td>
    </tr>
    <tr>
        <td>/admin</td>
        <td>要认证的数据库，腾讯云 MongoDB 固定为 admin</td>
        <td>必须，参见本页 认证数据库</td>
    </tr>
    <tr>
        <td>authMechanism=MONGODB-CR</td>
        <td>认证机制</td>
        <td>参见本页 认证机制</td>
    </tr>
    <tr>
        <td>authSource=admin</td>
        <td>身份认证所用库，腾讯云 MongoDB 固定为 admin</td>
        <td>必须，参见本页 认证数据库</td>
    </tr>
    <tr>
        <td>readPreference=secondaryPreferred</td>
        <td>可以设置优先读从库</td>
        <td>非必须，参见本页 读操作的主从优先级</td>
    </tr>
</table>

这里只是列举了一部分 MongoDB 连接 URI 的参数。

根据腾讯云 MongoDB 发布版本而异，对于最新生产的实例，我们內建了 “rwuser” 和 “mongouser” 两个默认用户。较老实例只有 rwuser（对于这批实例我们会进行升级，升级之前会联系您）。您也可以使用腾讯云 MongoDB 控制台进行账号和权限管理以满足您的业务需要。

2.rwuser（MONGODB-CR 认证）URI 示例
rwuser 是唯一使用 MONGODB-CR 认证的用户
```$xslt
mongodb://rwuser:password@10.66.100.186:27017/admin?authMechanism=MONGODB-CR
或者
mongodb://rwuser:password@10.66.100.186:27017/somedb?authMechanism=MONGODB-CR&authSource=admin
```

3.mongouser（SCRAM-SHA-1 认证）URI 示例
mongouser 以及在云控制台创建的用户都使用 SCRAM-SHA-1 认证
```$xslt
mongodb://mongouser:password@10.66.100.186:27017/admin
或者
mongodb://mongouser:password@10.66.100.186:27017/somedb?authSource=admin
```
4.腾讯云 MongoDB 统一使用 “admin” 库作为登录鉴权的认证数据库，所以在 URI 中端口后面必须加上“/admin”以指定认证库，通过认证以后再切换到具体业务数据库进行读写操作，URI 示例：
```$xslt
mongodb://username:password@IP:27017/admin
```

5.当然，也可通过直接指定读写目标数据库和额外的认证库参数（authSource=admin）来直达目标数据库，URI 示例：
```$xslt
mongodb://username:password@IP:27017/somedb?authSource=admin
```
综上，您必须选择一种方式将 admin 作为认证库代入 URI 中。

MongoDB 支持多种认证机制，目前官方推荐的是 “SCRAM-SHA-1”。
腾讯云 MongoDB 支持 “MONGODB-CR” 和 “SCRAM-SHA-1” 两种认证方式。
前文说道，腾讯云 MongoDB 内建了两个默认用户 “rwuser” 和 “mongouser”，同时还可以在腾讯云 MongoDB 控制台创建额外的用户，这些用户被分成了两类，分别采用不同的认证机制，分类如下：

<table>
    <tr>
        <th>用户名</th>
        <th>认证机制</th>
        <th>URI 处理</th>
    </tr>
    <tr>
        <td>rwuser</td>
        <td>MONGODB-CR</td>
        <td>必须加上参数 “authMechanism=MONGODB-CR”</td>
    </tr>
    <tr>
        <td>mongouser 以及在云控制台创建的用户</td>
        <td>SCRAM-SHA-1（推荐）</td>
        <td>不用加任何参数</td>
    </tr>
</table>

腾讯云 MongoDB 提供了一个负载均衡 IP 用于访问整个副本集，如果需要指定访问从库读请在 URI 里设置 “readPreference” 参数，具体取值含义如下：

<table>
    <tr>
        <th>取值</th>
        <th>含义</th>
        <th>是否默认</th>
    </tr>
    <tr>
        <td>primary</td>
        <td>只读主节点</td>
        <td>是</td>
    </tr>
    <tr>
        <td>primaryPreferred</td>
        <td>主节点优先，如主节点不可用，则读从节点</td>
        <td>否</td>
    </tr>
    <tr>
        <td>secondary</td>
        <td>只读从节点，如从节点不可用会报错</td>
        <td>否</td>
    </tr>
    <tr>
        <td>secondaryPreferred</td>
        <td>从节点优先，如从节点不可用，则读主节点</td>
        <td>否</td>
    </tr>
</table>

设置优先读取从节点可以按示例拼接 URI：
```$xslt
mongodb://username:password@IP:27017/admin?readPreference=secondaryPreferred
```




