### 管理镜像

用户的角色是由云管理员分配的。不同的角色决定了一个用户是否有上传和管理镜像的权限。云管理员一般都会将上传和管理镜像的权限限制在管理员范围内。

您可以用```glance```工具上传镜像，可以用```nova```工具管理镜像。后者可以让您列出镜像和删除镜像，设置和删除镜像元数据，以及用快照或备份的形式创建运行中的实例的镜像。

一旦您上传了镜像，就没办法更改了。

欲了解创建镜像的内容，请参阅Virtual Machine Image Guide章节。

#### 列出镜像，查看某个镜像的详情

要列出镜像，以及查看某个镜像的详情，请使用```glance image-list```和```glance image-show```命令。

```
$ glance image-list
+----------+---------------------------------+-------------+------------------+----------+--------+
| ID       | Name                            | Disk Format | Container Format | Size     | Status |
+----------+---------------------------------+-------------+------------------+----------+--------+
| 397e7... | cirros-0.3.2-x86_64-uec         | ami         | ami              | 25165824 | active |
| df430... | cirros-0.3.2-x86_64-uec-kernel  | aki         | aki              | 4955792  | active |
| 3cf85... | cirros-0.3.2-x86_64-uec-ramdisk | ari         | ari              | 3714968  | active |
| 7e514... | myCirrosImage                   | ami         | ami              | 14221312 | active |
+----------+---------------------------------+-------------+------------------+----------+--------+
```

```
$ glance image-show myCirrosImage

+---------------------------------------+--------------------------------------+
| Property                              | Value                                |
+---------------------------------------+--------------------------------------+
| Property 'base_image_ref'             | 397e713c-b95b-4186-ad46-6126863ea0a9 |
| Property 'image_location'             | snapshot                             |
| Property 'image_state'                | available                            |
| Property 'image_type'                 | snapshot                             |
| Property 'instance_type_ephemeral_gb' | 0                                    |
| Property 'instance_type_flavorid'     | 2                                    |
| Property 'instance_type_id'           | 5                                    |
| Property 'instance_type_memory_mb'    | 2048                                 |
| Property 'instance_type_name'         | m1.small                             |
| Property 'instance_type_root_gb'      | 20                                   |
| Property 'instance_type_rxtx_factor'  | 1                                    |
| Property 'instance_type_swap'         | 0                                    |
| Property 'instance_type_vcpu_weight'  | None                                 |
| Property 'instance_type_vcpus'        | 1                                    |
| Property 'instance_uuid'              | 84c6e57d-a6b1-44b6-81eb-fcb36afd31b5 |
| Property 'kernel_id'                  | df430cc2-3406-4061-b635-a51c16e488ac |
| Property 'owner_id'                   | 66265572db174a7aa66eba661f58eb9e     |
| Property 'ramdisk_id'                 | 3cf852bd-2332-48f4-9ae4-7d926d50945e |
| Property 'user_id'                    | 376744b5910b4b4da7d8e6cb483b06a8     |
| checksum                              | 8e4838effa1969ad591655d6485c7ba8     |
| container_format                      | ami                                  |
| created_at                            | 2013-07-22T19:45:58                  |
| deleted                               | False                                |
| disk_format                           | ami                                  |
| id                                    | 7e5142af-1253-4634-bcc6-89482c5f2e8a |
| is_public                             | False                                |
| min_disk                              | 0                                    |
| min_ram                               | 0                                    |
| name                                  | myCirrosImage                        |
| owner                                 | 66265572db174a7aa66eba661f58eb9e     |
| protected                             | False                                |
| size                                  | 14221312                             |
| status                                | active                               |
| updated_at                            | 2013-07-22T19:46:42                  |
+---------------------------------------+--------------------------------------+
```

在查看镜像列表时，您还可以用```grep```用关键词过滤列表：

```
$ glance image-list | grep 'cirros'
| 397e713c-b95b-4186-ad46-612... | cirros-0.3.2-x86_64-uec         | ami | ami | 25165824 | active |
| df430cc2-3406-4061-b635-a51... | cirros-0.3.2-x86_64-uec-kernel  | aki | aki | 4955792  | active |
| 3cf852bd-2332-48f4-9ae4-7d9... | cirros-0.3.2-x86_64-uec-ramdisk | ari | ari | 3714968  | active |
```

>注意：
>为了存储镜像的存储位置元数据，使得客户端能直接存取文件，请在```/etc/glance/glance-api.conf```文件中维护以下内容。	
> - ```show_multiple_localtions = True```
> - ```filesystem_store_metadata_file = FILEPATH```，其中FILEPATH指向了一个JSON文件，该文件保存了您OpenStack镜像的挂载路径和一个唯一ID：
>```
>[{
>	"id": "2d9bb53f-70ea-4066-a68b-67960eaae673",
>    "mountpoint": "/var/lib/glance/images/"
>}]
>```
>您重启了镜像服务之后，您便可以通过如下的命令来查看镜像的位置信息了：
>```
>$ glance --os-image-api-version 2 image-show imageID
>```
>比如，使用刚刚我们给出的image ID，您可以使用如下命令：
>```
>$ glance --os-image-api-version 2 image-show 2d9bb53f-70ea-4066-a68b-67960eaae673
>```

