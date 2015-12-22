### 管理实例和主机

“实例”，是运行在云中物理主机上的虚拟机。Compute服务管理着众多的实例。“主机”是一组实例栖身的物理节点。

本章介绍了实例管理中的各种不同的任务，比如添加浮动IP，关闭和启动实例，以及删除实例等等。本节也介绍了一些物理节点的管理任务。

#### 管理IP地址

每个实例都有一个内网的固定IP，不过还可以为其添加一个对外的IP，或者叫“浮动IP”。内网IP是用来和其他实例间通信的，而公网是用来和外网的云设施通信的，而且也经常用来和互联网上的设备通信。

当您创建了一个实例，OpenStack会自动为其分配一个内网IP，这个地址一直会保持不变，直到您将该实例销毁。您重启实例也不会影响这个内网IP。

浮动IP资源池则是由云管理员设置的，该服务在OpenStack Compute中可用。每个Project的资源配额决定了您能在这个Project分配的浮动IP的最大数量。在您给某个实例分配了一个浮动IP之后，您能够：

- 将该浮动IP地址和Project中的这个实例相关联。但是，一个浮动IP，同一时间内只能分配给一个实例。
- 将该浮动IP从实例上解除关联。
- 从Project中删除浮动IP，并且连带删除该IP的各种关联。

您可以使用`nova floating-ip-*`命令来管理浮动IP地址。

##### 列出浮动IP信息

列出所有的浮动IP资源池，请使用如下命令：

```
$ nova floating-ip-pool-list
+--------+
| name   |
+--------+
| public |
| test   |
+--------+
```

>注意：
>如果这个列表里没有东西，云管理员必须先创建一个浮动IP资源池。

想列出分配给当前Project的所有浮动IP地址，请使用如下命令：

```
$ nova floating-ip-list
+--------------+--------------------------------------+----------+--------+
| Ip           | Instance Id                          | Fixed Ip | Pool   |
+--------------+--------------------------------------+----------+--------+
| 172.24.4.225 | 4a60ff6a-7a3c-49d7-9515-86ae501044c6 | 10.0.0.2 | public |
| 172.24.4.226 | None                                 | None     | public |
+--------------+--------------------------------------+----------+--------+
```

对于每个分配给当前Project的浮动IP，上面的这个命令能返回以下四个结果：该浮动IP地址，该浮动IP关联的实例的ID，关联的固定IP地址，以及这个浮动IP地址的来源池。

##### 分配浮动IP

您可以将浮动IP分配给Project，也可以分配给instance。

1. 运行如下命令，将一个浮动IP分配给当前Project。默认情况下，该浮动IP是从公共的资源池而来的。该命令的返回结果中会显示本次分配的地址。

```
$ nova floating-ip-create
+--------------+-------------+----------+--------+
| Ip           | Instance Id | Fixed Ip | Pool   |
+--------------+-------------+----------+--------+
| 172.24.4.225 | None        | None     | public |
+--------------+-------------+----------+--------+
```

>注意：
>如果IP地址资源池不止有一个，您可以将资源池名称作为参数传递给该命令，来制定要使用哪一个资源池。比如，要从`test`池中抽取IP地址，请使用如下命令：
>```
>$ nova floating-ip-create test
>```

2. 列出该Project下所有可以关联浮动IP地址的实例：

```
$ nova list
+---------------------+------+---------+------------+-------------+------------------+
| ID                  | Name | Status  | Task State | Power State | Networks         |
+---------------------+------+---------+------------+-------------+------------------+
| d5c854f9-d3e5-4f... | VM1  | ACTIVE  | -          | Running     | private=10.0.0.3 |
| 42290b01-0968-43... | VM2  | SHUTOFF | -          | Shutdown    | private=10.0.0.4 |
+---------------------+------+---------+------------+-------------+------------------+
```

3. 用如下命令，将IP地址和实例相关联：

