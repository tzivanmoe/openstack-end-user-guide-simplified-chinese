### 管理共享

共享是由文件存储提供的。您可以对实例开放存取共享文件的权限。如要创建共享，您可以使用```manila```命令行客户端。

#### 创建共享网络

1. 创建共享网络

```
$ manila share-network-create --name mysharenetwork --description "My Manila network" --neutron-net-id 394246ed-d3fd-4a30-a456-7042ce3429b9 --neutron-subnet-id 8f56d97d-8495-4a5b-8544-9ae4ee9390fc

+-------------------+--------------------------------------+
| Property          | Value                                |
+-------------------+--------------------------------------+
| name              | mysharenetwork                       |
| segmentation_id   | None                                 |
| created_at        | 2015-08-17T21:13:29.607489           |
| neutron_subnet_id | 8f56d97d-8495-4a5b-8544-9ae4ee9390fc |
| updated_at        | None                                 |
| network_type      | None                                 |
| neutron_net_id    | 394246ed-d3fd-4a30-a456-7042ce3429b9 |
| ip_version        | None                                 |
| nova_net_id       | None                                 |
| cidr              | None                                 |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791     |
| id                | ccd6b453-8b05-4508-bbce-93bfe660451f |
| description       | My Manila network                    |
+-------------------+--------------------------------------+
```
2. 列出共享网络

```
$ manila share-network-list

+--------------------------------------+----------------+
| id                                   | name           |
+--------------------------------------+----------------+
| ccd6b453-8b05-4508-bbce-93bfe660451f | mysharenetwork |
+--------------------------------------+----------------+
```

#### 创建共享

1. 创建一份共享

```
$ manila create --name myshare --description "My Manila share" --share-network ccd6b453-8b05-4508-bbce-93bfe660451f NFS 1

+-------------------+--------------------------------------+
| Property          | Value                                |
+-------------------+--------------------------------------+
| status            | creating                             |
| description       | My Manila share                      |
| availability_zone | nova                                 |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f |
| export_locations  | []                                   |
| host              | None                                 |
| snapshot_id       | None                                 |
| is_public         | False                                |
| id                | 2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| size              | 1                                    |
| name              | myshare                              |
| share_type        | default                              |
| created_at        | 2015-08-17T21:17:23.777696           |
| export_location   | None                                 |
| share_proto       | NFS                                  |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791     |
| metadata          | {}                                   |
+-------------------+--------------------------------------+
```

2. 显示一份共享的细节

```
$ manila show 2fe736d1-08ac-46f9-a482-8f224405f2a7

+-------------------+--------------------------------------+
| Property          | Value                                |
+-------------------+--------------------------------------+
| status            | creating                             |
| description       | My Manila share                      |
| availability_zone | nova                                 |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f |
| export_locations  | []                                   |
| host              | ubuntuManila@generic1#GENERIC1       |
| snapshot_id       | None                                 |
| is_public         | False                                |
| id                | 2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| size              | 1                                    |
| name              | myshare                              |
| share_type        | default                              |
| created_at        | 2015-08-17T21:17:23.000000           |
| export_location   | None                                 |
| share_proto       | NFS                                  |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791     |
| metadata          | {}                                   |
+-------------------+--------------------------------------+
```

3. 列出所有的共享

```
$ manila list

+--------------------------------------+---------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
| ID                                   | Name    | Size | Share Proto | Status    | Is Public | Share Type | Export location                                               | Host                           |
+--------------------------------------+---------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
| 2fe736d1-08ac-46f9-a482-8f224405f2a7 | myshare | 1    | NFS         | available | False     | default    | 10.254.0.3:/shares/share-2fe736d1-08ac-46f9-a482-8f224405f2a7 | ubuntuManila@generic1#GENERIC1 |
+--------------------------------------+---------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
```

#### 赋予访问权限

1. 赋予访问权限

