# RabbitMq基础 #


# Linux环境下搭建单机RabbitMq #
本人是在虚拟机上搭建的，安装rabbitmq需要安装erlang，还涉及版本之间的兼容问题，安装过程并不简单，在搭建环境的过程中，也踩过不少坑，安装方法按照官网来，网上有很多安装教程，但是质量良莠不齐，还是官网最可靠。  
https://www.rabbitmq.com/install-rpm.html（rabbitmq下载安装官网）  
### 1、安装erlang  ### 
官网提供了三个方式，Erlang Solutions、EPEL、openSUSE，选择第一种好了。  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/rabbitmq-erlang.jpg)
点击跳转到Erlang Solutions的官网下载页，https://www.erlang-solutions.com/resources/download.html。鼠标往下拉，选择 Installation using repository 的方式。  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/rabbitmq-erlang-solution.jpg)  

1、先执行下载命令（执行1 Adding repository entry）：  
wget https://packages.erlang-solutions.com/erlang-solutions-1.0-1.noarch.rpm  
2、再执行更新包命令，更新包时可能会报错，先执行 yum install epel-release 之后再执行更新命令。  
rpm -Uvh erlang-solutions-1.0-1.noarch.rpm  
Alternatively: adding the repository entry manually 下的可以不执行。  
Adding repository with dependencies 下的可以不执行。  
3、最后执行 yum install erlang 。执行时间很长（大约两个小时，不得不佩服国内的网速），慢慢等待。前后下载两小时，还失败了。  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/erlang-error.jpg)  
4、执行完毕后查看erlang版本，命令：erl  
5、选择对应的rabbitmq的版本，对应关系图官网已经给了，参考下面链接，**版本一定要兼容**。  
https://www.rabbitmq.com/which-erlang.html  

### 2、安装rabbitmq ###
官网上的第一种方式 Using PackageCloud Yum Repository ，打开之后还要登录，放弃，使用 Using Bintray Yum Repository 。  
1、导入签名：  
rpm --import https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc  
2、配置yum源，按照官网来就行。
3、安装命令：  
yum install rabbitmq-server-3.7.14-1.el7.noarch.rpm
注意点：  
1、yum update执行时间会很长，慢慢等，等待的时间可以复习下相关概念；  
2、在安装elixir后要配置环境变量，注意替换掉文档中的path为实际安装路径；




#参考资料#
https://www.jianshu.com/p/dae5bbed39b1