# SQL Server数据迁移

云数据库 SQL Server 支持数据源是云服务器 CVM 自建的 SQL Server 数据库进行数据迁移，本节将指导您如何配置迁移任务，帮助您完成将 CVM 上自建的 SQL Server 数据库迁移至云数据库 SQL Server：

1.创建迁移任务

2.登录 SQL Server 控制台，在左侧栏选择【数据传输】页。

3.单击【创建任务】，填写任务名称、源库信息、目标库信息，源实例类型选择【云服务器自建SQL Server数据库】。

![image](../../../Gallerys/tencentd6-52.jpg)

4.单击【下一步】后，需先对 源 SQL Server 实例进行相关配置 再继续 配置迁移任务。

**说明**：若出现报错信息为 “源实例信息校验不通过！” ，请检查以下几项进行排查：
* 源 SQL Server 实例 sa 账号是否存在。
* 源 SQL Server 实例 sa 账号密码是否正确。
* 源 SQL Server 实例 IP 和 PORT 连通性是否正常。

## 1、配置远SQL Server 实例

1.源 SQL Server 实例开启 sa 账户。

2.在【连接】里选择【允许远程连接到此服务器】，并设置合理的远程查询超时值。

![image](../../../Gallerys/tencentd6-53.jpg)

3.在【安全性】里选择【SQL Server 和 Windows 身份验证模式】。

![image](../../../Gallerys/tencentd6-54.jpg)

4.开启网络协议 TCP/IP。

![image](../../../Gallerys/tencentd6-55.jpg)

5.启动内置帐户选择【localsystem】。

![image](../../../Gallerys/tencentd6-56.jpg)

6.Windows 防火墙允许 SQL Server 端口通信，以及445端口通信（基础网络）或49001端口通信（私有网络）。

7.（可选）【CVM 网络】选择【私有网络】时，需配置 freeSSHd 工具。

(1)	下载安装 freeSSHd，默认安装即可，同意启动 freeSSHd 服务。

(2)	双击 freeSSHd 桌面图标，右键任务栏 freeSSHd 图标打开 setting 设置页面，进行配置。

(3)	选择【SSH】选项，配置端口为49001（此处端口默认为22，需要改为49001）。

![image](../../../Gallerys/tencentd6-57.jpg)

(4)	选择【Server status】选项，启动 ssh server。

(5)	选择【Authentication】选项，选择【Allowed】允许密码授权。

(6)	选择【Users】选项，添加用户 tencent_vpc_migrate（该用户名不可更改），密码tencent_vpc_migrate（该密码不可更改），配置如下图所示：

![image](../../../Gallerys/tencentd6-58.jpg)

(7)	使用 D:\dbbackup\（此路径不可改变）为 SQL Server 迁移中使用的备份文件夹，选择【SFTP】选项，并将此路径配置到“SFTP home path”中。

## 2、配置迁移任务

选择迁移类型、设置数据库（选择需要迁移的库表），单击【保存并校验】，如校验不通过可按错误提示完成修复。

![image](../../../Gallerys/tencentd6-59.jpg)

任务创建完后，返回任务列表，此时任务状态为【初始化】，选择并单击【启动】同步任务。

数据同步完成（即进度条为100%）后，需要手动单击【完成】，同步进程才会结束；如果在配置迁移任务时勾选了【增量同步】，需在进度条在99%时手动单击【完成】；根据【状态】查看迁移是否成功。

1. 任务状态变为【任务成功】时，表示数据迁移成功。

2. 任务状态变为【任务失败】时，表示数据迁移失败，请查看失败信息，并根据失败信息修正后重新迁移。