```
$ manila access-allow 2fe736d1-08ac-46f9-a482-8f224405f2a7 ip 192.100.00.168

+--------------+--------------------------------------+
| Property     | Value                                |
+--------------+--------------------------------------+
| share_id     | 2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| deleted      | False                                |
| created_at   | 2015-08-17T21:36:52.025125           |
| updated_at   | None                                 |
| access_type  | ip                                   |
| access_to    | 192.100.00.168                       |
| access_level | rw                                   |
| state        | new                                  |
| deleted_at   | None                                 |
| id           | d73d04ca-a97e-42bb-94b1-e01c72c8e50e |
+--------------+--------------------------------------+
```

2. 列出权限列表

```
$ manila access-list 2fe736d1-08ac-46f9-a482-8f224405f2a7

+--------------------------------------+-------------+----------------+--------------+--------+
| id                                   | access type | access to      | access level | state  |
+--------------------------------------+-------------+----------------+--------------+--------+
| d73d04ca-a97e-42bb-94b1-e01c72c8e50e | ip          | 192.100.00.168 | rw           | active |
+--------------------------------------+-------------+----------------+--------------+--------+
```

#### 收回访问权限

1. 收回访问权限

```
$ manila access-deny 2fe736d1-08ac-46f9-a482-8f224405f2a7 d73d04ca-a97e-42bb-94b1-e01c72c8e50e
```

2. 列出权限列表

```
$ manila access-list 2fe736d1-08ac-46f9-a482-8f224405f2a7

+----+-------------+-----------+--------------+-------+
| id | access type | access to | access level | state |
+----+-------------+-----------+--------------+-------+
+----+-------------+-----------+--------------+-------+
```

#### 创建快照

1. 创建一份快照

```
$ manila snapshot-create --name mysnapshot --description "My Manila snapshot" 2fe736d1-08ac-46f9-a482-8f224405f2a7

+-------------+--------------------------------------+
| Property    | Value                                |
+-------------+--------------------------------------+
| status      | creating                             |
| share_id    | 2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| name        | mysnapshot                           |
| created_at  | 2015-08-17T21:50:53.295017           |
| share_proto | NFS                                  |
| id          | 1a411703-baef-495f-8e9c-b60e68f2e657 |
| size        | 1                                    |
| share_size  | 1                                    |
| description | My Manila snapshot                   |
+-------------+--------------------------------------+
```

2. 列出快照列表

```
$ manila snapshot-list

+--------------------------------------+--------------------------------------+-----------+------------+------------+
| ID                                   | Share ID                             | Status    | Name       | Share Size |
+--------------------------------------+--------------------------------------+-----------+------------+------------+
| 1a411703-baef-495f-8e9c-b60e68f2e657 | 2fe736d1-08ac-46f9-a482-8f224405f2a7 | available | mysnapshot | 1          |
+--------------------------------------+--------------------------------------+-----------+------------+------------+
```

#### 从快照创建共享

1. 从某份快照创建一个共享

```
$ manila create --snapshot-id 1a411703-baef-495f-8e9c-b60e68f2e657 --share-network ccd6b453-8b05-4508-bbce-93bfe660451f --name mysharefromsnap NFS 1

+-------------------+--------------------------------------+
| Property          | Value                                |
+-------------------+--------------------------------------+
| status            | creating                             |
| description       | None                                 |
| availability_zone | nova                                 |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f |
| export_locations  | []                                   |
| host              | ubuntuManila@generic1#GENERIC1       |
| snapshot_id       | 1a411703-baef-495f-8e9c-b60e68f2e657 |
| is_public         | False                                |
| id                | bcc5b2a7-862b-418a-9607-5d669619d652 |
| size              | 1                                    |
| name              | mysharefromsnap                      |
| share_type        | default                              |
| created_at        | 2015-08-17T21:54:43.000000           |
| export_location   | None                                 |
| share_proto       | NFS                                  |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791     |
| metadata          | {}                                   |
+-------------------+--------------------------------------+
```

2. 列出共享

