### 登录控制台

控制台运行在具有```nova-dashboard```这一服务器角色的节点上。

1. 向这个云服务的管理员咨询能登录控制台的主机名，IP地址，账号和密码。
2. 打开一个启用了JavaScript和Cookies的浏览器。
	>注意：
	>如果要使用VNC客户端，你的浏览器需要支持HTML5 Canvas和HTML5 WebSockets。VNC浏览器基于noVNC。详情请查阅[noVNC: HTML5 VNC Client](https://github.com/kanaka/noVNC/blob/master/README.md)。想查阅支持的浏览器列表，参阅[支持浏览器](https://github.com/kanaka/noVNC/wiki/Browser-support)。
3. 在地址栏里，输入控制台的主机名或者IP地址。例如：	
	```
	https://ipaddressorhostname/
	```
4. 在登录界面上，输入账号和密码，然后单击```Sign In```。	
	窗口的最上边便会显示你的用户名。你还可以在这里找到设置和退出按钮。		
	登录之后显示哪些选项卡取决于访问权限，用户角色，还取决于你用什么用户登录。
	- 如果你是以最终用户身份登录的，*Project*选项卡和*Identity*选项卡可用。
	- 如果你是以管理员身份登录的，*Project*选项卡，*Admin*选项卡和*Identity*选项卡可用。

#### OpenStack控制台 - *Project*选项卡

**Project**是云服务中的组织单位，也被成为**tenant**或者**account**。每一个用户都是一个或多个Project中的成员。用户创建和管理服务器实例，都是在Project里完成的。

在Project选项卡中，用户可以查看和管理某个project的资源情况，包括实例和镜像。在**CURRENT PROJECT**可以选择想看的project。

在*Project*选项卡下，可以查看如下选项卡。

##### *Compute*选项卡

- *Overview*：查看Project的概览。
- *Instances*：对服务器实的各种操作，包括查看，启动，关闭，暂停，重启和创建快照等，也可以在此处通过VNC连接服务器。
- *Volumes*：包含如下两个选项卡：
	- Volumes: 查看，创建，编辑和删除Volume。
	- Volume Snapshots: 查看，创建，编辑和删除Volume快照。
- *Images*: 查看Project用户创建的镜像和实例快照，以及所有公开可用的镜像。创建，编辑，删除镜像，以及通过镜像或快照启动实例。
- *Access & Security*: 包含如下四个选项卡：
	- Security Groups: 查看，创建，编辑和删除安全组和安全组规则。
	- Key Pairs: View, create, edit, import, and delete key pairs. 查看，创建，编辑，导入以及删除公私钥对。
	- Floating IPs: Allocate an IP address to or release it from a project. 向project分配IP；从project处回收IP。
	- API Access: 查看API端点。

##### *Network*选项卡

- *Network Topology*：查看网络拓扑结构。
- *Networks*：创建和管理公司网络。
- *Routers*：查看和管理路由。

##### *Object Store*选项卡
- *Containers*：创建和管理容器和对象。

##### *Orchestration*选项卡
- *Stacks*: 使用REST API来编排管理多个复合云应用。
- *Resource Types*: 列出所有HOT模板支持的资源种类。

#### OpenStack控制台 - *Admin*选项卡

管理员用户可以通过*Admin*选项卡来查看使用量，管理实例，卷，flavors，镜像，服务和配额等。

在*Admin*选项卡下，可以查看如下选项卡。

##### *System*选项卡

- Overview：查看概况
- Resource Usage：查看如下两个选项卡
	- Usage Report：查看用量报告。
	- Stats：查看所有资源的统计信息。
- Hypervisors：查看虚拟机管理器的概况。
- Host Aggregates：查看，创建和编辑主机聚合（？）。查看可用的域。
- Instance：查看，暂停，回复，停用，迁移，软/硬重启，删除其他某些project下的用户的实例。在这个选项卡中还可以查看日志，以及通过VNC连接服务器实例。
- Volumes：使用如下两个选项卡。
	- Volumes：查看，创建，管理和删除卷。
	- Volume Types：查看，创建，管理和删除卷类别。
	- Volume Snapshots：查看，管理和删除卷快照。
- Flavors：查看，创建，编辑，删除预先编排的云主机类型，或者查看额外的介绍。不同的云主机类型有不同的实例大小。
- Images：对定制的镜像做如下操作：查看，创建，删除，编辑属性。
- Networks：对网络做如下操作：查看，创建，删除，编辑属性。
- Routers：对路由做如下操作：查看，创建，删除，编辑属性。
- Defaults：查看默认的配额值。配额是在OpenStack Compute中硬性安排的，定义了最大的可用实例，以及资源的数量。
- Metadata Definitions：导入命名空间，查看元数据信息。
- System Information：该选项卡有如下子选项卡。
	- Services：查看服务列表。
	- Compute Services：查看计算服务列表。
	- Block Storage Services：查看块存储服务列表。
	- Network Agents：: 查看网络？View the network agents.
	- Orchestration Services：查看编排服务列表。

#### OpenStack控制台 - *Identity*选项卡

- Projects: 对Project做如下操作：查看，创建，分配用户，移除用户，删除。
- Users: 查看，创建，启用，停用，删除用户。

#### OpenStack控制台 - *Setting*选项卡


登录进任意账户，在左边便可以看见*Setting*选项卡。
- User Settings: 查看和管理控制台选项。
- Change Password: 修改用户密码。