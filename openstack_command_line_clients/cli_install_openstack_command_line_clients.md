### 安装OpenStack命令行工具

让我们先来了解下如何安装OpenStack依赖的软件和Python包。

#### 安装依赖的软件

绝大多数Linux发行版都为您提供了可以直接安装的命令行工具包，详情请参见Installing from packages。

如果您需要用源码包安装这些命令行工具。下面的列表列出了您需要提前安装的软件，以及一些必要的安装提示。

- Python 2.7及其后续版本（但并不支持Python 3）
- setuptools
	- Mac OS X：已经默认安装好了。
	- Linux：绝大多数Linux发行版都提供了setuptools的安装包。您在您系统默认的软件包管理器上应该都能找到。如果找不到，您还可以直接在以下链接处下载到：https://pypi.python.org/pypi/setuptools
	- Windows：如果您要在Windows下安装setuptools，我们建议您参阅setuptools的网站：https://pypi.python.org/pypi/setuptools/。此外，您也可以下载由Christoph Gohlke维护的非官方安装包：http://www.lfd.uci.edu/~gohlke/pythonlibs/#setuptools。
- pip package
	- 在Linux，Mac OS X以及Windows上安装命令行工具，您都需要用到pip。pip使用起来很简单，并且能确保您下载到的一定是最新版的客户端，还能让您在日后方便地更新或删除您下载的包。	

	因为安装的过程中需要编译源码，因此在安装中您的系统上要有适用的Python开发包。

	通过包管理器来安装pip：

	Mac OS

	```
	# easy_install pip
	```

	Microsoft Windows

	使用之前请确保```C:\Python27\Scripts\这个文件夹路径在环境变量```PATH```中。然后通过```easy_install```命令来安装pip。
	此外，您也可以下载和使用由Christoph Gohlke维护的非官方安装包：http://www.lfd.uci.edu/~gohlke/pythonlibs/#setuptools。

	Ubuntu和Debian

	```
	# apt-get install python-dev python-pip
	```
	如果您用的操作系统版本不同，您可能需要不同的包。具体需要哪些包和您要安装哪些包有关，比如Tempest。

	Red Hat Enterprise Linux, CentOS 或者 Fedora

	您可以通过如下命令安装：

	```
	# yum install python-devel python-pip
	```

	RDO也提供了客户端的安装包，您可以参看Installing from packages，其中介绍了如何让yum获取RDO中的安装包。

	SUSE Linux Enterprise Linux 11

	在Open Build Service上有一个封装好的pip包，可以让您通过zypper安装pip。首先，您需要添加Open Build Service：

	```
	# zypper addrepo -f obs://Cloud:OpenStack: \ 
	kilo/SLE_12 Kilo
	```

	然后您就可以用zypper安装pip了：

	```
	# zypper install python-devel python-pip
	```

	如同Installing from packages描述的一样，这些OpenStack客户端也有直接封装好的版本，可以通过zypper直接安装。

	openSUSE

	您可以通过zypper直接安装pip：

	```
	# zypper install python-devel python-pip
	```

	如同Installing from packages描述的一样，这些OpenStack客户端也有直接封装好的版本，可以通过zypper直接安装。

#### 安装命令行客户端

在您参照本小节操作时，请将命令中的```"PROJECT"```替换为要安装的客户端的名字，例如```“nova”```。您安装每一个客户端，都要用这样的做法。以下值是有效的：

- barbican - 密钥管理服务API
- ceilometer - 遥测（？）API
- cinder - 块存储API及扩展。
- cloudkitty - 评价服务API
- glance - 镜像服务API
- gnocchi - 遥测服务APIv3
- heat - 统一编排API
- magnum - 容器服务API
- manila - 共享文件系统服务API
- mistral - 工作流服务API
- monasca - 监控API
- murano - 应用系列API
- neutron - 网络API
- nova - 计算API和扩展
- sahara - 数据处理API
- swift - 对象存储API
- trove - 数据库服务API
- tuskar - 部署服务API
- openstack - 支持多个OpenStack服务的全能型的命令行工具

以下命令行客户端已逐渐被那个叫openstack的全能型命令行替代。

- keystone - 身份认证服务API及扩展

如下例子介绍了如何通过命令行，用pip安装nova客户端。

```
# pip install python-novaclient

##### 通过pip安装

我们强烈建议您用pip来安装OpenStack命令行客户端，不管您用的是Linux，Mac OS X还是Microsoft Windows。pip安装非常的简单，能确保您用的都是最新版的客户端。此外pip还能让您更新或者卸载包。

每一个客户端都用下面这条命令来安装：

- Mac OS X或者Linux：
```
# pip install python-PROJECTclient
```

- Microsoft Windows：
```
C:\>pip install python-PROJECTclient
```

##### 用包管理器安装

RDO，openSUSE，SUSE Linux Enterprise，Debian和Ubuntu版本可以在不安装pip的情况下直接用包管理器来安装各自需要的包。 

- 在RHEL，CentOS，Fedora下，用yum来安装托管在RDO上的安装包：

```
# yum install python-PROJECTclient
```

- 在Debian或Ubuntu系统，用apt-get来安装各个包：

```
# apt-get install python-PROJECTclient
```

-在openSUSE下，您可以使用zypper从软件包服务中下载到各个客户端包：

```
# zypper install python-PROJECTclient
```

- 如果您用的时SUSE Linux Enterprise Server，您也可使用zypper来安装这些包。但您要先添加Open Build Servivce库。

```
# zypper addrepo -f obs://Cloud:OpenStack:Kilo/SLE_12 Kilo
```
然后您就可以通过如下命令安装各个包了：

```
# zypper install python-PROJECTclient
```

#### 升级或卸载命令行客户端

如果您要升级某个包，您可以在pip命令上添加参数：

```
# pip install --upgrade python-PROJECTclient
```

如果您要卸载某个包，请使用```pip uninstall```命令：

```
# pip uninstall python-PROJECTclient
```

#### 下一步要做的事

在您运行命令之前，您必须创建一个叫PROJECT-openrc.sh的文件，然后运行一次，以设置环境变量。请参见Set environment variable using the OpenStack RC file。