```
$ manila list

+--------------------------------------+-----------------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
| ID                                   | Name            | Size | Share Proto | Status    | Is Public | Share Type | Export location                                               | Host                           |
+--------------------------------------+-----------------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
| 2fe736d1-08ac-46f9-a482-8f224405f2a7 | myshare         | 1    | NFS         | available | False     | default    | 10.254.0.3:/shares/share-2fe736d1-08ac-46f9-a482-8f224405f2a7 | ubuntuManila@generic1#GENERIC1 |
| bcc5b2a7-862b-418a-9607-5d669619d652 | mysharefromsnap | 1    | NFS         | creating  | False     | default    | None                                                          | ubuntuManila@generic1#GENERIC1 |
+--------------------------------------+-----------------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
```

3. 打印从快照创建出的这份共享的详情

```
$ manila show bcc5b2a7-862b-418a-9607-5d669619d652

+-------------------+---------------------------------------------------------------+
| Property          | Value                                                         |
+-------------------+---------------------------------------------------------------+
| status            | available                                                     |
| description       | None                                                          |
| availability_zone | nova                                                          |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f                          |
| export_locations  | 10.254.0.3:/shares/share-bcc5b2a7-862b-418a-9607-5d669619d652 |
| host              | ubuntuManila@generic1#GENERIC1                                |
| snapshot_id       | 1a411703-baef-495f-8e9c-b60e68f2e657                          |
| is_public         | False                                                         |
| id                | bcc5b2a7-862b-418a-9607-5d669619d652                          |
| size              | 1                                                             |
| name              | mysharefromsnap                                               |
| share_type        | default                                                       |
| created_at        | 2015-08-17T21:54:43.000000                                    |
| share_proto       | NFS                                                           |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791                              |
| metadata          | {}                                                            |
+-------------------+---------------------------------------------------------------+
```

#### 删除共享

1. 删除共享

```
$ manila delete bcc5b2a7-862b-418a-9607-5d669619d652
```

2. 列出共享

```
$ manila list

+--------------------------------------+-----------------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
| ID                                   | Name            | Size | Share Proto | Status    | Is Public | Share Type | Export location                                               | Host                           |
+--------------------------------------+-----------------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
| 2fe736d1-08ac-46f9-a482-8f224405f2a7 | myshare         | 1    | NFS         | available | False     | default    | 10.254.0.3:/shares/share-2fe736d1-08ac-46f9-a482-8f224405f2a7 | ubuntuManila@generic1#GENERIC1 |
| bcc5b2a7-862b-418a-9607-5d669619d652 | mysharefromsnap | 1    | NFS         | deleting  | False     | default    | 10.254.0.3:/shares/share-bcc5b2a7-862b-418a-9607-5d669619d652 | ubuntuManila@generic1#GENERIC1 |
+--------------------------------------+-----------------+------+-------------+-----------+-----------+------------+---------------------------------------------------------------+--------------------------------+
```

此时共享已删除。

#### 删除快照

1. 在删除快照之前，先列出快照

```
$ manila snapshot-list

+--------------------------------------+--------------------------------------+-----------+------------+------------+
| ID                                   | Share ID                             | Status    | Name       | Share Size |
+--------------------------------------+--------------------------------------+-----------+------------+------------+
| 1a411703-baef-495f-8e9c-b60e68f2e657 | 2fe736d1-08ac-46f9-a482-8f224405f2a7 | available | mysnapshot | 1          |
+--------------------------------------+--------------------------------------+-----------+------------+------------+
```

2. 删除快照

```
$ manila snapshot-delete 1a411703-baef-495f-8e9c-b60e68f2e657xyang@ubuntuManila:~/devstack$ manila snapshot-list
```

3. 再次列出快照列表

```
$ manila snapshot-list

+--------------------------------------+--------------------------------------+-----------+------------+------------+
| ID                                   | Share ID                             | Status    | Name       | Share Size |
+--------------------------------------+--------------------------------------+-----------+------------+------------+
+--------------------------------------+--------------------------------------+-----------+------------+------------+
```

此时快照已删除。

#### 为共享盘扩充空间

1. 扩展共享

```
$ manila extend 2fe736d1-08ac-46f9-a482-8f224405f2a7 2
```

2. 查看正在扩展的共享