```
$ nova floating-ip-associate INSTANCE_NAME_OR_ID FLOATING_IP_ADDRESS
```

比如像下面这样：

```
$ nova floating-ip-associate VM1 172.24.4.225
```

此时，该实例便已经和两个IP地址绑定了。

```
$ nova list
+------------------+------+--------+------------+-------------+-------------------------------+
| ID               | Name | Status | Task State | Power State | Networks                      |
+------------------+------+--------+------------+-------------+-------------------------------+
| d5c854f9-d3e5... | VM1  | ACTIVE | -          | Running     | private=10.0.0.3, 172.24.4.225|
| 42290b01-0968... | VM2  | SHUTOFF| -          | Shutdown    | private=10.0.0.4              |
+------------------+------+--------+------------+-------------+-------------------------------+
```

您在关联了该IP地址，且为实例配置了安全组规则之后，该实例便能通过浮动IP来访问了。

>注意：
>如果某个实例连接了多个网络，用`--fixed-address`即可将浮动IP和一个固定IP关联。
>```
>$ nova floating-ip-associate --fixed-address FIXED_IP_ADDRESS \
>  INSTANCE_NAME_OR_ID FLOATING_IP_ADDRESS
>```

##### 回收浮动IP

若要解除浮动IP和实例的关联，请使用如下命令：

```
$ nova floating-ip-disassociate INSTANCE_NAME_OR_ID FLOATING_IP_ADDRESS
```

若要解除浮动IP和Project，请使用如下命令：

```
$ nova floating-ip-delete FLOATING_IP_ADDRESS
```

此时，该IP地址会回归地址资源池，可以被所有Project使用。如果您直接将某一个浮动IP和Project解除了关联，且正挂在某个运行的实例上时，该IP同样会被自动收回。

#### 调整服务器大小

通过调整型号来调整服务器大小。

1. 请首先查看服务器信息，包括服务器的型号大小，这里的型号大小是体现在`flavor`上的：

```
$ nova show myCirrosServer
+-------------------------------------+-------------------------------------+
| Property                            | Value                               |
+-------------------------------------+-------------------------------------+
| status                              | ACTIVE                              |
| updated                             | 2013-07-18T15:08:20Z                |
| OS-EXT-STS:task_state               | None                                |
| OS-EXT-SRV-ATTR:host                | devstack                            |
| key_name                            | None                                |
| image                               | cirros-0.3.2-x86_64-uec (397e71...  |
| private network                     | 10.0.0.3                            |
| hostId                              | 6e1e69b71ac9b1e6871f91e2dfc9a9b9... |
| OS-EXT-STS:vm_state                 | active                              |
| OS-EXT-SRV-ATTR:instance_name       | instance-00000005                   |
| OS-EXT-SRV-ATTR:hypervisor_hostname | devstack                            |
| flavor                              | m1.small (2)                        |
| id                                  | 84c6e57d-a6b1-44b6-81eb-fcb36afd31b5|
| security_groups                     | [{u'name': u'default'}]             |
| user_id                             | 376744b5910b4b4da7d8e6cb483b06a8    |
| name                                | myCirrosServer                      |
| created                             | 2013-07-18T15:07:59Z                |
| tenant_id                           | 66265572db174a7aa66eba661f58eb9e    |
| OS-DCF:diskConfig                   | MANUAL                              |
| metadata                            | {u'description': u'Small test ima...|
| accessIPv4                          |                                     |
| accessIPv6                          |                                     |
| progress                            | 0                                   |
| OS-EXT-STS:power_state              | 1                                   |
| OS-EXT-AZ:availability_zone         | nova                                |
| config_drive                        |                                     |
+-------------------------------------+-------------------------------------+
```

如上显示，该服务器的型号是`m1.small(2)`

2. 用如下的方法列出可用的型号

