# 创建腾讯云MySQL数据库
<img src='../../../../Gallerys/tencentdb5-5.jpg'>
下面我们先介绍如何创建腾讯云MySQL数据库

## 1、创建实例
1.登陆云数据库MySQL控制台，在左侧导航栏选择【实例列表】页签，点击【新建】

2.根据实际需求，在创建实例页面配置各项信息，本页面也是购买页面，核对无误后点击【立即购买】，即可完成新建实例（注：购买完成后需要等待一定时间才可完成购买）：

(1)	计费模式：支持包年包月和按量计费。
* 若业务量有较稳定的长期需求，建议选择包年包月。
* 若业务量有瞬间大幅波动场景，建议选择按量计费。

(2)	地域：选择业务需要部署 MySQL 的地域。不同地域的云产品内网不通，购买后不能更换。

(3)	主可用区和备可用区：建议选择主备机处于同一可用区，避免网络延迟问题。

(4)	网络：云数据库 MySQL 所属网络，缺省设置为“Default-VPC（默认）”。

(5)	安全组：安全组创建与管理请参见云数据库安全组。

(6)	架构：提供基础版（基础版采用单个节点部署，价格低廉，性价比非常高）和高可用版（一主N从高可用架构，本地SSD 存储，性能强劲，易用便捷。适用于大中型企业的生产数据库）。

(7)	指定项目：选择数据库实例所属的项目，缺省设置为默认项目。

(8)	购买数量：每个用户在每个可用区可购买按量计费实例的总数量为10个。

## 2.初始化实例

1.在MySQL列表中点击实例初始化
<img src='../../../../Gallerys/tencentdb5-6.jpg'>
2.在弹出的对话框配置相关参数，点击确认大约等待50s后完成初始化

(1)	支持字符集：支持 LATIN1 、GBK、UTF8 、UTF8MB4 字符集，默认字符集编码格式是 LATIN1，即 ISO-8859-1 编码格式。初始化实例后，亦可在控制台实例详情页修改字符集。

(2)	表名大小写敏感：表名是否大小写敏感，默认为是。

(3)	自定义端口：数据库的访问端口，默认为3306。

(4)	设置root帐号密码：新创建的 MySQL 数据库的用户名默认为 root，此处用来设置该 root 帐号的密码。

(5)	确认密码：再次输入密码。
<img src='../../../../Gallerys/tencentdb5-7.jpg'>
返回sql列表中，当我们看到实例【状态】为【运行中】即完成了实例创建。创建完了实例后，我们来看一下如何访问已建好的MySQL，**访问数据库有两种方式**：

**内网访问**：使用云服务器 CVM 访问自动分配给云数据库的内网地址，这种访问方式使用内网高速网络，延迟低。CVM 和数据库须是同一账号，且同一个 VPC 内（保障同一个地域），或同在基础网络内。

**外网访问**：通过外网地址访问云数据库 MySQL。外网访问需要开启数据库实例的外网地址，此操作会使用户的数据库服务暴露在公网上，可能导致数据库被入侵或攻击。建议用户使用内网访问的方式来登录数据库。
访问数据库之前一定要确保cvm和数据库的网络是一致的，用户可在数据库管理后台将MySQL添加到相关服务器下，检测是否可以走通
<img src='../../../../Gallerys/tencentdb5-8.jpg'>

## 3、从Windows系统的CVM访问
1.登陆到Windows系统的CVM

2.下载一个标准的SQL客户端，下载地址：https://dev.mysql.com/downloads/workbench/
<img src='../../../../Gallerys/tencentdb5-9.jpg'>

3.界面将提示【Login】、【Sign Up】和【No, thanks, just start my download.】， 选择【No thanks, just start my download.】来快速下载。
<img src='../../../../Gallerys/tencentdb5-10.jpg'>

4.在此台 CVM 上安装 MySQL Workbench
(1)	此电脑上需要安装 Microsoft .NET Framework 4.5 和 Visual C++ Redistributable for Visual Studio 2015。

(2)	您可以单击 MySQL Workbench 安装向导中的【Download Prerequisites】，跳转至对应页面下载并安装这两个软件，然后安装 MySQL Workbench。
<img src='../../../../Gallerys/tencentdb5-11.jpg'>

5.打开 MySQL Workbench，选择【Database】>【Connect to Database】，输入 MySQL 数据库实例的内网（或外网）地址和用户名、密码，单击【OK】进行登录。

(1)	Hostname：输入内网（或外网）地址。在 MySQL 控制台 的实例详情页可查看内网（或外网）地址和端口号。若为外网地址，请确认是否已开启。

(2)	Port：内网（或外网）对应端口。

(3)	Username：默认为 root，外网访问时建议用户单独创建帐号便于访问控制管理。

(4)	Password：Username 对应的密码。
<img src='../../../../Gallerys/tencentdb5-12.jpg'>
6.登录成功的页面如图 所示，在此页面上用户可以看到 MySQL 数据库的各种模式和对象，您可以开始创建表，进行数据插入和查询等操作。
<img src='../../../../Gallerys/tencentdb5-13.jpg'>

## 4、从linux系统的cvm访问
1.登陆到linux系统的CVM

2.安装mysql：在Ubuntu中，默认情况下，只有最新版本的MySQL包含在APT软件包存储库中，要安装它，只需更新服务器上的包索引并安装默认包apt-get。命令行：

(1)	apt install mysql-server

(2)	安装完成后可以使用如下命令来检查是否安装成功

(3)	netstat -tap | grep mysql

(4)	通过上述命令检查之后，如果看到有mysql的socket处于LISTEN状态则表示安装成功

3.根据不同访问方式，对数据库进行访问

(1)	**内网访问**：
<ul>
    <li>执行如下命令登录到 MySQL 数据库实例。<br/>mysql -h hostname -u username -p<br/>hostname：替换为目标 MySQL 数据库实例的内网地址，在 MySQL 控制台 的实例详情页可查看内网地址。<br/>username：替换为默认的用户名 root。</li>
    <li>按照说明输入密码，连接成功提示如图</li>
</ul>
<img src='../../../../Gallerys/tencentdb5-14.jpg'>

(2)	**外网访问**：
<ul>
    <li>执行如下命令登录到 MySQL 数据库实例。<br/>
        mysql -h hostname -P port -u username -p<br/>
        hostname：替换为目标 MySQL 数据库实例的外网地址，在 MySQL 控制台 的实例详情页可查看外网地址和端口号。<br/>
        port：替换为外网端口号。<br/>
        username：替换为外网访问用户名，用于外网访问，建议用户单独 创建帐号 便于访问控制管理。
    </li>
    <li>在提示Enter password：后输入外网访问用户名对应的密码。</li>
</ul>

4.在MySQL \[(none)]>提示符下可以发送 SQL 语句到要执行的 MySQL 服务器




