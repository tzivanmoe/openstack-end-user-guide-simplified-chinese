### 创建和管理卷

卷是一种块存储设备。我们把卷挂载在实例实例上，从而达到持久化存储的目的。您可以随时将某个卷安装在一个运行中的实例上，或者从实例上卸载卷，再把它安装再另一个实例上。您还可以给卷拍快照，以及删除卷等等。但只有管理员才能指定卷的类别。

#### 创建卷

1. 登录控制台。
2. 在PROJECT选框中选择希望操作的Project。
3. 在PROJECT选项卡种，打开Compute选项卡，然后点击Volumes子项。
4. 点击Create Volume。在打开的对话框中填入如下信息。
	- Volume Name：为卷指定名称。
	- Description：非必须填写，给该卷添加一段简短的描述。
	- Type：此处请留空。
	- Size(GB)：该卷的大小（以GiB为单位）。
	- Volume Source：在如下选项中选择。
		- No source, empty volume：创建一个空卷，其中不包含文件系统和分区表。
		- Image：如果选择此项，一个叫“Use image as a source”的下拉框便会显示出来，您可以在此选择镜像。
		- Volume：如果选择此项，一个名为“Use volume as a sourc”的下拉框便会显示出来。您可以在此选择卷。快照和卷的选项只会在快照和卷真实存在的情况下才会显示出来。
	- 可用区域：在列表中选择可用区域。默认的情况下，可用区域的设定是由云供应商决定的（例如，```us-west```或者```apac-south```）。有些时候，这个地方也可能是```nova```。

5. 点击Create Volume。

#### 将卷安装在实例上

在你创建了卷之后，此时便可以把卷安装在实例上了。不过，一次只能安装一个卷。

1. 登录控制台。
2. 在PROJECT选框中选择希望操作的Project。
3. 在PROJECT选项卡种，打开Compute选项卡，然后点击Volumes子项。
4. 选择想要安装在实例上的卷，然后点击“Edit Attachments”。
5. 在“Manage Volume Attachments”对话框，选择要安装的实例。
6. 输入一个设备名称，以便实例通过这个名称访问到卷。

>注意：
>有时由于虚拟机设置原因，有些设备的实际名称和卷名称是不一样的。

7. 点击Attach Volume。

	此时控制台会上便会列出实例信息，以及实例连接的卷信息。

现在您便可以在控制台下卷选项卡中上看见卷的状态了，该卷有两种状态：要么是Available，要么是In-Use。

现在您便可以在实例中使用这个磁盘了。挂载，格式化，您随意。

#### Detach a volume from an instance

1. 登录控制台。
2. 在PROJECT选框中选择希望操作的Project。
3. 在PROJECT选项卡种，打开Compute选项卡，然后点击Volumes子项。
4. 选择卷，点击“Edit Attachments”。
5. 点击“Detach Volume”并确定。

此后会有一条消息提示您该变更是否成功完成。

#### 为卷创建快照

1. 登录控制台。
2. 在PROJECT选框中选择希望操作的Project。
3. 在PROJECT选项卡种，打开Compute选项卡，然后点击Volumes子项。
4. 选择想要创建快照的卷。
5. 点击“More”，然后选择“Create Snapshot”。
6. 在弹出的对话框中输入快照名称和简介。
7. 确定变更。

此时，在控制台下的“Volume Snapshot”中，新的卷快照便会显示出来。

#### 编辑卷

1. 登录控制台。
2. 在PROJECT选框中选择希望操作的Project。
3. 在PROJECT选项卡种，打开Compute选项卡，然后点击Volumes子项。
4. 选择想要编辑的卷。
5. 在Action列中，点击“Edit Volume”。
6. 在“Edit Volume”对话框中，编辑该卷的名字和描述。
7. 点击“Edit Volume”。

>注意：
>想要给某个卷扩容，请点击“More”菜单，然后选择“Extend Volume”选项，然后再弹出的框中输入新的卷大小。

#### 删除卷

如果您删除了一个实例，安装在这个实例上的卷中的文件是不会被删除的。

1. 登录控制台。
2. 在PROJECT选框中选择希望操作的Project。
3. 在PROJECT选项卡种，打开Compute选项卡，然后点击Volumes子项。
4. 选择想要删除的卷。此处可以多选。
5. 点击删除卷，然后确认操作。

此后会有一条消息提示您该变更是否完成。
