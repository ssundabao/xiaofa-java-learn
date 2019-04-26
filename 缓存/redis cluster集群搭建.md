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

	

