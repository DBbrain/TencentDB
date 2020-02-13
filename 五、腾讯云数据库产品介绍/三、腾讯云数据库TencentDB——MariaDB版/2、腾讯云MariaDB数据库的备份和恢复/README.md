# 腾讯云MariaDB数据库的备份和恢复
云数据库 MariaDB 支持全量备份和增量备份。备份采用 LZ4 方式压缩。
全量备份可设置备份程序启动时刻和备份保存时长，默认启动时刻为凌晨00：00 - 05:00性能较低时刻，备份保存时长默认为7天（金融云默认为30天）。
增量备份以 binlog 方式提供，binlog 实时生成（将占用一定数据盘空间），并定期上传至云数据库备份系统。

腾讯云数据库会执行用户设定的备份策略，自动备份数据库。

1.登录云数据库 MariaDB 控制台，单击实例名或操作列的【管理】，进入实例管理页面。

2.选择【备份与恢复】>【备份和日志设置】页，单击以下图标可设置存储时间。

(1)	备份周期：目前默认每天执行备份任务。

(2)	存储时间：数据和日志备份文件保留的天数，默认为30天，可设置1天 - 30天，超过30天需 提交工单申请，最大可配置3650天。

(3)	备份执行时间：可以设置为任意时段，以小时为单位。

(4)	日志备份：默认开启，不可关闭。日志包括错误日志，慢日志，事务日志（binlog）等。
 
下面我们来详细介绍一下MariaDB的数据库备份和恢复操作路径。

## 1、下载备份文件
1.下载备份文件和Binlog：

(1)	登录MariaDB 控制台，单击实例名进入实例管理页，选择【备份与恢复】页，选择冷备列表或 Binlog列表。

(2)	选择需要下载的备份，在操作列单击【下载】。

(3)	在弹出的下载对话框，提供 VPC 网络地址，单击【复制】获取地址。说明：地址有效期为15分钟，过期后请刷新页面重新获取，VPC 网络地址请在 VPC 网络进行访问。
 
2.下载慢查询日志：

(1)	单击实例名进入实例管理页，选择【性能优化】>【慢查询日志】页。

(2)	选择需要下载的备份，在操作列单击【下载】。说明：若备份大小为0KB，无慢查询记录，则无法下载。

(3)	在弹出的下载对话框，提供 VPC 网络地址，单击【复制】获取地址。说明：地址有效期为15分钟，过期后请刷新页面重新获取，VPC 网络地址请在 VPC 网络进行访问。

## 2、解压备份和日志文件
出于压缩性能和压缩比的综合考虑，MariaDB 的备份文件和日志文件（binlog 文件）采用 LZ4（Extremely Fast Compression algorithm）工具进行压缩，您可以选用 LZ4 工具进行解压。由于常见的解压工具不支持该格式，本文特别给出解压工具和操作指引。

### 2.1、Windows
1.下载工具，工具下载地址：https://wiki-jjb-1254408587.cos.ap-chengdu.myqcloud.com/lz4_win64_v1_9_2.zip

2.安装工具，双击 zip 文档，解压后得到 LZ4installv1.4.exe ，双击运行，按指引完成安装。

3.如果只是解压我们的文件，最后一步的复选框可以忽略。

4.解压文件右键单击需要解压的 lz4 文件，选择 Decode with LZ4 项即可完成解压。

### 2.2、Linux

1.安装工具，腾讯云云服务器的 yum 库中有 LZ4 组件，登录云服务器执行如下命令即可安装。
```apacheconfig
yum install lz4
```
2.直接执行 lz4 返回类似如下图所示，表示安装正确。

3.解压文件。执行如下命令即可完成解压。
```apacheconfig
lz4 -d xxx.lz4
```

## 3、通过备份文件恢复实例
MariaDB 可通过回档功能来查看历史数据，如果您需要在本地恢复您的数据库实例，可按照本文的步骤进行操作恢复历史数据。

### 3.1、准备服务器

