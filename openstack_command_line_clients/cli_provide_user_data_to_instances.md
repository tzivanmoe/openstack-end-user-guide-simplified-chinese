#### 为实例提供用户数据

用户数据文件时在元数据服务中的一个特殊的键，它保存了一份能给虚拟机实例中的云服务使用的文件。比如，```cloud-init```程序便使用了用户数据文件，这个程序是一个源自Ubuntu的开源包，能用在多个Linux发行版上，它可以接管云实例的初始化过程。

您可以将用户数据写在一份本地文件中，然后在创建实例时用`--user-data <user-data-file>`参数将其传入。

```
$ nova boot --image ubuntu-cloudimage --flavor 1 --user-data mydata.file
```