```
$ manila show 2fe736d1-08ac-46f9-a482-8f224405f2a7

+-------------------+---------------------------------------------------------------+
| Property          | Value                                                         |
+-------------------+---------------------------------------------------------------+
| status            | extending                                                     |
| description       | My Manila share                                               |
| availability_zone | nova                                                          |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f                          |
| export_locations  | 10.254.0.3:/shares/share-2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| host              | ubuntuManila@generic1#GENERIC1                                |
| snapshot_id       | None                                                          |
| is_public         | False                                                         |
| id                | 2fe736d1-08ac-46f9-a482-8f224405f2a7                          |
| size              | 1                                                             |
| name              | myshare                                                       |
| share_type        | default                                                       |
| created_at        | 2015-08-17T21:17:23.000000                                    |
| share_proto       | NFS                                                           |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791                              |
| metadata          | {}                                                            |
+-------------------+---------------------------------------------------------------+
```

3. 在扩展动作结束后再次查看

```
$ manila show 2fe736d1-08ac-46f9-a482-8f224405f2a7

+-------------------+---------------------------------------------------------------+
| Property          | Value                                                         |
+-------------------+---------------------------------------------------------------+
| status            | available                                                     |
| description       | My Manila share                                               |
| availability_zone | nova                                                          |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f                          |
| export_locations  | 10.254.0.3:/shares/share-2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| host              | ubuntuManila@generic1#GENERIC1                                |
| snapshot_id       | None                                                          |
| is_public         | False                                                         |
| id                | 2fe736d1-08ac-46f9-a482-8f224405f2a7                          |
| size              | 2                                                             |
| name              | myshare                                                       |
| share_type        | default                                                       |
| created_at        | 2015-08-17T21:17:23.000000                                    |
| share_proto       | NFS                                                           |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791                              |
| metadata          | {}                                                            |
+-------------------+---------------------------------------------------------------+
```

#### 为共享盘缩减容量

1. 缩减共享盘容量

```
$ manila shrink 2fe736d1-08ac-46f9-a482-8f224405f2a7 1
```

2. 在缩减的过程中查看共享盘详情

```
$ manila show 2fe736d1-08ac-46f9-a482-8f224405f2a7

+-------------------+---------------------------------------------------------------+
| Property          | Value                                                         |
+-------------------+---------------------------------------------------------------+
| status            | shrinking                                                     |
| description       | My Manila share                                               |
| availability_zone | nova                                                          |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f                          |
| export_locations  | 10.254.0.3:/shares/share-2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| host              | ubuntuManila@generic1#GENERIC1                                |
| snapshot_id       | None                                                          |
| is_public         | False                                                         |
| id                | 2fe736d1-08ac-46f9-a482-8f224405f2a7                          |
| size              | 2                                                             |
| name              | myshare                                                       |
| share_type        | default                                                       |
| created_at        | 2015-08-17T21:17:23.000000                                    |
| share_proto       | NFS                                                           |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791                              |
| metadata          | {}                                                            |
+-------------------+---------------------------------------------------------------+
```

3. 在缩减过程结束后再次查看共享盘详情

```
$ manila show 2fe736d1-08ac-46f9-a482-8f224405f2a7

+-------------------+---------------------------------------------------------------+
| Property          | Value                                                         |
+-------------------+---------------------------------------------------------------+
| status            | available                                                     |
| description       | My Manila share                                               |
| availability_zone | nova                                                          |
| share_network_id  | ccd6b453-8b05-4508-bbce-93bfe660451f                          |
| export_locations  | 10.254.0.3:/shares/share-2fe736d1-08ac-46f9-a482-8f224405f2a7 |
| host              | ubuntuManila@generic1#GENERIC1                                |
| snapshot_id       | None                                                          |
| is_public         | False                                                         |
| id                | 2fe736d1-08ac-46f9-a482-8f224405f2a7                          |
| size              | 1                                                             |
| name              | myshare                                                       |
| share_type        | default                                                       |
| created_at        | 2015-08-17T21:17:23.000000                                    |
| share_proto       | NFS                                                           |
| project_id        | d80a6323e99f4f22a26ad2accd3ec791                              |
| metadata          | {}                                                            |
+-------------------+---------------------------------------------------------------+
```