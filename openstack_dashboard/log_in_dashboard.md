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
	- 如果你是以最终用户身份登录的，*Project*选项卡和*Identity*选项卡便会显示出来。
	- 如果你是以管理员身份登录的，显示的选项卡会有三个：*Project*选项卡，*Admin*选项卡和*Identity*选项卡。

#### OpenStack控制台 - *Project*选项卡

**Project**是云服务中的组织单位，也被成为**tenant**或者**account**。每一个用户都是一个或多个Project中的成员。用户创建和管理服务器实例，都是在Project里完成的。

在Project选项卡中，用户可以查看和管理某个project的资源情况，包括实例和镜像。在**CURRENT PROJECT**可以选择想看的project。