如您需在本地恢复数据库实例，请确保服务器基本配置如下：
<ul>
    <li>CPU：2核或以上。</li>
    <li>内存：4G或以上。</li>
    <li>磁盘空间：必须超过数据库已用空间并留足系统所需的临时空间。</li>
    <li>操作系统：centos。</li>
</ul>

### 3.2、准备数据库
下文以安装 MariaDB 10.0.10 为例：

1.添加 yum 源。
```apacheconfig
(1) vi /etc/yum.repos.d/mariadb-10.0.10.repo):# MariaDB 10.0 CentOS repository list - created 2016-05-30 02:16 UTC# http://downloads.mariadb.org/mariadb/repositories/
(2) [mariadb]
(3) name = MariaDB# baseurl = http://yum.mariadb.org/10.0/centos7-amd64
(4) baseurl = http://archive.mariadb.org/mariadb-10.0.10/yum/centos6-amd64/
(5) gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
(6) gpgcheck=0
```
2.检查配置 yum 源对应的 MariaDB 版本是否为10.0.10。
```apacheconfig
(1) yum makecache
(2) yum info MariaDB-server
```
3.安装 MariaDB-server。
```apacheconfig
yum install MariaDB-server
```
4.说明：提示与旧版本冲突时，需要先移除之前的安装包，例如yum remove mariadb-libs。

### 3.3、安装辅助工具
1.安装 MariaDB 客户端。
```apacheconfig
yum install MariaDB-client
```
2.安装 LZ4 解压软件，请参见 解压备份文件和日志文件。LZ4 默认安装在mysqlagent/bin目录下，也可以将其放置在/usr/bin目录下，当环境变量引用。
```apacheconfig
(1) yum install -y lz4
(2) percona-xtrabackup
(3) yum install http://www.percona.com/downloads/percona-release/redhat/0.1-3/percona-release-0.1-3.noarch.rpm
(4) yum install percona-xtrabackup
```
### 3.4、下载备份
在 MariaDB 控制台，单击实例名进入实例管理页，在【备份与恢复】页获取备份下载地址。下载命令示例：
```apacheconfig
wget  --content-disposition 'http://169.254.0.27:8083/2/noshard1/set_1464144850_587/1464552298xxxxxxxx'
```
### 3.5、通过备份文件恢复数据库（未加密）
1.进入备份文件下载目录，通过 lz4 解压冷备文件
```apacheconfig
lz4 -d set_1464144850_587.1464552298.xtrabackup.lz4
```

2.使用 xbstream 工具解压到临时目录 xtrabackuptmp
```apacheconfig
(1) mkdir xtrabackuptmp/
(2) mv set_1464144850_587.1464552298.xtrabackup xtrabackuptmp/
(3) xbstream -x < set_1464144850_587.1464552298.xtrabackup
```
 
3.使用 innobackupex 应用日志
```apacheconfig
(1) mkdir /root/dblogs_tmp
(2) innobackupex --apply-log  --use-memory=1G --tmpdir='/root/dblogs_tmp/' /root/xtrabackuptmp/
```
操作成功后，会显示completed OK!
 
4.停止数据库，清空数据文件
(1)	service mysql stop清空数据文件（数据目录、表空间目录、日志目录）：
```apacheconfig
mkdir /var/lib/mysql-backup
mv /var/lib/mysql/* /var/lib/mysql-backup
```

5.修改数据库参数文件

修改数据库参数文件(/etc/my.cnf.d/server.cnf)，具体参数数值请参考解压文件中backup-my.cnf的参数。不能直接用 backup-my.cnf 替换参数文件。
```apacheconfig
(1) [mysqld]
(2) skip-name-resolve
(3) datadir=/var/lib/mysql
(4) innodb_checksum_algorithm=innodb
(5) innodb_log_checksum_algorithm=innodb
(6) innodb_data_file_path=ibdata1:2G:autoextend
(7) innodb_log_files_in_group=4
(8) innodb_log_file_size=1073741824
(9) innodb_page_size=4096
(10) innodb_log_block_size=512
(11) innodb_undo_tablespaces=0
```
6.使用 innobackupex 加载镜像
```apacheconfig
innobackupex --defaults-file=/etc/my.cnf --move-back /root/xtrabackuptmp/
```
成功后，显示Complete OK!
 
