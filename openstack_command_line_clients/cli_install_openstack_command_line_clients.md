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

	