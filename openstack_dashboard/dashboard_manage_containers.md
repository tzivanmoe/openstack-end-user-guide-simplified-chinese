### 创建和管理对象容器

OpenStack对象存储是一个分布式的对象存储平台，支持API调用。该功能可以直接结合进其他应用中，也可以用来存储各种文件，包括虚拟机镜像、备份、归档、甚至是媒体文件。在OpenStack控制台，用户暂时只能管理容器和镜像。

在OpenStack对象存储中，容器是存放对象文件的空间，就像Windows或者Linux的文件夹是存放文件的空间一样。只是容器和容器不能像文件夹一样嵌套。OpenStack的对象包括存储的文件和各种附属的元数据。

#### 创建容器

1. 登录OpenStack控制台。
2. 在Project选项卡中的CURRENT PROJECT中，选择相应的Project。
3. 在Project选项卡中打开Object Store选项卡，然后点击Containers分类。
4. 点击Create Container。
5. 在Create Container对话框，为新的容器起个名字，然后点击Create Container。

至此，您已成功创建了一个新容器。

>注意：
>如果要删除容器，点击More，然后选择Delete Container。


#### 上传对象

1. 登录OpenStack控制台。
2. 在Project选项卡中的CURRENT PROJECT中，选择相应的Project。
3. 在Project选项卡中打开Object Store选项卡，然后点击Containers分类。
4. 选择一个容器来存放对象。
5. 点击Upload Object。
6. 此时会弹出一个对话框，名为“Upload Object To Container: <name>”。其中<name>处是存放本次上传对象的容器名。
7. 输入对象的名称。
8. 找到你想上传的本地文件。
9. 点击Upload Object。

至此，您成功地在容器中上传了一个对象。

#### 管理对象

**编辑对象**

1. 登录OpenStack控制台。
2. 在Project选项卡中的CURRENT PROJECT中，选择相应的Project。
3. 在Project选项卡中打开Object Store选项卡，然后点击Containers分类。
4. 选择一个容器来存放对象。
5. 点击More，在下拉菜单中点击Edit。
6. 此时会弹出一个对话框，名为“Edit Object”。
8. 找到你想上传的本地文件。（译注：上传的新对象会覆盖掉原来的旧对象。）
9. 点击Update Object。

**将某对象在不同容器之间复制**

1. 登录OpenStack控制台。
2. 在Project选项卡中的CURRENT PROJECT中，选择相应的Project。
3. 在Project选项卡中打开Object Store选项卡，然后点击Containers分类。
4. 找到想要复制的文件对象。
5. 点击右边的“More”，然后在下拉菜单中点击“复制”。
6. 在Copy Object对话框，输入如下项：
	- 目标容器：选择复制的目的地。
	- 路径：为复制过去的对象指定在新容器中的路径。
	- 目标对象名：为复制过去的对象设置新的对象名称。
7. 点击Copy Object。

**创建一个只有元数据，但没有文件的对象**

在容器中，可以创建一个只有元数据，但没有文件的对象。待文件可用时再将文件上传至对象即可。这样的临时对象起到了占位的作用，还便于提前将的对象的元数据和URL共享出去。

1. 登录OpenStack控制台。
2. 在Project选项卡中的CURRENT PROJECT中，选择相应的Project。
3. 在Project选项卡中打开Object Store选项卡，然后点击Containers分类。
4. 选择一个容器来存放对象。
5. 点击Upload Object。
6. 此时会弹出一个对话框，名为“Upload Object To Container: <name>”。其中<name>处是存放本次上传对象的容器名。
7. 输入对象的名称。
8. 点击Upload Object。

**创建一个Pseudo-folder**

Pseudo-folders是和正常操作系统类似的文件夹。只是他们是由对象的前缀（或前缀集合）标示的。

1. 登录OpenStack控制台。
2. 在Project选项卡中的CURRENT PROJECT中，选择相应的Project。
3. 在Project选项卡中打开Object Store选项卡，然后点击Containers分类。
4. 选择希望操作的Container。
5. 点击Create 为Pseudo-folder起个名字。
	此时会弹出一个对话框，名为“Create Pseudo-Folder in Container <name>”，其中<name>是你希望操作的容器名称。
6. 为Pseudo-folder起个名字。

在对象名称中，OpenStack会用一个斜线“/”来划分Pseudo-folder和真实的对象名称。

7. 点击Create
