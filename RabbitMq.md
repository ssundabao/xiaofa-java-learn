# RabbitMq基础 #


# Linux环境下搭建单机RabbitMq #
本人是在虚拟机上搭建的，安装rabbitmq需要安装erlang，还涉及版本之间的兼容问题，安装过程并不简单，在搭建环境的过程中，也踩过不少坑，安装方法按照官网来，网上有很多安装教程，但是质量良莠不齐，还是官网最可靠。  
https://www.rabbitmq.com/install-rpm.html（rabbitmq下载安装官网）  
1、安装erlang
官网提供了三个方式，Erlang Solutions、EPEL、openSUSE，选择第一种好了。  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/rabbitmq-erlang.jpg)
点击跳转到Erlang Solutions的官网下载页，https://www.erlang-solutions.com/resources/download.html。这里有个坑，不要选择download的方式，下了俩小时，硬是下载不下来。鼠标往下拉，选择 Installation using repository 的方式。  

注意点：  
1、yum update执行时间会很长，慢慢等，等待的时间可以复习下相关概念；  
2、在安装elixir后要配置环境变量，注意替换掉文档中的path为实际安装路径；




#参考资料#
https://www.jianshu.com/p/dae5bbed39b1