#### 创建或上传镜像（glance）

如果要创建镜像，请使用```glance image-create```命令：

```
$ glance image-create IMAGENAME
```

如果要通过名称或ID更新一个镜像，请使用```glance image-update```：

```
$ glance image-update IMAGENAME
```

以下列表列出了您可以在```create```和```update```时添加的参数。这些参数用于修改各种镜像属性。想了解更多详情，请参阅OpenStack Command-Line Interface Reference一文中镜像服务的章节。

- ```--name NAME```
	镜像的名字。
- ```--disk-format DISK_FORMAT```
	镜像的格式。可接受的格式有ami, ari, aki, vhd, vmdk, raw, qcow2, vdi和iso。
- ```--container-format CONTAINER_FORMAT```
	镜像的容器格式。可接受的格式有ami, ari, aki, bare和ovf。
- ```--owner TENANT_ID --size SIZE```
	哪一个用户拥有这个镜像。以及镜像的大小，单位是byte。
- ```--min-disk DISK_GB```
	启动这个镜像最少需要的磁盘空间，单位是Gigabyte。
- ```--min-ram DISK_RAM```
	启动这个镜像最少需要的没存大小，单位是Megabyte。
- ```--location IMAGE_URL```
	这个镜像的数据的存放位置。例如，如果这个镜像存放在swift中，那您便可以将其指定为```swift://account:key@example.com/container/obj```
- ```--file FILE```
	更新镜像时需要上传的镜像文件。除此之外，您还可以通过stdin将镜像传递给客户端。
- ```--checksum CHECKSUM```
	镜像数据的哈希值，做校验用。
- ```--copy-from IMAGE_URL```
	和```--location```的使用方法类似，但是该项会让服务器立即将镜像数据复制到指定的镜像存储中。
- ```--is-public [True|False]```
	设置是否允许这个镜像对所有用户开放（默认只能给管理员用）。
- ```--is-protected [True|False]```
	设置该镜像能否被删掉。
- ```--property KEY=VALUE```
	和镜像相关联的其他设置。该参数可以多次使用。
- ```--purge-props```
	删除所有没在update操作中定义的选项。否则，那些没指定的都会被保留下来。
- ```--human-readable```
	用方便阅读的单位显示镜像大小。

下面的例子向您展示了如何在qcow2格式下上传一个CentOS 6.3镜像，且设置成公共可访问权限。

```
$ glance image-create --name centos63-image --disk-format qcow2 \
--container-format bare --is-public True --file ./centos63.qcow2
```

下面的例子向您展示了如何更新一个镜像的属性，包括硬盘种类，CD-ROM种类，和VIF模式。

```
$ glance image-update \ 
	--property hw_disk_bus=scsi \ 
	--property hw_cdrom_bus=ide \ 
	--property hw_vif_model=e1000 \ 
	f16-x86_64-openstack-sda
```

目前，libvirt虚拟化工具是根据虚拟机管理器的种类来决定硬盘，CD-ROM和VIF设备的模式的，```libvirt_type```设置在```/etc/nova/nova.conf```中。为了优化性能，libvirt默认会把disk和VIF（NIC）都配置成virtio。这样做的缺点是，对于那些没有virtio驱动的操作系统来说，就根本跑不起来系统了，比如BSD，Solaris和一些老版本的Linux和Windows。

如果您指定了一个不支持的硬盘或光盘格式，请参阅Disk and CD-ROM bus model values table。如果您指定了一个不支持的VIF格式，导致实例不能启动，请参阅VIF model values table。

可用的模式取决于libvirt_type设置，如下表所示。

**磁盘和光盘总线设置**

|libvirt_type setting|Supported model values|
|:--|:--|
|qemu or kvm|ide/scsi/virtio|
|xen|ide/xen|

**VIF模式设置**

|libvirt_type setting|Supported model values|
|:--|:--|
|qemu or kvm|e1000/ne2k_pci/pcnet/rtl8139/virtio|
|xen|e1000/netfront/ne2k_pci/pcnet/rtl8139|
|vmware|VirtualE1000/VirtualPCNet32/VirtualVmxnet|

#### 解决创建镜像过程中出现的问题

如果您在使用Image或Compute创建镜像时遇到了问题，以下信息也许会帮您解决问题：

- 首先，保证您使用的```qemu```版本在0.14版以上。这之前的版本会在```nova-compute.log```文件中留下```unknown option -s```的报错。
- 在```/var/log/nova-api.log```和```/var/log/nova-compute.log```文件中查看错误信息。