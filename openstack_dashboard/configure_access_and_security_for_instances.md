### 权限和安全配置

在创建服务器实例之前，你需要在该实例上添加安全组规则，这样服务器实例的用户才能ping这个主机或者通过SSH连接这个实例。安全组是IP过滤的规则组，它决定了网络的连通性，适用于一个Project内的所有实例。配置安全组有两种方式：要么是在默认的安全组中添加规则，要么新建一个安全组，然后把规则写在新的安全组里。

密钥对是SSH的证书，在实例启动时被注入进系统里。如欲使用密钥对注入，镜像中的系统上必须安装了cloud-init包。每个Project必须有至少一个密钥对，欲知详情，请参看添加密钥对。

如果你使用了其他工具生产了密钥对，你可以将这个密钥对导入进OpenStack。密钥对可以用在一个Project下的多个实例里。欲知详情，请参看导入密钥对。

>注意：	
>一对密钥只属于一个独立的用户，而不属于Project。如果想让多个用户都用同一个密钥对的话，需要让每个用户都导入一次才可以。

在OpenStack中创建实例时，OpenStack会在可用的IP段中分配一个固定IP给这个实例。在这个实例的整个生命周期里，这个IP都和它相关联，只有删掉这个实例后这个IP才被释放。不过除了固定IP外，Floating IP也可以和实例相关联。和固定IP不同的是，floating IP可以无视实例的状态，随时被关联到一个不同的实例上。

#### 在默认的安全组上添加规则

以下操作过程介绍了如何在一个实例上启用SSH和ICMP（ping）权限。该操作对Project内的所有实例生效，也建议用户在所有的Project下执行这些操作，除非你们有什么特殊理由要禁用实例的SSH和ICMP。

如果你的云需要，也可以调整以下的步骤，从而添加别的安全组规则。

>注意：	
>添加规则的时候，必须指定源端口或目的端口使用的协议。

1. 登录控制台。
2. 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
3. 在Project选项卡中，打开Compute选项卡，点击```Access & Security```分类。在这里，安全组选项列出了当前Project适用的安全组信息。
4. 选择默认的安全组，点击“规则管理”。
5. 点击“添加规则”。
6. 在添加规则对话框，输入如下规则：
	
	```
	Rules: SSH			
	Remote: CIDR
	```
	>注意：	
	>如果要只允许某一个IP段内的主机访问，在CIDR框中填写相应的IP段即可。
	
7. 点击“添加”。	
	此时该Project下的所有实例的SSH端口（22）便都打开了。
8. 再次点击“添加规则”。
9. 在添加规则对话框，输入如下规则：
	
	```
	Rules: All ICMP	
	Remote: Ingress
	```
10. 点击“添加”。	
	此时该Project下的实例应该可以接收ICMP包了。

#### 添加密钥对

至少要给每一个Project都至少创建一个密钥对：

1. 登录控制台。
2. 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
3. 在Project选项卡中，打开Compute选项卡，点击```Access & Security```分类。
4. 点击密钥对选项卡，该选项卡内会显示该Project内所有可用的密钥对。
5. 点击创建密钥对。
6. 在创建密钥对窗口，输入密钥对的名字，然后点击创建密钥对。
7. 下载生成的密钥对。

#### 导入密钥对

1. 登录控制台。
2. 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
3. 在Project选项卡中，打开Compute选项卡，点击```Access & Security```分类。
4. 点击密钥对选项卡，该选项卡内会显示该Project内所有可用的密钥对。
5. 点击导入密钥对。
6. 在导入密钥对对话框，输入你要导入的密钥对的名字，将公钥粘贴进公钥框，然后点击导入密钥对。
7. 在本地保存```*.pem```文件。
8. 如果要将私钥修改成只有你能访问，使用如下命令：

	```
	$ chmod 0600 yourPrivateKey.pem
	```
	>注意：	
	>如果你在用Windows访问OpenStack控制台，用PuTTYgen导入```*.gem```文件，然后转换成```*.ppk```。欲知详情，请参阅WinSCP web page for PuTTYgen。

9. 通过运行```ssh-add```来导入密钥。

	```
	$ ssh-add yourPrivateKey.pem
	```
	此时你电脑上的电脑便注册了该密钥对的公钥。
	
	在OpenStack控制台中，所有密钥对都会在Access & Security选项卡中列出。

#### 为服务器实例分配Floating IP

在OpenStack中创建实例时，OpenStack会在可用的IP段中分配一个固定IP给这个实例。在这个实例的整个生命周期里，这个IP都和它相关联，只有删掉这个实例后这个IP才被释放。

不过，除了固定IP外，Floating IP也可以和实例相关联。和固定IP不同的是，floating IP可以在无视实例状态的情况下随时被关联到一个不同的实例上。

以下步骤详述了将一个IP从IP资源池中预留出来变成Floating IP，和把这个IP分配给某个实例的过程。

1. 登录控制台。
2. 在Project选项卡中，进入CURRENT PROJECT，然后选择目标Project。
3. 在Project选项卡中，打开Compute选项卡，点击```Access & Security```分类。
4. 点击Floating IP选项卡，该选项卡中会显示所有分配给实例的Floating IP。
5. 点击“Allocate IP To Project”。
6. 选择IP地址的来源IP池。
7. 点击“Allocate IP”。
8. 在“Floating IPs”列表中，点击“Associate”。
9. 在“Manage Floating IP Associations”对话框中，选择如下选项。
	- “IP Address”一栏是自动填好的，但是你也可以用“+”按钮添加一个新的IP地址。
	- 在“Port to be associated”一栏，在列表中选择一个端口。

	这个列表列出了所有实例和这些实例绑定的固定IP地址。
10. 点击“Associate”。

>注意：	
>如果要解绑IP，请点击“Disassociate”按钮。

如果要将Floating IP重新放回IP资源池，点击“More”，然后点击“Release Floating IP”。