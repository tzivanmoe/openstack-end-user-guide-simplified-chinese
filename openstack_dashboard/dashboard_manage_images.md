### 上传和管理镜像

虚拟机镜像（本文简称镜像），是一个包含了虚拟磁盘和一个可启动的操作系统的文件。在云中，镜像可以用来创建虚拟机实例。如欲了解如何创建镜像文件，请参阅[*OpenStack Virtual Machine Image Guide*](http://ithasnotbeenaddedyet.com)

如果你权限够大，你可以上传和管理虚拟机镜像。操作人员可能会把上传和管理镜像的权限限制在云管理员和操作人员的角色上。如果你有相应权限，在控制台里，你可以在管理员Project中上传和管理镜像。

>注意：	
>你还可以用```glance```和```nova```这两个命令行工具，或者“Image service and Compute APIs”来管理镜像。

#### 上传镜像

要把镜像上传至某个Project，请参考如下步骤：

- 登录控制台。

- 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。

- 在Project选项卡种，打开Compute选项卡，然后点击Images分类。

- 点击Create Image。  
	此时将会弹出一个“Create An Image”对话框。

- 输入如下值：

|选项|说明|
|:--|:--|
|Name|镜像的名称|
|Description|镜像的概要介绍|
|Image Source|在下拉菜单中选择镜像源。有镜像地址和镜像文件可供选择。|
|Image File or Image Location|根据用户在“Image Source”中的选择，在此处可以填写镜像的URL地址，或者浏览本地文件寻找镜像。|
|Format|选择镜像的格式（如“QCOW2”格式等）|
|Architecture|指定该镜像的架构。例：32位的架构是“i386”，64位的架构是“x86_64”|
|Minimum Disk (GB) and Minimum RAM (MB)|此处请留空|
|Copy Data|勾选此项表示希望将镜像数据复制到镜像服务中|
|Public|勾选此项表示此镜像会对此Project下的所有用户公开|
|Protected|勾选此项表示只有有权限的人才能删除此镜像|
	
-  点击“创建镜像”。	
	该镜像将会进入队列等待上传。从进入队列到启用状态，可能需要一定的时间。
	
#### 更新镜像

要更新一个已有的镜像，请参考如下步骤：

- 登录控制台。
- 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
- 在Project选项卡种，打开Compute选项卡，然后点击Images分类。
- 选择你想修改的镜像。
- 在Action列，点击More，然后在列表中点击Edit Image。
- 在Update Image对话空中，可以进行如下操作：
	- Change the name of the image.重命名镜像。
	- 勾选Public，将该镜像公开。
	- 取消勾选Public，将该镜像转为私有。
- 点击Update Image。

#### 删除镜像

删除镜像将永久删除，不能恢复。只有拥有相应权限的用户才能删除镜像。

- 登录控制台。
- 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
- 在Project选项卡种，打开Compute选项卡，然后点击Images分类。
- 选择想删除的镜像。
- 点击Delete Images。
- 在确认删除的对话框中，点击Delete Images来确认删除。