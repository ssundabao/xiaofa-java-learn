# RabbitMq基础 #


# Linux环境下搭建单机RabbitMq #
本人是在虚拟机上搭建的，安装rabbitmq需要安装erlang，还涉及版本之间的兼容问题，安装过程并不简单，在搭建环境的过程中，也踩过不少坑，安装方法按照官网来，网上有很多安装教程，但是质量良莠不齐，还是官网最可靠。  
https://www.rabbitmq.com/install-rpm.html（rabbitmq下载安装官网）  
### 1、安装erlang  ### 
官网提供了三个方式，Erlang Solutions、EPEL、openSUSE，选择第一种好了。  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/rabbitmq-erlang.jpg)

点击跳转到Erlang Solutions的官网下载页，https://www.erlang-solutions.com/resources/download.html。选择对应的Linux版本，我选择的是centos。鼠标往下拉，选择 Installation using repository 的方式。  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/rabbitmq-erlang-solution.jpg)  


1、先执行下载命令（执行1 Adding repository entry）：  
`wget https://packages.erlang-solutions.com/erlang-solutions-1.0-1.noarch.rpm`  

2、再执行更新包命令，更新包时可能会报错，此时执行  
`https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm ` 

安装epel-release成功之后,再执行更新   
`rpm -Uvh erlang-solutions-1.0-1.noarch.rpm `   
Alternatively: adding the repository entry manually 下的可以不执行。  
Adding repository with dependencies 下的可以不执行。  

3、最后执行   
`yum install erlang`   
执行时间根据网速不同，我在家里执行了两小时最后还失败了，在公司执行三分钟就完毕了。遇到这种情况，建议多试几次。   

4、执行完毕后查看erlang版本，命令：  
`erl `   

5、选择对应的rabbitmq的版本，对应关系图官网已经给了，参考下面链接，**版本一定要兼容**。  
https://www.rabbitmq.com/which-erlang.html  

### 2、安装rabbitmq ###
官网上的第一种方式 Using PackageCloud Yum Repository ，打开之后还要登录，故而放弃，使用 Using Bintray Yum Repository 。  

1、导入签名：  
`rpm --import https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc `   

2、配置yum源，按照官网来就行。  

3、列出安装源：    
`yum list | grep rabbitmq`  

4、选择版本安装   
`yum install rabbitmq-server-3.7.14-1.el7.noarch.rpm`  

5、安装成功后配置自启动  
`chkconfig rabbitmq-server on`
执行之后打印如下内容  
注意：正在将请求转发到“systemctl enable rabbitmq-server.service”。  
Created symlink from /etc/systemd/system/multi-user.target.wants/rabbitmq-server.service to /usr/lib/systemd/system/rabbitmq-server.service.
说明创建自启动成功。

一些常用命令：  
查看状态：  
rabbitmqctl status  
重启：  
service rabbitmq-server restart

### 3、管理界面配置 ###
进入命令目录：  
cd /usr/lib/rabbitmq/bin  

查看各插件：  
rabbitmq-plugins list  

开启控制台管理页面，执行命令：  
rabbitmq-plugins enable rabbitmq_management  

重启rabbitmq  

输入 http://192.168.1.238:15672 ，即可进入控制台页面  
默认使用 guest 账户登录，但是guest账号只支持localhost登录。我的rabbitmq是在虚拟机上安装的，在宿主机上无法登录。此时创建一个新的账号，命令如下：  

创建账号：  
rabbitmqctl add_user  user_admin  passwd_admin  

设置为管理员  
rabbitmqctl set_user_tags user_admin administrator  

给账户配置具体权限，如果想要在程序中使用该账号，必须配置权限，否则项目启动就会报错。  
rabbitmqctl set_permissions -p / user_admin ".*" ".*" ".*"  
配置权限成功以后可以看到，新增的账号和guest账号权限一样。  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/user-right.png)
重启rabbitmq  
此时可以使用新建的账号进行登录。
#参考资料#
https://www.rabbitmq.com/tutorials/tutorial-one-java.html
https://www.rabbitmq.com/download.html