7.启动数据库
```apacheconfig
(1) chmod 777 -R /var/lib/mysql
(2) service start mysql，如果遇到启动失败，则需要检查错误信息，修复后再重新启动。
```
8.连接数据库查看数据

数据库启动后，您可能需要通过原来的账号密码来连接数据库查看数据。

### 3.6、通过备份文件恢复数据库（已加密）
数据透明加密（TDE）当前仅支持 Percona 5.7 版本，您可以通过云数据库 MariaDB 入口访问，请先下载并安装本次恢复需要的关键工具。加密流程如下：

1.将备份文件解压到临时目录

进入备份文件下载目录，通过 lz4 解压冷备文件，使用 xbstream 工具解压到临时目录 xtrabackuptmp 中。

本节示例中，将备份解压到临时目录./backup_dir 中。lz4 默认安装在mysqlagent/bin目录下，也可将其放置在/usr/bin目录下，当环境变量引用。

2.获取数据密钥明文

您可以使用密钥管理 KMS 的 API 接口。
```apacheconfig
innobackupex --apply-log --rebuild-indexes  --use-memory=1G  --tmpdir=/tmp ./backup_dir/
```
3.数据恢复前的准备工作

停止数据库，清空数据文件。

4.获取数据密钥明文

解密数据前，需在 MariaDB 控制台 的实例管理页的【数据安全性】>【数据加密】中查询数据密钥密文，然后，您可以通过如下两种方案（任选其一）解密数据密钥密文，得到数据密钥明文。

(1)	通过密钥管理服务 KMS 的 API，自己实现获取数据密钥明文。

(2)	通过腾讯云提供的 Python 脚本./kms_tool.py，获取数据密钥明文。

<table width="100%" style="table-layout:fixed;">
    <tr>
        <td width="200px">参数解释：</td>
        <td>
            1)	--role：格式固定，填入 kmsTDSQLRole 即可。
            <br/>2)	--secret_id、--secret_key：授权信息，可在【访问管理】中的API密钥管理查询。
            <br/>3)	--region：地域信息，可在 KMS公共参数中查询。
            <br/>4)	--ciphertext：数据密钥密文。
        </td>
    </tr>
    <tr>
        <td>使用 demo 案例：</td>
        <td>
            1)	python ./kms_tool.py --role="qcs::cam::uin/xxxxxxxxx:roleName/kmsTDSQLRole" 
            <br/>2)	--secret_id="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" --secret_key="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
            <br/>3)	--region="ap-hongkong" --ciphertext="CtFlCTPsA0+LilyvZ5V4nsEi6qSIA/KEhVeE8zQFIGfBwzXiMShQZIYYUt9KBWAUsHcfLQ0Z5feADH2D49/nOw==-k-fKVP3WIlGpg8m9LMW4jEkQ==-k-zudP3Tz4jxrvjs7zKkuKU+0V/gVVaNRRIIaRl/+83qCinaBgsLQbU5e1MpW4q/IJKpNXnb9N9/rO5Es03fh7PU9n8Sjex6mnl+YKV1SMQog+RJ1E8bNmwx/22hhHb/1B5LGpwB8tbXKD3gL0tZwSJvV2QxSaUnONh5+6ssb2cQZI8MhcBBhGj9oXtbL6OC74PuDO1D/AsQ6qBLIqC2bTSA68s8Q="
        </td>
    </tr>
</table>

5.重新生成数据密钥文件

获取数据密钥明文后，您可以通过如下两种方案（任选其一）生成数据密钥文件。
<ul>
    <li>通过兼容 Percona 的 TDE 开源工具生成。</li>
    <li>通过腾讯云提供的工具./keyring_tool生成数据密钥文件。./keyring_tool的基本命令格式为 ./keyring_tool "[ciphertext]" [File Path]。</li>
    <li>keyring_tool依赖libboost_program_options.so.1.53.0，若系统没有该 lib 库，需要先执行：export LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH，再运行keyring_tool。</li>
