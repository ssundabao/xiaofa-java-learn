# kafka集群搭建 #
打开官网 http://kafka.apache.org/quickstart  
步骤基本就是：下载、解压、配置、启动、测试  

## 下载解压跳过 ##

## 配置 ##
采用自己搭建的zk集群作为kafka的注册中心。zk集群的配置、启动参见zookeeper。  

kafka配置，在默认的配置中修改三个配置。

    broker.id=2 # 每个几点的id不同
	listeners=PLAINTEXT://192.168.1.164:9092 #配置本服务器的ip
	zookeeper.connect=192.168.1.117:2181,192.168.1.153:2181,192.168.1.164:2181 #zk集群服务器配置 

## 启动 ##
进入到kafka的bin目录，本人是 /usr/local/develop/kafka_2.12-2.2.0/bin ，执行：  

    ./kafka-server-start.sh -daemon ../config/server.properties 

daemon表示后台启动。  

## 创建topic ##

    ./kafka-topics.sh --create --bootstrap-server 192.168.1.117:9092 --replication-factor 1 --partitions 1 --topic test

## 列出topic ##

    ./kafka-topics.sh --list --zookeeper 192.168.1.117:2181

## 发送消息 ##

    ./kafka-console-producer.sh --broker-list 192.168.1.117:9092 --topic test


## 消费消息 ##

    ./kafka-console-consumer.sh --bootstrap-server 192.168.1.117:9092 --topic test --from-beginning


