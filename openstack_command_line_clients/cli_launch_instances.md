## 创建实例

实例（Instance）是运行在云中的虚拟机。

在创建新实例之前，您要提前知晓以下参数：

- **实例的源**可以是镜像，快照，或者包含镜像或快照的块存储设备。
- 实例的**名字**。
- 您实例的**型号**，这个型号决定了您nova实例的CPU，内存和磁盘空间情况。型号（flavor）是您虚拟机的硬件可用配置。它决定了您能创建的虚拟机的大小。
- 任意的**用户数据**文件。用户数据文件时在元数据服务中的一个特殊的键，它保存了一份能给虚拟机实例中的云服务使用的文件。比如，```cloud-init```程序便使用了用户数据文件，这个程序是一个源自Ubuntu的开源包，能用在多个Linux发行版上，能够接管云实例的初始化过程。
- 访问权限和安全认证信息，其中包括以下认证信息：
	- **密钥对**。密钥对能在镜像启动的时候注入进镜像中，不过前提时该镜像必须包含cloud-init包。您要为每个Project建立至少一个密钥对。如果您已经通过外部工具创建了密钥对，您可以将其导入OpenStack。在一个Project内的多个实例可以共用一个密钥对。
	- **安全组**。用来决定哪些网络流量能流至实例。安全组中包含了一组防火墙策略，被成为安全组规则。
- 如果您需要，您可以为运行中的实例分配一个**浮动IP（公网地址）**。
- 您还可以为实例安排一个块存储设备，或者说**卷**，来做持久化存储。

>注意：		
>使用默认安全组的实例，默认情况下是不能为任何外部IP访问到的。如果您想要让外部IP地址访问到某个实例，您必须修改默认安全组的规则。
>不过您还可以通过分配浮动IP的方式让外部IP得以访问我们的实例。请参见Manage IP Addresses。

在您已经知晓了上述参数之后，您便可以通过镜像或卷来启动实例了。您可以从OpenStack的镜像直接启动实例，也可以从持久化卷上的镜像启动实例。OpenStack镜像服务提供了一个镜像池，能将其中的镜像提供给不同Project中的用户。

### 收集启动实例时需要用到的参数

在您开始之前，请先执行OpenStack RC文件。

1. 列出可用的实例型号：

```
$nova flavor-list
```

记下您想使用的服务器型号。

```
+-----+-----------+-----------+------+-----------+------+-------+-------------+-----------+
| ID  | Name      | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public |
+-----+-----------+-----------+------+-----------+------+-------+-------------+-----------+
| 1   | m1.tiny   | 512       | 1    | 0         |      | 1     | 1.0         | True      |
| 2   | m1.small  | 2048      | 20   | 0         |      | 1     | 1.0         | True      |
| 3   | m1.medium | 4096      | 40   | 0         |      | 2     | 1.0         | True      |
| 4   | m1.large  | 8192      | 80   | 0         |      | 4     | 1.0         | True      |
| 5   | m1.xlarge | 16384     | 160  | 0         |      | 8     | 1.0         | True      |
+-----+-----------+-----------+------+-----------+------+-------+-------------+-----------+
```

2. 列出可用镜像：

```
$ nova image-list
```

记下您想使用哪个镜像来启动您的实例。

```
+--------------------------------------+---------------------------------+--------+--------+
| ID                                   | Name                            | Status | Server |
+--------------------------------------+---------------------------------+--------+--------+
| 397e713c-b95b-4186-ad46-6126863ea0a9 | cirros-0.3.2-x86_64-uec         | ACTIVE |        |
| df430cc2-3406-4061-b635-a51c16e488ac | cirros-0.3.2-x86_64-uec-kernel  | ACTIVE |        |
| 3cf852bd-2332-48f4-9ae4-7d926d50945e | cirros-0.3.2-x86_64-uec-ramdisk | ACTIVE |        |
+--------------------------------------+---------------------------------+--------+--------+
```

您也可以用`grep`来过滤结果，像下面这样：

```
$ nova image-list | grep 'kernel'

| df430cc2-3406-4061-b635-a51c16e488ac | cirros-0.3.2-x86_64-uec-kernel  | ACTIVE |        |
```

3. 列出可用的安全组：

```
$ nova secgroup-list --all-tenants
```

>注意：		
>如果您是管理员，您可以指定`--all-tenants`参数来列出所有tenant的安全组。

记下您想套用在您新实例上的安全组。

```
+----+---------+-------------+----------------------------------+
| Id | Name    | Description | Tenant_ID                        |
+----+---------+-------------+----------------------------------+
| 2  | default | default     | 66265572db174a7aa66eba661f58eb9e |
| 1  | default | default     | b70d90d65e464582b6b2161cf3603ced |
+----+---------+-------------+----------------------------------+
```

如果您没有创建任何安全组，您可以将实例设置为只使用默认安全组。

您还可以用下面的命令来查看某一个安全组内的详细规则：

```
$ nova secgroup-list-rules default
```

4. 列出可用的密钥对，记下您想用在新实例SSH上的那对密钥。

```
$ nova keypair-list
```

