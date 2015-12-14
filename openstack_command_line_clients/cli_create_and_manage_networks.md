### 创建和管理网络

在对OpenStack网络进行操作前，请先设置如下环境变量：

```
export OS_USERNAME=admin
export OS_PASSWORD=password
export OS_TENANT_NAME=admin
export OS_AUTH_URL=http://localhost:5000/v2.0
```

#### 创建网络

1. 列出neutron工具的扩展功能：

```
$ neutron ext-list -c alias -c name

+-----------------+--------------------------+
| alias           | name                     |
+-----------------+--------------------------+
| agent_scheduler | Agent Schedulers         |
| binding         | Port Binding             |
| quotas          | Quota management support |
| agent           | agent                    |
| provider        | Provider Network         |
| router          | Neutron L3 Router        |
| lbaas           | LoadBalancing service    |
| extraroute      | Neutron Extra Route      |
+-----------------+--------------------------+
```

2. 创建网络

```
$ neutron net-create net1

Created a new network:
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | True                                 |
| id                        | 2d627131-c841-4e3a-ace6-f2dd75773b6d |
| name                      | net1                                 |
| provider:network_type     | vlan                                 |
| provider:physical_network | physnet1                             |
| provider:segmentation_id  | 1001                                 |
| router:external           | False                                |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tenant_id                 | 3671f46ec35e4bbca6ef92ab7975e463     |
+---------------------------+--------------------------------------+
```

>注意：	
>使用`net-create`命令创建网络时，返回信息中的一些项是只有管理员才能看到的。

3. 创建网络时，指定网络类型：

```
$ neutron net-create net2 --provider:network-type local

Created a new network:
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | True                                 |
| id                        | 524e26ea-fad4-4bb0-b504-1ad0dc770e7a |
| name                      | net2                                 |
| provider:network_type     | local                                |
| provider:physical_network |                                      |
| provider:segmentation_id  |                                      |
| router:external           | False                                |
| shared                    | False                                |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tenant_id                 | 3671f46ec35e4bbca6ef92ab7975e463     |
+---------------------------+--------------------------------------+
```
正如上面展示的一样，刚刚我们用到的那个`--provider:network-type`能用来创建一个`local`的provider network。

#### 创建子网

```
$ neutron subnet-create net1 192.168.2.0/24 --name subnet1

Created a new subnet:
+------------------+--------------------------------------------------+
| Field            | Value                                            |
+------------------+--------------------------------------------------+
| allocation_pools | {"start": "192.168.2.2", "end": "192.168.2.254"} |
| cidr             | 192.168.2.0/24                                   |
| dns_nameservers  |                                                  |
| enable_dhcp      | True                                             |
| gateway_ip       | 192.168.2.1                                      |
| host_routes      |                                                  |
| id               | 15a09f6c-87a5-4d14-b2cf-03d97cd4b456             |
| ip_version       | 4                                                |
| name             | subnet1                                          |
| network_id       | 2d627131-c841-4e3a-ace6-f2dd75773b6d             |
| tenant_id        | 3671f46ec35e4bbca6ef92ab7975e463                 |
+------------------+--------------------------------------------------+
```

`subnet-create`命令有如下几个位置固定的参数，还有可选参数：

- 这个子网从属的网络的名字或者ID
	在本例中，`net1`这个参数是位置固定的。
- 子网的CIDR
	在本例中，`192.168.2.0/24`也是一个位置固定的参数，它标记了要创建的子网的CIDR。
- 子网的名称
	在本例中，`--name subnet1`指定了要创建的子网名。

#### 创建路由

1. 创建一个新路由

```
$ neutron router-create router1

Created a new router:
+-----------------------+--------------------------------------+
| Field                 | Value                                |
+-----------------------+--------------------------------------+
| admin_state_up        | True                                 |
| external_gateway_info |                                      |
| id                    | 6e1f11ed-014b-4c16-8664-f4f615a3137a |
| name                  | router1                              |
| status                | ACTIVE                               |
| tenant_id             | 7b5970fbe7724bf9b74c245e66b92abf     |
+-----------------------+--------------------------------------+
```

记下返回的路由识别码，这个编码是唯一的，在稍后的步骤中我们将用到它。

