# 创建腾讯云SQLServer数据库
## 1、创建实例
1.登录云数据库SQL Server控制台，在实例列表页单击【新建】。

2.在购买页中，选择数据库相关配置，确认无误后，单击【立即购买】。
<ul>
    <li>计费模式：支持包年包月和按量计费。</li>
    <li>地域和可用区：地域说明请参见地域和可用区。</li>
    <li>网络类型：支持基础网络和私有网络。</li>
    <li>数据库版本：支持 SQL Server 2008、SQL Server 2012、SQL Server 2016、SQL Server 2017 Enterprise 和 Standard 版本。不同可用区版本有所不同，具体以实际情况为准。</li>
    <li>选择实例规格和所需的硬盘。</li>
    <li>选择购买数量和购买时长。</li>
</ul>
3.购买后返回实例列表，查看创建的实例，待运行状态显示为【运行中】，表示实例创建成功。

## 2、创建帐号
1.单击实例名或操作列的【管理】，进入实例管理页面。

2.选择【帐号管理】>【创建帐号】，在弹出的对话框填写相关信息，确认无误后单击【确定】。

3、创建完之后，我们来一起看一下如何连接到sql server，这里将介绍两种方式进行连接。

## 3、从 Windows 云服务器连接 SQL Server 实例

1.登录云数据库 SQL Server实例详情页，查看实例内网 IP 及端口号。该内网 IP 及端口号会在连接云数据库时使用。

2.登录腾讯云 Windows 云服务器。本文以 Windows Server 2012 R2 标准版64位中文版为例。

3.在 Windows 云服务器中下载并安装SQL Server Management Studio。
 
4.Windows 云服务器上启动 SQL Server Management Studio。在【Connect to server】页面，填写相关信息连接云数据库。单击【Connect】，稍等几分钟后，SQL Server Management Studio 将连接到您的数据库实例。
<ul>
    <li>Server type：选择 Database Engine。</li>
    <li>Server name：数据库实例的内网 IP 和端口号，需用英文逗号隔开。例如，内网 IP 和端口号为10.10.10.10:1433，则在此填入10.10.10.10,1433。注意使用英文标点符号。</li>
    <li>Authentication：选择 SQL Server Authentication。</li>
    <li>Login 和 Password：在实例【帐号管理】页创建帐号时，填写的帐号名和密码。</li>
</ul>

5.连接到数据库后，可以查看到 SQL Server 的标准内置系统数据库（master、model、msdb 和 tempdb）。
 
6.您可以开始创建自己的数据库并对数据库运行查询。选择【File】>【New】>【Query with Current Connection】，键入以下 SQL 查询：
```angular2html
select @@VERSION
```
7.运行查询，SQL Server Management Studio 会返回 SQL Server 版的腾讯云云数据库实例。

## 4、从本地连接 SQL Server 实例

考虑到数据的安全，云数据库 SQL Server 尚未开放实例外网 IP，如有需求可以利用 SSH2 的端口映射在外网连接实例，并对其进行配置和管理。

1.登录云数据库 SQL Server实例详情页，查看实例内网 IP 及端口号。该内网 IP 及端口号会在配置端口映射时使用。

2.准备一台具有外网 IP的 Linux 云服务器。

3.在本地使用 SSH 工具（如 SecureCRT 或 PuTTY 等，本文以 SecureCRT 为例）登录 Linux 云服务器。

4.在 SecureCRT 菜单栏选择【Options】>【Session Options】，进入会话属性设置。
 
5.在会话属性设置页，选择【Connection】>【Port Forwarding】>【Add】，进入配置端口映射页。
 
6.在配置端口映射页，配置相应参数。
 
7.在本地下载并安装SQL Server Management Studio。

8.本地启动 SQL Server Management Studio。在【Connect to server】 页面，填写相关信息连接云数据库。单击【Connect】，稍等几分钟后，SQL Server Management Studio 将连接到您的数据库实例。
<ul>
    <li>Server type：选择 Database Engine。</li>
    <li>Server name：本机 IP 地址和端口号，需用英文逗号隔开，例如10.0.0.1,4000。端口号需与第6步中配置的端口保持一致。</li>
    <li>Authentication：选择 SQL Server Authentication。</li>
    <li>Login 和 Password：在实例【帐号管理】页创建帐号时，填写的帐号名和密码。</li>
</ul>
 
9.连接到数据库后，可以查看到 SQL Server 的标准内置系统数据库（master、model、msdb 和 tempdb）。
 
10.现在您可以开始创建自己的数据库并对数据库运行查询。选择【File】>【New】>【Query with Current Connection】，键入以下 SQL 查询：
```angular2html
select @@VERSION
```
11.运行查询，SQL Server Management Studio 会返回 SQL Server 版的腾讯云云数据库实例。














