### 为实例设置权限和安全选项

当您启动一个虚拟机时，您可以向其中注入一对密钥，这样您就能通过SSH访问实例了。不过，这样做的前提是您的实例中必须安装了`cloud-init`这个包。

您可以为一个实例创建不止一对密钥，您也可以将一对密钥放在同一Project下的多个服务器中。如果您的密钥对是用外部工具创建的，您还可以将其导入进OpenStack。

>注意：
>一对密钥只属于一个用户，并不属于一个组。如果要和多个用户共享一对密钥，每个最终用户都要把相同的密钥导入一次。

如果某个镜像是用固定的root密码，或一个静态的密钥来做认证的，您就不能在启动这个实例时创建密钥对了。

“安全组”是一组网络规则，它能限制对实例的网络访问类型。当您启动实例时，您可以为其设置一个或多个安全组。如果您没有创建安全组，新的实例会被自动分配默认的安全组，除非您另外指定别的组了。

安全组里的安全规则控制着组内实例的网络。任何不符合规则的网络流量都会被拒绝。您可以在网络组中添加或删除规则，还可以修改默认安全组或者其他安全组内的规则。

您可以在安全组中设置允许连接实例的哪些端口（或者是通过哪些协议来连接）。例如，您可以修改规则，允许外部通过SSH来连接服务器，Ping这个服务器，或者允许UDP连接（比如DNS之类的服务）。具体说来，您可以指定规则中的如下参数：
	- 网络流量的来源：可以设置成允许云中的某个IP，其他组的某个IP，或者所有的IP。
	- 协议：如果连接SSH的话就要选TCP。如果要ping的话就选ICMP。有时候还可能要选UDP。
	- 虚拟机上的目标端口：定义一个端口范围。如果只想开放一个端口，则需要把这个端口写两遍。ICMP不支持端口，如果您要开发ICMP流量，您需要输入值来定义开放ICMP中的哪些类别和代号。

如果您修改了规则，这些规则即刻生效。

>注意：
>使用了默认安全组的实例是不能被任何外部IP访问到的。如果您希望用这些外部IP来访问实例，您必须修改默认安全组中的规则。
>您也可以为运行中的实例分配一个浮动IP，这样便能从云的外部访问到了。请参看Manage IP Addresses章节。

#### 添加一对密钥

您可以生成一对密钥，也可以将已经生成的公钥上传进OpenStack

1. 如果想生成一对密钥，请使用如下命令：

```
$ nova keypair-add KEY_NAME > MY_KEY.pem
```

这条命令会生成一对密钥，其中`KEY_NAME`是您指定的这对密钥的名称，私钥会保存在您指定的`.pem`文件中，然后将公钥注册进Nova数据库里。

2. 运行如下命令，保证只有您能读写这个文件的内容：

```
$ chmod 600 MY_KEY.pem
```

#### 导入一对密钥

1. 如果您已经创建了一对密钥了，假如您的公钥存放在`~/.ssh/id_rsa.pub`，您可以运行如下命令来上传公钥。

```
$ nova keypair-add --pub_key ~/.ssh/id_rsa.pub KEY_NAME
```

这条命令会将您的公钥注册在Nova数据库中，保存的名称是您指定的，此处是KEY_NAME。

2. 为了确认您的密钥已经成功导入，用下面的命令列出所有的密钥对：

```
$ nova keypair-list
```

#### 创建和管理安全组

1. 如果要列出当前Project所有的安全组，包括描述，请使用以下命令：

```
$ nova secgroup-list
```

2. 如果要创建一个安全组，请使用如下命令（该命令同时会为安全组命名，还会添加描述）：

```
$ nova secgroup-create SECURITY_GROUP_NAME GROUP_DESCRIPTION
```

3. 如果要删除某个安全组，请使用如下命令：

```
$ nova secgroup-delete SECURITY_GROUP_NAME
```

>注意：		
>您不能删除某个Project下的默认安全组，也不能删除已经分配给一个运行中的实例的安全组。

#### 创建和管理安全组规则

`nova secgroup-*-rule`命令专门用来修改用户组规则。在您开始修改这些规则前，请先载入OpenStack RC 文件。详情请参阅Set environment variables using the OpenStack RC file。

	1. 如果您要列出安全组下地所有规则，请执行以下命令：
	```
	$ nova secgroup-list-rules SECURITY_GROUP_NAME
	```

	2. 如果要允许本机的SSH，从以下两个命令中选择一个：
	
		- 允许所有的网络连接，用CIDR格式写明0.0.0.0/0IP和子网。
		```
		$ nova secgroup-add-rule SECURITY_GROUP_NAME tcp 22 22 0.0.0.0/0
		```
		- 只允许别的安全组内的IP来访问特定端口：
		```
		$ nova secgroup-add-group-rule --ip_proto tcp --from_port 22 \
      		--to_port 22 SECURITY_GROUP_NAME SOURCE_GROUP_NAME
		```
	3. 如果要允许实例接受ping，从以下两个命令中任选一个：

		- 允许从任意地址发来的ping，用CIDR格式写明0.0.0.0/0IP和子网。
		```
		$ nova secgroup-add-rule SECURITY_GROUP_NAME icmp -1 -1 0.0.0.0/0
		```
		这条命令将打开所有ICMP流量的所有编码和种类。

		- 只允许别的安全组内的IP来ping：
		```
		$ nova secgroup-add-group-rule --ip_proto icmp --from_port -1 \
     		--to_port -1 SECURITY_GROUP_NAME SOURCE_GROUP_NAME
		```

	4. 如果要允许UDP通信，比如需要在虚拟机上搭建DNS的时候，请从下面两个命令中任选一个：

		- 允许从任意地址发来的UDP通信，用CIDR格式写明0.0.0.0/0IP和子网。
		```
		$ nova secgroup-add-rule SECURITY_GROUP_NAME udp 53 53 0.0.0.0/0
		```
		- 只允许别的安全组内的IP来访问特定端口：
		```
		$ nova secgroup-add-group-rule --ip_proto udp --from_port 53 \
      		--to_port 53 SECURITY_GROUP_NAME SOURCE_GROUP_NAME
		```

#### 删除用户组规则

如果要删除安全组规则，您要在删除指令后面写明您创建这个规则时添加的参数。

比如，如果您要删除允许所有IP连接SSH的规则，请使用如下指令：

```
$ nova secgroup-delete-rule SECURITY_GROUP_NAME tcp 22 22 0.0.0.0/0
```