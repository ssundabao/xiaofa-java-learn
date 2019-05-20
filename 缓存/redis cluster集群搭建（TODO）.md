# redis cluster集群搭建 #
Redis-Cluster采用无中心结构，每个节点保存数据和整个集群状态,每个节点都和其他所有节点连接。  
以当前最新稳定版本5.0为例，我使用三台虚拟机服务器，ip分别为：192.168.101.11 192.168.101.13 192.168.101.14 
redis包保存路径为 /usr/local/develop 

## 1. 下载、解压、编译、安装 ##
官网地址：https://redis.io/download

    wget http://download.redis.io/releases/redis-5.0.4.tar.gz

    tar -zxvf redis-5.0.4.tar.gz

    cd redis-5.0.4/

    make

	cd src

	make install

    
## 2. 配置 ##

    cd /usr/local/develop

	mkdir redis-cluster

	cd redis-cluster

	mkdir 7000 7001

复制默认的redis.conf文件到 7000 7001目录下，并修改其配置。如下配置需要修改：  

    port 7000 #端口号
	bind 0.0.0.0  #绑定可访问IP地址 设为无限制
    daemonize yes  #后台运行
    cluster-enabled yes #允许集群模式
    cluster-config-file nodes.conf #配置文件
    cluster-node-timeout 5000  #超时时间
    appendonly yes  
	protected-mode no #保护模式设置no,否则在5.0版本无法通过其他ip连接，之前版本好像不受影响