```
$ nova flavor-list
+-----+-----------+-----------+------+-----------+------+-------+-------------+----------+
| ID  | Name      | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public|
+-----+-----------+-----------+------+-----------+------+-------+-------------+----------+
| 1   | m1.tiny   | 512       | 1    | 0         |      | 1     | 1.0         | True     |
| 2   | m1.small  | 2048      | 20   | 0         |      | 1     | 1.0         | True     |
| 3   | m1.medium | 4096      | 40   | 0         |      | 2     | 1.0         | True     |
| 4   | m1.large  | 8192      | 80   | 0         |      | 4     | 1.0         | True     |
| 5   | m1.xlarge | 16384     | 160  | 0         |      | 8     | 1.0         | True     |
+-----+-----------+-----------+------+-----------+------+-------+-------------+----------+
```

3. 如果您希望重新调整服务器大小，请使用`nova resize`命令，后面加上服务器ID或者名称，以及新的型号。想要即时查看容量调整过程，请使用`--poll`参数。例如：

```
$ nova resize myCirrosServer 4 --poll

Instance resizing... 100% complete
Finished
```

>注意：
>默认情况下，`nova resize`这个命令会给服务器实例自主关机的时间，此后才会执行关闭电源和调整大小的动作。关机的行为是在`nova.conf`中的`shutdown_timeout`参数下配置的。这个值表示OpenStack预留给服务器实例执行关机操作的总时间，单位为秒。默认情况下这个时间是60秒。详情请参看Description of Compute configuration options。

4. 查看服务器状态

```
$ nova list
+----------------------+----------------+--------+-----------------------------------------+
| ID                   | Name           | Status | Networks                                |
+----------------------+----------------+--------+-----------------------------------------+
| 84c6e57d-a6b1-44b... | myCirrosServer | RESIZE | private=172.16.101.6, public=10.4.113.6 |
+----------------------+----------------+--------+-----------------------------------------+
```

容量调整完成后，这里的状态将变为`VERIFY_RESIZE`。

5. 确认容量调整完成：

```
$ nova resize-confirm 84c6e57d-a6b1-44b6-81eb-fcb36afd31b5
```

此时服务器状态应为`ACTIVE`。

6. 如果容量调整未能按预期完成，您可以回滚调整操作：

```
$ nova resize-revert 84c6e57d-a6b1-44b6-81eb-fcb36afd31b5
```

此时服务器状态也应为`ACTIVE`。


#### 关闭和开启实例

您可以使用下面的几种方式来关闭和开启实例。

##### 暂停和恢复实例

如果要暂停实例，请使用如下命令：

```
$ nova pause INSTANCE_NAME
```

该命令会把服务器实例的状态存储在内存中，暂停了的实力会依然运行，只是如同被封冻一样，不会有任何动作。想要恢复实例，请使用如下命令：

```
$ nova unpause INSTANCE_NAME
```

##### 停用和恢复实例

如果要在虚拟化软件层面上将某个实例停用，请使用如下命令：

```
$nova suspend INSTANCE_NAME
```

如果要恢复一个被停用的实例，请使用如下命令：

```
$nova resume INSTANCE_NAME
```

##### 雪藏和恢复实例

如果您有一个暂时不用的实例，但还想让它留在您的服务器列表上，您便可能需要雪藏的功能了。例如，您可以在一周结束时雪藏一个实例，然后在下周一重新打开这个实力。所有相关的数据和资源会被保留，但雪藏时内存的东西是不会保存的。如果一个被雪藏的实例再也没用了，也可以被彻底删除。

您可以执行如下几种有关雪藏的任务：

- 雪藏实例：关闭服务器实例，将关联数据和资源保存（如果没有volume backed，将会创建一个快照）。内存中的信息将会丢失。

```
$ nova shelve SERVERNAME
```

