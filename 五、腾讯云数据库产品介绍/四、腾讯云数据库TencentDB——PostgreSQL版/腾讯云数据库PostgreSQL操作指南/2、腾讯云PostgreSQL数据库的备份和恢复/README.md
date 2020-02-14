# 腾讯云PostgreSQL数据库的备份和恢复
云数据库 PostgreSQL 高可用版目前仅支持物理备份的方式：
* 全量备份：每天备份一次，备份时间为01:00。
* 增量备份：当产生了 xlog，15分钟会备份一次；或者当产生 xlog，并且已经积压到60个 xlog 时备份一次。
* 数据文件保留时间：保存7天以内的全量备份和增量备份。

## 1、下载备份
下面为您介绍通过 PostgreSQL 控制台下载备份文件的操作：

1.登录 PostgreSQL 控制台，单击实例名称进入详情页。

2.在【备份管理】页，选择【备份列表】或【xlog 列表】， 选择需要下载的备份，在“操作”列单击【下载】。

说明：【备份列表】的备份数据即全量备份；【xlog 列表】的备份数据即增量备份。

3.在弹出的下载页中，会提供 VPC 网络地址和外网下载两种下载方式。

说明：为保证数据安全，地址有效期为15分钟，过期后请刷新页面重新获取，VPC 网络地址请在 VPC 网络进行访问。

## 2、数据恢复
备份好了数据，下面我们来一起看一下如何在云服务器上对数据进行恢复。

通过控制台下载备份进行恢复。

### 2.1、创建恢复目录
```apacheconfig
mkdir recovery
说明：recovery 为示例目录，用户可自行修改恢复目录。
```
### 2.2、下载全量备份文件
登录 PostgreSQL 控制台，在实例列表单击操作列的【管理】进入管理页面，选择【备份管理】页，获取您所需的备份文件下载地址。（服务器需与数据库处于同一网络中）
 
### 2.3、解压全量备份文件
执行如下命令解压全量备份文件：
```apacheconfig
tar zxf 20170905010143.tar.gz –C recovery
```
 
### 2.4（可选）安装相同版本的 PostgreSQL
如已安装可跳过此步骤，本示例采用 yum 源的安装方式，yum 源可至该地址查找您所需要的版本。

```apacheconfig
1. yum install http://yum.postgresql.org/9.3/RedHat/rhel-6-x86_64/pgdg-redhat93-9.3-1.noarch.rpm
2. yum install postgresql93-server postgresql93-contrib 
3. //初始化
4. service postgresql-9.3 initdb
5. 开启
6. service postgresql-9.3 start或者
7. /etc/init.d/postgresql-9.3 start
8. 查看安装结果 
9. rpm -aq| grep postgres 
10.可以看到 
11. [root@i-87-575-VM vmuser]# rpm -aq| grep postgres
12. postgresql93-libs-9.3.4-1PGDG.rhel6.x86_64
13. postgresql93-contrib-9.3.4-1PGDG.rhel6.x86_64
14. postgresql93-9.3.4-1PGDG.rhel6.x86_64
15. postgresql93-server-9.3.4-1PGDG.rhel6.x86_64
```
### 2.5修改配置文件

将配置文件postgresql.conf中的以下选项注释掉，注释方法：在行首使用#。

如有多个该选项，则全部注释掉。
```apacheconfig
1. shared_preload_libraries
2. local_preload_libraries
3. rds_extension_list.names
4. pg_stat_statements.max
5. pg_stat_statements.track
6. archive_mode
7. archive_command
8. 在postgresql.conf文件末尾追加配置 （表示不再使用强同步模式）
9. synchronous_commit = local
10. synchronous_standby_names = ''
```
### 2.6更改文件夹权限
```apacheconfig
chmod 0700 recovery
```
 
### 2.7（可选）应用增量备份文件
如果跳过该步骤，则数据库的内容为开始做全量备份时数据库的内容。

说明：将 xlog 文件放入 pg_xlog 文件夹下，pg 会自动重放 xlog 日志。例如12:00时做的全量备份，如果在该全量备份的基础上，在 pg_xlog 文件夹下放置12:00至13:00的所有 xlog，则数据库能恢复到13:00时的数据内容。

下载增量备份文件（xlog）：
```apacheconfig
tar zxf 20170904010214_20170905010205.tar.gz
```
### 2.8启动数据库
```apacheconfig
pg_ctl start -D ~/recovery
```
### 2.9登录验证
登录数据库。

通过手动导出数据进行恢复，您也可以手动导出备份数据，然后在腾讯云云服务器上进行恢复操作，该方案在 Windows 和 Linux 下同样适用，与物理文件所在的文件系统无关。

1.在云服务器下 dump 出数据，多个库时使用 pg_dumpall，示例如下：

(1)	pg_dump -h 10.20.3.7 -p 5432 -Utera   -f tera.sql -c -C postgres

(2)	不指定文件格式时，默认导出的文件为文本文件，文件形式如下：
```apacheconfig
-- PostgreSQL database dump---- Dumped from database version 9.5.4-- Dumped by pg_dump version 9.5.19SET statement_timeout = 0;SET lock_timeout = 0;SET client_encoding = 'UTF8';SET standard_conforming_strings = on;SELECT pg_catalog.set_config('search_path', '', false);SET check_function_bodies = false;SET xmloption = content;SET client_min_messages = warning;SET row_security = off;
```
(3)	若数据较大，可通过 -Fc 指定为二进制文件。

2.在云服务器上恢复数据。
(1)	文本文件，可直接通过执行 sql 语句恢复，示例如下：
```apacheconfig
psql -U postgres <./tera.sql
```
说明：因为有 pg_stat_error 等插件，可能会导致报错，但不影响数据导入。
二进制文件，需要用 pg_restore 还原。





