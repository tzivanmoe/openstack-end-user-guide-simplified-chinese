### 概览

每一个OpenStack项目都会提供命令行工具，这样一来，用户通过简单的命令便可使用项目的API了。比如，Compute服务就提供了nova这个命令行工具。

您可以在命令行里直接输入命令，也可以把这些命令写进脚本，或者一些自动化任务中。如果您有必要的身份认证信息（例如账号和密码），你还可以在远程使用这些命令。

这些命令行工具实际上是cURL+API请求的集合。OpenStack的API遵循RESTful API规范，运行在HTTP协议下。这些API包含了HTTP方法（method），URI，媒体类型（media type）和返回码（response code）。

OpenStack的API都是开源的Python客户端，能运行在Linux和Mac OS X上。有些API请求允许您添加debug参数，添加之后这些API请求便会显式地打印出来。这样会让您更加了解OpenStack的API调用。

作为终端用户，您可以使用OpenStack控制台查看分配给您的各种资源。此外，您可以使用本章的例子来创建其他种类和大小的服务器实例。

OpenStack服务和客户端。

|服务|客户端|包|简介|
|:--|:--|:--|:--|
|Application catalog|murano|python-muranoclient|创建和管理应用|
|Block Storage|cinder|python-cinderclient|创建和管理卷|
|Compute|nova|python-novaclient|创建和管理镜像，实例以及系统型号|
|Containers service|magnum|python-magnumclient|创建和管理容器|
|Database service|trove|python-troveclient|创建和管理数据库|
|Data processing|sahara|python-saharaclient|在OpenStack上创建和管理Hadoop集群|
|Deployment service|tuskar|python-tuskarclient|安排部署|
|Identity|keystone|python-keystoneclient|创建和管理用户，租户，角色，endpoint和认证信息|
|Image service|glance|python-glanceclient|创建和管理镜像|
|Key Manager service|barbican|python-barbicanclient|创建和管理密钥|
|Monitoring|monasca|python-monascaclient|监控方案|
|Networking|neutron|python-neutronclient|管理实例的网络配置|
|Object Storage|swift|python-swiftclient|对象存储服务中的统计信息收集，内容展示，元数据更新，文件的上传下载删除。（Gain access to an Object Storage installation for ad hoc processing|
|Orchestration|heat|python-heatclient|从templates中启动stack，查看运行中的stack（包括事件和资源），以及更新和删除stack|
|Rating service|cloudkitty|python-cloudkittyclient|评价服务|
|Shared file systems|manila|python-manilaclient|创建和管理共享的文件系统|
|Telemetry|celiometer|python-celiometerclient|创建和收集OpenStack中的各种指标|
|Telemetry v3|gnocchi|python-gnocchiclient|创建和收集OpenStack中的各种指标|
|Workflow service|mistral|python-mistralclient|OpenStack云服务的工作流系统|
|Common client|openstack|python-openstackclient|OpenStack项目的普适的客户端|