>注意：
>默认情况下，`nova shelve`这个命令会给服务器实例自主关机的时间，此后才会执行关闭电源和调整大小的动作。关机的行为是在`nova.conf`中的`shutdown_timeout`参数下配置的。这个值表示OpenStack预留给服务器实例执行关机操作的总时间，单位为秒。默认情况下这个时间是60秒。详情请参看Description of Compute configuration options。
>关机时间也可以被镜像中的元数据配置覆盖掉。镜像元数据中的`os_shutdown_timeout`参数表示该系统需要多长时间才能完成一次关机操作，该参数的优先级要高于`nova.conf`中的配置。

- 恢复被雪藏的实例：

```
$ nova unshelve SERVERNAME
```

- 删除被雪藏的实例：将该实例删除，删掉所有的数据，回收资源。如果您不再需要某个实例，您可以将该实例从云中抹除，以节省空间。

```
$ nova shelve-offload SERVERNAME
```

#### 通过IP地址查找实例

在使用`nova list`时，您可以用IP地址来查找实例，只需添加`--ip`即可。

```
$ nova list --ip IP_ADDRESS
```

如下示例展示了如何查询IP为`10.0.0.4`的实例：

```
$ nova list --ip 10.0.0.4
+------------------+----------------------+--------+------------+-------------+------------------+
| ID               | Name                 | Status | Task State | Power State | Networks         |
+------------------+----------------------+--------+------------+-------------+------------------+
| 8a99547e-7385... | myInstanceFromVolume | ACTIVE | None       | Running     | private=10.0.0.4 |
+------------------+----------------------+--------+------------+-------------+------------------+
```

#### 重启实例

重启实例有两种，分别被称为“软重启”和“硬重启”。软重启会尝试正常关机并重启实例，硬重启会直接将实例“断电”并重启。

默认情况下，如果您通过`nova`重启，执行的是软重启。

```
$ nova reboot SERVER
```

如果您需要执行硬重启，添加`--hard`参数即可：

```
$ nova reboot --hard SERVER
```

您在重启时还可以进入恢复模式。例如，在您的实例使用时间过长之后，文件系统有损坏时，您可能就需要进入恢复模式了。

>注意：
>实例运行在“恢复模式”下时，暂停、停用和关闭操作是不可用的。因为这些操作会使实例的原始状态丢失，并且无法退出“恢复模式”。

“恢复模式”提供了一种访问实例的机制，这种机制在实例不可访问时依然有效。默认情况下，“恢复模式”会使用初始的景象启动一个实例，然后将当前的启动盘作为第二启动盘挂载到这个实例上。

如果要在“恢复模式”下启动某个实例，请使用如下命令：

```
$ nova rescue SERVER
```

>注意：
>默认情况下，`nova rescue`这个命令会给服务器实例自主关机的时间，此后才会执行关闭电源和调整大小的动作。关机的行为是在`nova.conf`中的`shutdown_timeout`参数下配置的。这个值表示OpenStack预留给服务器实例执行关机操作的总时间，单位为秒。默认情况下这个时间是60秒。详情请参看Description of Compute configuration options。
>关机时间也可以被镜像中的元数据配置覆盖掉。镜像元数据中的`os_shutdown_timeout`参数表示该系统需要多长时间才能完成一次关机操作，该参数的优先级要高于`nova.conf`中的配置。

如果要在正常模式下重启这个实例，请执行如下命令：

```
$ nova unrescue SERVER
```

如果您在“恢复模式”下启动实例时希望指定一个镜像，而不是用默认的那个镜像，请使用`--rescue_image_ref`参数：

```
$ nova rescue --rescue_image_ref IMAGE_ID SERVER
```

#### 删除实例

如果您不再需要某个实例，您可以删了它。

1. 列出所有实例：