### 通过镜像创建实例

1. 在您已经掌握了所需的参数，运行如下命令来启动实例。要指定服务器名，型号ID和镜像ID：

```
$ nova boot --flavor FLAVOR_ID --image IMAGE_ID --key-name kEY_NAME \ 
-- user-data USER_DATA_FILE --security-groups SEC_GROUP_NAME --meta KEY=VALUE \
INSTANCE_NAME
```

此外，您可以在上面的命令中传入一个`--key-name`参数以指定您要用哪一个密钥对，以及用`--security-groups`参数来指定您要用哪一个安全组。您还可以在参数中添加元数据键值对，比如，您如果要为您的服务器添加一段简短的描述，用`--meta description="My Server"`参数即可。

您还可以用`--user-data USER-DATA-FILE`将用户数据文件传入新建的实例。

>重要：
>如果您创建实例时，`INSTANCE_NAME`这一项的长度超过了63个字符，Compute会将其截断，保证dnsmasq能正常工作。这一动作的日志同时会保存在`nova-network.log`中。

请参看下面的命令。该命令创建了一个叫myCirrosServer的实例，型号时`m1.small`（ID是1），镜像是cirros-0.3.2-x86_64-uec（ID是397e713c-b95b-4186-ad46-6126863ea0a9)，默认用户组，密钥对是`KeyPair01`，用户数据文件是`cloudinit.file`。

```
$ nova boot --flavor 1 --image 397e713c-b95b-4186-ad46-6126863ea0a9 \
--security-groups default --key-name KeyPair01 --user-data cloudinit.file \
myCirrosServer
```

根据您提供的参数，上面的命令会返回如下服务器属性：

```
+-------------------------------------+-------------------------------------+
| Property                            | Value                               |
+-------------------------------------+-------------------------------------+
| OS-EXT-STS:task_state               | scheduling                          |
| image                               | cirros-0.3.2-x86_64-uec             |
| OS-EXT-STS:vm_state                 | building                            |
| OS-EXT-SRV-ATTR:instance_name       | instance-00000002                   |
| flavor                              | m1.small                            |
| id                                  | b3cdc6c0-85a7-4904-ae85-71918f734048|
| security_groups                     | [{u'name': u'default'}]             |
| user_id                             | 376744b5910b4b4da7d8e6cb483b06a8    |
| OS-DCF:diskConfig                   | MANUAL                              |
| accessIPv4                          |                                     |
| accessIPv6                          |                                     |
| progress                            | 0                                   |
| OS-EXT-STS:power_state              | 0                                   |
| OS-EXT-AZ:availability_zone         | nova                                |
| config_drive                        |                                     |
| status                              | BUILD                               |
| updated                             | 2013-07-16T16:25:34Z                |
| hostId                              |                                     |
| OS-EXT-SRV-ATTR:host                | None                                |
| key_name                            | KeyPair01                           |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                |
| name                                | myCirrosServer                      |
| adminPass                           | tVs5pL8HcPGw                        |
| tenant_id                           | 66265572db174a7aa66eba661f58eb9e    |
| created                             | 2013-07-16T16:25:34Z                |
| metadata                            | {u'KEY': u'VALUE'}                  |
+-------------------------------------+-------------------------------------+
```

如果状态是“BUILD”，意味着实例已启动，但尚未上线。

如果状态是“ACTIVE”，意味着实例已经可以用了。

2. 在打印出来的参数中查找ID这一项，这一项是服务器ID。您可以用这个ID获取到服务器详情，或者删掉这个服务器。

3. 在参数中查找adminPass项，将管理员密码复制出来。用这个密码来登录服务器。

>注意：
>您可以在创建实例时，将任意本地文件注入系统，用`--file <dst-path=src-path>`即可。您最多能存入五个文件。比如，您想用自定义的认证密钥文件放在实例中，而不是使用正常的SSH密钥注入的话，您可以用`--file`选项。如下是代码示例：
>```
>$ nova boot --image ubuntu-cloudimage --flavor 1 vn-name\
>--file /root/.ssh/authorized_keys=special_authorized_keysfile
>```

4. 检查一下实例是否已上线成功：

```
$ nova list
```

该列表显示您在这个Project下的服务器ID，名称，状态，私网IP（如果有公网IP也会显示）。

```
+-------------+----------------------+--------+------------+-------------+------------------+
| ID          | Name                 | Status | Task State | Power State | Networks         |
+-------------+----------------------+--------+------------+-------------+------------------+
| 84c6e57d... | myCirrosServer       | ACTIVE | None       | Running     | private=10.0.0.3 |
| 8a99547e... | myInstanceFromVolume | ACTIVE | None       | Running     | private=10.0.0.4 |
+-------------+----------------------+--------+------------+-------------+------------------+
```

如果实例的状态是`ACTIVE`，则该实例已经上线了。

5. 如果要查看所有`nova list`后面可以接的命令，可以执行以下命令：

```
$ nova help list
```

>注意：
>如果您不提供密钥对，安全组，或者安全组规则，那您只能通过云内提供的VNC来访问这个实例。连ping这个实例都ping不通。