</ul>

6.通过./innobackupex工具对备份文件进行 apply

下图为通过./innobackupex对备份文件进行 apply，直至 apply 正常结束。apply 时需要通过--keyring-file-data=key_file来指定密钥，同时该参数指定的路径须填绝对路径。
此处及后文中用到的 xtrabakcup，为腾讯云专有云数据库 TDSQL 自带的 xtrabakcup，默认放置在专有云安装包目录下的 xtrabackup 目录。
工具使用 demo 如下：
```apacheconfig
./innobackupex --apply-log  --use-memory=1G  --tmpdir=/tmp  --keyring-file-data=/data/home/test/key_file   ./backup/ 
```
 
7.通过./innobackupex工具将备份拷贝到数据目录

下图为通过./innobackupex对备份文件进行移动，建议使用 MySQL 启动用户权限。

工具使用 demo 如下：
```apacheconfig
./innobackupex --defaults-file='/data/home/seven/tdsqlinstall/percona-5.7.17/etc/my_8003.cnf' --move-back ./backup_dir/
```
 
8.通过keyring_file工具将生成的密钥文配置到 MySQL

通过keyring_file将生成的密钥文配置到 MySQL，注意红框中的配置，建议使用 MySQL 启动用户权限运行keyring_file。
 
9.重新启动 mysqld

腾讯云专有云数据库 TDSQL 自带的启动脚本，您也可以使用其他方案启动 mysqld。
 
10.正常访问加密表

成功恢复加密备份后，您可以直接访问已加密表。如果缺少密钥也能恢复，但访问加密表时，会出现错误提示：can't find master key from keying，please check keyring plugin is loaded（开源版 MySQL 或 Percona 会提示 Error 错误）。

## 4、回档数据库
1.回档说明

MariaDB 可以根据备份和日志保持情况，回档到30天内的任意时刻。通过数据库回档能力，可以最大程度地减少系统损失。

MariaDB 回档功能不会影响现网生产实例，可直接回档到腾讯云创建的一个临时实例。临时实例支持读写，可以更灵活的对回档实例进行修改（如清理脏数据），临时实例数据修正完毕后，可以通过“实例切换”将临时实例切换到现网生产实例。

2.限制条件
<ul>
    <li>回档、创建临时实例和实例切换过程中，现网生产实例的部分管理功能将不可用，操作完成即可恢复数据库管理操作。</li>
    <li>回档操作有可能会对二进制日志（binlog）进行强行分片，即未达到100MB也会被备份为一个独立文件。</li>
    <li>回档后的临时实例具备现网生产实例的参数信息（如帐户、数据库参数等），但不能修改。</li>
</ul>

3.回档实例

(1)	登录MariaDB 控制台，单击实例名进入实例详情页，在右上角单击【回档】。

(2)	在弹出的对话框，设置回档时间，单击【确认】。
 
4.临时实例

临时实例是 MariaDB 基于备份生产的一个仅用于临时调整的实例，特性如下：
<ul>
    <li>具备读写特性。</li>
    <li>生命周期仅有48小时，48小时后删除。</li>
    <li>一个实例同时只具备一个临时实例。</li>
    <li>临时实例可以随意删除。</li>
</ul>

操作回档后，可在【备份与恢复】>【临时实例】页查看生成的临时实例。
 
5.切换实例

切换实例指将临时实例切换为现网生产环境。临时实例回档数据确认无误后，可对临时实例进行切换，切换会更换原实例和临时实例的内网链接。
<ul>
    <li>切换过程中，现网生产实例可能闪断1秒。</li>
    <li>切换完成后，临时实例被替换为现网生产实例，而原临时实例也会被删除。</li>
</ul>

可在【备份与恢复】>【临时实例】页，单击【迁移临时实例至现网生产环境】迁移实例。



