```
$ nova list
+-------------+----------------------+--------+------------+-------------+------------------+
| ID          | Name                 | Status | Task State | Power State | Networks         |
+-------------+----------------------+--------+------------+-------------+------------------+
| 84c6e57d... | myCirrosServer       | ACTIVE | None       | Running     | private=10.0.0.3 |
| 8a99547e... | myInstanceFromVolume | ACTIVE | None       | Running     | private=10.0.0.4 |
| d7efd3e4... | newServer            | ERROR  | None       | NOSTATE     |                  |
+-------------+----------------------+--------+------------+-------------+------------------+
```

2. 运行`nova delete`命令来删除实例。下面的例子便是删除一个实例的做法，其中`newServer`实例的状态是`ERROR`。

```
$ nova delete newServer
```

如果您删除实例成功，是不会有提示的。

3. 如果您想确认服务器的确被删掉了，再执行一次`nova list`即可。

```
$ nova list
+-------------+----------------------+--------+------------+-------------+------------------+
| ID          | Name                 | Status | Task State | Power State | Networks         |
+-------------+----------------------+--------+------------+-------------+------------------+
| 84c6e57d... | myCirrosServer       | ACTIVE | None       | Running     | private=10.0.0.3 |
| 8a99547e... | myInstanceFromVolume | ACTIVE | None       | Running     | private=10.0.0.4 |
+-------------+----------------------+--------+------------+-------------+------------------+
```

被删除的实例已经不会显示在列表中了。

#### 通过控制台连接实例

如果要使用控制台连接实例，我们需要用到VNC或者SPICE。这两种都不在乎控制台日志是否有输出。控制台的方式可以将鼠标和键盘的信号通过中继发送到实例上。

OpenStack中，目前支持了三种远程连接控制台的方式：

**novnc**：一个通过HTML 5 Canvas和WebSockets实现的浏览器上的VNC客户端。
**spice**：一个运行在浏览器上的连接虚拟化实例的客户端。
**xvpvnc**：Java写成的连接服务器实例的控制端。

例如：

如果需要通过控制台访问实例，请执行如下命令：

```
$ nova get-vnc-console INSTANCE_NAME VNC_TYPE
```

该命令会返回一个URL，通过这个URL，您便可以访问控制台了。

```
+--------+------------------------------------------------------------------------------+
| Type   | Url                                                                          |
+--------+------------------------------------------------------------------------------+
| xvpvnc | http://192.168.5.96:6081/console?token=c83ae3a3-15c4-4890-8d45-aefb494a8d6c  |
+--------+------------------------------------------------------------------------------+
```

上面的指令中，`VNC_TYPE`可以是前述三种连接方式的任意一种。

在使用SPICE方式时，您可以直接在实例的页面使用浏览器插件来使用，也可以用`get-vnc-console`命令，获取到一个带认证信息的URL来访问使用。上面的例子便是后一种。

欲了解更多信息，或者这三者的对比（包括安全性考虑），请参看Security
Guide。

#### 管理裸机（bare-metal）节点

OpenStack Compute的裸机驱动，能通过一些公用的云API，或Orchestration（Heat）来管理硬件主机设备。这个驱动的用例主要是一些单租户的云平台，例如高性能计算平台，或是部署OpenStack本身的时候。

如果您使用裸机驱动，您必须创建一个网络接口，并且将其加在裸机节点上。然后您便可以通过裸机镜像来启动实例了。

您可以列出和删除裸机节点。当您删除一个节点时，与之关联的网络接口都将会被删除。您也可以列出或移除和某个裸机节点相关联的网络接口。

##### 常用指令

管理裸机节点时，我们一般使用如下命令：

`baremetal-interface-add`
将某个网络接口添加在裸机节点上。

`baremetal-interface-list`
列出和某个裸机节点关联的网络接口。

`baremetal-node-create`
创建一个裸机节点。

`baremetal-node-delete`
删除一个裸机节点，同时删除和它关联的所有接口。

`baremetal-node-list`
列出所有可用的裸机节点。

`baremetal-node-show`
列出某一裸机节点的信息。

##### 创建一个bare-metal节点