2. 将路由器连接到外部的provider network。

```
$ neutron router-gateway-set ROUTER NETWORK
```

将这条命令中的`ROUTER`字段用刚刚的路由识别码代替，将`NETWORK`字段用唯一的外部provider network识别码代替。

3. 将该路由和子网相连。

```
$ neutron router-interface-add ROUTER SUBNET
```

将这条命令中的`ROUTER`字段用刚刚的路由识别码代替，将`SUBNET`字段用唯一的子网代码代替。

#### 创建端口

1. 在指定的IP地址上创建一个端口

```
$ neutron port-create net1 --fixed-ip ip_address=192.168.2.40

Created a new port:
+----------------------+----------------------------------------------------------------------+
| Field                | Value                                                                |
+----------------------+----------------------------------------------------------------------+
| admin_state_up       | True                                                                 |
| binding:capabilities | {"port_filter": false}                                               |
| binding:vif_type     | ovs                                                                  |
| device_id            |                                                                      |
| device_owner         |                                                                      |
| fixed_ips            | {"subnet_id": "15a09f6c-87a5-4d14-b2cf-03d97cd4b456", "ip_address... |
| id                   | f7a08fe4-e79e-4b67-bbb8-a5002455a493                                 |
| mac_address          | fa:16:3e:97:e0:fc                                                    |
| name                 |                                                                      |
| network_id           | 2d627131-c841-4e3a-ace6-f2dd75773b6d                                 |
| status               | DOWN                                                                 |
| tenant_id            | 3671f46ec35e4bbca6ef92ab7975e463                                     |
+----------------------+----------------------------------------------------------------------+
```

在前一个指令中，`net1`参数表示的是网络名，该参数的位置要固定。`--fixed-ip ip_address=192.168.2.40`则是可选的，指定了该端口的绑定的IP地址是哪一个。

>注意：
>在创建端口时，您可以指定任意一个在子网中的未分配的IP地址，即便它不在您的云供应商提供的地址池内也可以。

2. 在不指定IP地址的情况下创建端口

```
$ neutron port-create net1

Created a new port:
+----------------------+----------------------------------------------------------------------+
| Field                | Value                                                                |
+----------------------+----------------------------------------------------------------------+
| admin_state_up       | True                                                                 |
| binding:capabilities | {"port_filter": false}                                               |
| binding:vif_type     | ovs                                                                  |
| device_id            |                                                                      |
| device_owner         |                                                                      |
| fixed_ips            | {"subnet_id": "15a09f6c-87a5-4d14-b2cf-03d97cd4b456", "ip_address... |
| id                   | baf13412-2641-4183-9533-de8f5b91444c                                 |
| mac_address          | fa:16:3e:f6:ec:c7                                                    |
| name                 |                                                                      |
| network_id           | 2d627131-c841-4e3a-ace6-f2dd75773b6d                                 |
| status               | DOWN                                                                 |
| tenant_id            | 3671f46ec35e4bbca6ef92ab7975e463                                     |
+----------------------+----------------------------------------------------------------------+
```

>注意：
>如果您在使用`neutron port-create`指令时不指定IP地址，系统会自动分配给您一个IP地址。

3. 通过固定的IP地址来查询端口

```
$ neutron port-list --fixed-ips ip_address=192.168.2.2 \
  ip_address=192.168.2.40

+----------------+------+-------------------+-------------------------------------------------+
| id             | name | mac_address       | fixed_ips                                       |
+----------------+------+-------------------+-------------------------------------------------+
| baf13412-26... |      | fa:16:3e:f6:ec:c7 | {"subnet_id"... ..."ip_address": "192.168.2.2"} |
| f7a08fe4-e7... |      | fa:16:3e:97:e0:fc | {"subnet_id"... ..."ip_address": "192.168.2.40"}|
+----------------+------+-------------------+-------------------------------------------------+
```

其中，`--fixed-ips ip_address=192.168.2.2 ip_address=192.168.2.40`是这个命令的unknown option。

**如何查找unknown option**：unknown option可以通过观察`create_xxx`或者`show_xxx`的指令来查找。比如，在用创建端口的命令时，我们能看见`fixed_ips`这一项，它便可以用作unknown option。