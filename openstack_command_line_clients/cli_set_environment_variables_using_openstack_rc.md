### 用OpenStack RC文件设置环境变量

为了给OpenStack命令行客户端提供所需的环境变量，您必须创建OpenStack RC文件，或者说叫```openrc.sh```文件。如果您安装的时候有这个文件，您可以在OpenStack控制台用任意一个用户下载到这个文件。这个针对某个project有效地文件包含了所有OpenStack服务所需的关键信息。

当您手动载入这个文件时，里面的环境变量配置便会加载进当前Shell中。这些环境变量连接了本地的OpenStack客户端和在云上的OpenStack服务。

>注意：
>在Microsoft Windows中，我们并不会用环境变量文件导入环境变量。环境变量一般都是在*System Properties*对话框中的*Advaned*选项卡中设置的。

#### 下载和载入OpenStack RC文件

登录OpenStack控制台，选择对应的Project，然后依次点击“Compute”和“Access&Security”。

在API Access选项卡中，点击Download OpenStack RC文件，把文件下载下来。这个文件名的格式是“```PROJECT-openrc.sh```”，此处的PROJECT便是你下载时所在的那个Project。

将这份PROJECT-openrc.sh文件复制到您想运行OpenStack命令的机器上。（比如您想用glance工具从某机器上上传一个图片到云，那就把这个文件复制到这机器上。）

然后载入这个文件，请看下面这个例子，假定我们要设置demo这个Project。

```
$ source demo-openrc.sh
```

当系统要求您输入密码时，您要输入刚刚下载这个文件时登录进OpenStack那个用户的密码。

#### 手动创建和载入OpenStack RC文件

此外，如果您无法在OpenStack控制台处下载到PROJECT-openrc.sh，您可以按照下面的教程手动创建一个。

在文本编辑器中，创建一个叫PROJECT-openrc.sh的文件，添加如下认证信息。

```
export OS_USERNAME=username
export OS_PASSWORD=password
export OS_TENANT_NAME=projectName
export OS_AUTH_URL=https://identityHost:portNumber/v2.0
# 以下内容也可以省略
export OS_TENANT_ID=tenantIDString
export OS_REGION_NAME=regionName
export OS_CACERT=/path/to/cacertFile
```

在你想运行OpenStack命令的shell中加载这个文件。本例中，我们为admin这个project加载了admin-openrc.sh。

```
$ source admin-openrc.sh
```

>注意：	
>如果您操作的时候使用了本例中的RC文件，在运行OpenStack命令的时候系统就不会再要求您输入密码了，因为密码已经用明文存储在这个RC文件中了。请给此文件设置一个合适的权限来避免安全问题。您也可以在删除该RC文件中有关密码的条目，在运行OpenStack时使用“```--password```”参数，这样系统就会在执行命令时向您询问密码了。

>注意：	
>如果在```OS_AUTH_URL```中设置了使用HTTPS协议的话，您必须设置OS_CACERT这个环境变量，因为TLS（HTTPS）服务器认证过程中需要用到这个环境变量。该环境变量所指向的证书在我们认证服务器证书时会用到。

#### 让环境变量值失效

如果您使用OpenStack命令，您可以用命令中的参数覆盖掉某些环境变量值，这些可选参数可以用各个客户端的```help```命令查询到。例如，您可以在使用```openstack```命令时，用添加参数的方法让环境变量```PROJECT-openrc.sh```中的```OS_PASSWORD```失效：

```
$ openstack --os-password PASSWORD service list
```

本例中```PASSWORD```是您的密码。

您在用任何命令行工具与OpenStack交互时都需要提供用户名和密码。这些用户名和密码信息可以通过不同方法来指定，比如，用环境变量文件或者是命令参数。不过这两种方法都不安全。

举例：如果您在用命令行客户端时用```--os-password```写了密码。那任何有权限登录您电脑的人都可以用```ps```命令看见密码。

为了避免密码被别人看见，请考虑使用交互式的方式提供密码。