在您创建裸机节点时，您提供的PM地址，用户名和密码应该匹配您硬件设备中BIOS或IPMI上的配置。

```
$ nova baremetal-node-create --pm_address PM_ADDRESS --pm_user PM_USERNAME \
  --pm_password PM_PASSWORD $(hostname -f) 1 512 10 aa:bb:cc:dd:ee:ff
```

如下示例展示了创建节点的命令，其中PM地址是`1.2.3.4`，PM用户名是`ipmi`，密码是`ipmi`。

```
$ nova baremetal-node-create --pm_address 1.2.3.4 --pm_user ipmi \
  --pm_password ipmi $(hostname -f) 1 512 10 aa:bb:cc:dd:ee:ff
+------------------+-------------------+
| Property         | Value             |
+------------------+-------------------+
| instance_uuid    | None              |
| pm_address       | 1.2.3.4           |
| interfaces       | []                |
| prov_vlan_id     | None              |
| cpus             | 1                 |
| memory_mb        | 512               |
| prov_mac_address | aa:bb:cc:dd:ee:ff |
| service_host     | ubuntu            |
| local_gb         | 10                |
| id               | 1                 |
| pm_user          | ipmi              |
| terminal_port    | None              |
+------------------+-------------------+
```

##### 在该节点上添加网卡口

对于该节点上的每一个网卡，您都必须创建一个接口，并且指定该接口的MAC地址。

```
$ nova baremetal-interface-add 1 aa:bb:cc:dd:ee:ff
+-------------+-------------------+
| Property    | Value             |
+-------------+-------------------+
| datapath_id | 0                 |
| id          | 1                 |
| port_no     | 0                 |
| address     | aa:bb:cc:dd:ee:ff |
+-------------+-------------------+
```

##### 从一份bare-metal镜像创建实例

“裸机实例”是直接创建在物理机上的虚拟机实例，其下没有任何虚拟层。Nova通过IPMI来管理其电源。有些时候，Nova会通过Neutron和OpenFlow来管理网络。

```
 $ nova boot --image my-baremetal-image --flavor my-baremetal-flavor test
 +-----------------------------+--------------------------------------+
 | Property                    | Value                                |
 +-----------------------------+--------------------------------------+
 | status                      | BUILD                                |
 | id                          | cc302a8f-cd81-484b-89a8-b75eb3911b1b |
 +-----------------------------+--------------------------------------+

... wait for instance to become active ...
```

>注意：
>通过`--availability_zone`来指定在那个区，或哪个节点上启动服务器。并用逗号将区和主机名分开。
>
>```
>$ nova boot --availability_zone zone:HOST,NODE
>```
>`host`（主机名）是可选的。您可以只写`zone:,node`，但记得要加逗号。

##### 列出bare-metal节点和接口

用`nova baremetal-node-list`来查看所有的裸机设备和接口。如果节点正在使用中，这个信息表中会同时显示实例的UUID。

##### 查看bare-metal节点的详细信息

用`nova baremetal-node-show`命令来查看某个裸机设备的详情：

```
$ nova baremetal-node-show 1
+------------------+--------------------------------------+
| Property         | Value                                |
+------------------+--------------------------------------+
| instance_uuid    | cc302a8f-cd81-484b-89a8-b75eb3911b1b |
| pm_address       | 1.2.3.4                              |
| interfaces       | [{u'datapath_id': u'0', u'id': 1,    |
|                  | u'port_no': 0,                       |
|                  | u'address': u'aa:bb:cc:dd:ee:ff'}]   |
| prov_vlan_id     | None                                 |
| cpus             | 1                                    |
| memory_mb        | 512                                  |
| prov_mac_address | aa:bb:cc:dd:ee:ff                    |
| service_host     | ubuntu                               |
| local_gb         | 10                                   |
| id               | 1                                    |
| pm_user          | ipmi                                 |
| terminal_port    | None                                 |
+------------------+--------------------------------------+
```