### 创建和管理网络

OpenStack网络服务是一个在OpenStack云内即可使用的、可扩展的管理网络的工具。这个工具能够帮助用户快速响应网络变更的需求（例如创建和分配新的IP地址等）。

OpenStack中的网络很复杂。本章仅简单介绍了如何创建网络和路由。欲了解详细的OpenStack网络管理内容，请参阅OpenStack Cloud Administrator Guide。

#### 创建网络

1. 登录控制台。
2. 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
3. 在Project选项卡中，打开Network选项卡，点击Network分类。
4. 点击“创建网络”。
5. 在“创建网络”对话框，填入以下内容
	- Network选项卡
		- Network Name：为新建的网络取个名字
		- Admin State：选择要在什么state下启动该网络
	- Subnet选项卡
		- Create Subnet：勾选这个框表示来创建一个子网			
		创建网络的时候不需要必须指定子网，但是如果不指定子网的话，任何与之关联的服务器实例都会收到一个报错信息。
		- Subnet Name：为子网指定一个名称。
		- Network Address：指定子网的IP地址。
		- IP Version：选择IPv4或者IPv6。
		- Gateway IP：指定一个IP为网关地址。该配置可省略。
		- Disable Gateway：禁用网关。
	- Subnet Details选项卡
		- Enable DHCP：勾选此处则启用DHCP。
		- Allocation Pools：指定IP地址池。
		- DNS Name Servers：指定DNS的地址。
		- Host Routes：指定主机路由的IP地址。
6. 点击“创建”	
		此时OpenStack便在Network选项卡上显示网络信息了。

#### 创建路由器

1. 登录控制台。
2. 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
3. 在Project选项卡中，打开Network选项卡，点击Network分类。
4. 点击“Create Router”。
5. 在“Create Router”对话框，给新路由器起一个名字，然后点击“Create Router”。		
	现在这个新的路由器已经在Router选项卡中列出了。
6. 点击新路由的“Set Gateway”选项。
7. 在“External Network”框，指定该路由器要连接到哪个网络上，然后点击“Set Gateway”。
8. 如果要将一个私网连接在刚刚创建的路由器上，请遵循以下步骤：
	1. 在“Router”选项卡上，点击路由器的名字。
	2. 在“Router Details”页面，点击“Interface”选项卡，然后点击“Add Interface”。
	3. 在“Add Interface”对话框，选择一个Subnet。	
	此外，在“Add Interface”对话框，你可以为连接在这个子网的路由器接口设置一个IP Address。		
	如果你执意不设置IP Address，OpenStack Networking会把这个子网中的第一个IP地址分配给这个路由器接口。
	Router Name和Router ID这两栏会自动更新。
9.	点击“Add Interface”。

至此，我们创建了一个新的路由器。在Network Torpology选项卡中可以查看新的网络拓扑图。