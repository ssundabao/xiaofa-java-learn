## 原理

基于zookeeper的临时顺序节点。

## Curator实现

使用Curator客户端实现，不用自己手撸代码。

注意Curator版本和Zookeeper服务器版本的对应关系，否则会报异常。

> org.apache.zookeeper.KeeperException$UnimplementedException:

具体的版本对应关系参见

