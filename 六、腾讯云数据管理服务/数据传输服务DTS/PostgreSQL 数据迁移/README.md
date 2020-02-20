# PostgreSQL 数据迁移

本节我们讲一下如何对PostgreSQL进行数据迁移：

1.新建 DTS 数据迁移任务

登录 DTS 控制台，进入数据迁移页，单击【新建任务】。

2.设置源和目标数据库

跳转页面后，填写任务设置、源库设置和目标库设置。

3.任务设置

* 任务名称：为任务指定名称。
* 定时执行：为迁移任务指定开始时间。

4.源库设置和目标库设置

源库类型：目前支持有公网 IP 的 PostgreSQL、云服务器上的自建 PostgreSQL、专线接入腾讯云的 PostgreSQL、VPN 接入的 PostgreSQL、云数据库 PostgreSQL 五种源库类型。

<table>
    <tr>
        <th>源库类型</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>有公网 IP 的 PostgreSQL</td>
        <td>
            能够通过公网 IP 访问的 PostgreSQL 数据库。所需信息：
            <br/>PostgreSQL 主机地址
            <br/>PostgreSQL 端口
            <br/>PostgreSQL 账号
            <br/>PostgreSQL 密码
        </td>
    </tr>
    <tr>
        <td>云服务器上的自建 PostgreSQL</td>
        <td>
            支持基础网络和私有网络两种环境下基于云服务器 CVM 的自建 PostgreSQL 数据库。使用时需要指定云服务器 CVM 的实例 ID 和所处的网络环境。所需信息：所属地域：目前仅支持同地域内的 CVM 自建 PostgreSQL 迁移 TencentDB。若 CVM 与 TencentDB 分处于不同地域，使用 CVM 公网网络，选择【有公网 IP 的 PostgreSQL】项实现迁移CVM 网络：支持基础网络和私有网络私有网络：如选择私有网络，需选择所属的私有网络及子网云服务器实例 IDPostgreSQL 端口PostgreSQL 账号PostgreSQL 密码
        </td>
    </tr>
    <tr>
        <td>专线接入的 PostgreSQL</td>
        <td>
            本地 IDC 自建 PostgreSQL 使用 专线接入DC 服务与腾讯云相连接后，可使用 DTS 数据迁移至腾讯云。所需信息：专线网关：接入腾讯云的数据库服务器所使用的专线网关，了解专线网关私有网络：专线网关所属的私有网络PostgreSQL主机地址：IDC 内的 PostgreSQL 主机地址，DTS 数据迁移将通过专线网关映射 IP 后访问PostgreSQL 端口PostgreSQL 账号PostgreSQL 密码
        </td>
    </tr>
    <tr>
        <td>VPN 接入的 PostgreSQL</td>
        <td>
            本地 IDC 自建 PostgreSQL 通过 VPN 连接服务 或云服务器上自建 VPN 服务接入与腾讯云相连接后，可使用 DTS 数据迁移至腾讯云。所需信息：所属地域：目前仅支持同地域内的 VPN 服务VPN 类型：云 VPN 服务 或云服务器上自建 VPNVPN 网关：仅 云 VPN 服务 需要补充 VPN 网关信息，了解 VPN私有网络：VPN 服务所属的私有网络PostgreSQL 主机地址：IDC 内的 PostgreSQL 主机地址，DTS 数据迁移将通过专线网关映射 IP 后访问PostgreSQL 端口PostgreSQL 账号PostgreSQL 密码
        </td>
    </tr>
    <tr>
        <td>云数据库 PostgreSQL</td>
        <td>
            TencentDB for PostgreSQL 云数据库实例。所需信息：
            <br/>PostgreSQL 实例 ID
            <br/>PostgreSQL 账号
            <br/>PostgreSQL 密码
        </td>
    </tr>
</table>

![image](../../../Gallerys/tencentdb6-40.jpg)













