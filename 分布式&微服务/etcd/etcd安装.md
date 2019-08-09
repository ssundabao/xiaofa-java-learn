## 1. etcd安装（Mac）

###1.1 确认Mac安装了Homebrew

###1.2 安装命令

> brew install etcd

可能会失败，多试几次，和网络有关。

###1.3 启动

启动命令： 

> etcd

**注意事项：**启动时切换到root权限，否则创建数据文件时会报错。

切换root权限命令：

> sudo su

下面是无权限失败示例：

```shell
zhaoxiaofadeMacBook-Pro:local zhaoxiaofa$ etcd
2019-07-25 15:26:07.232501 I | etcdmain: etcd Version: 3.3.13
2019-07-25 15:26:07.232601 I | etcdmain: Git SHA: GitNotFound
2019-07-25 15:26:07.232609 I | etcdmain: Go Version: go1.12.4
2019-07-25 15:26:07.232614 I | etcdmain: Go OS/Arch: darwin/amd64
2019-07-25 15:26:07.232618 I | etcdmain: setting maximum number of CPUs to 4, total number of available CPUs is 4
2019-07-25 15:26:07.232630 N | etcdmain: failed to detect default host (default host not supported on darwin_amd64)
2019-07-25 15:26:07.232638 W | etcdmain: no data-dir provided, using default data-dir ./default.etcd
2019-07-25 15:26:07.232716 W | etcdmain: found invalid file/dir .touch under data dir default.etcd (Ignore this if you are upgrading etcd)
2019-07-25 15:26:07.233159 I | embed: listening for peers on http://localhost:2380
2019-07-25 15:26:07.233388 I | embed: listening for client requests on localhost:2379
2019-07-25 15:26:07.233604 C | etcdmain: cannot access data directory: open default.etcd/.touch: permission denied
```



下面是切换root权限后启动成功示例：

```shell
2019-07-25 15:29:46.311158 I | etcdmain: etcd Version: 3.3.13
2019-07-25 15:29:46.311291 I | etcdmain: Git SHA: GitNotFound
2019-07-25 15:29:46.311298 I | etcdmain: Go Version: go1.12.4
2019-07-25 15:29:46.311305 I | etcdmain: Go OS/Arch: darwin/amd64
2019-07-25 15:29:46.311312 I | etcdmain: setting maximum number of CPUs to 4, total number of available CPUs is 4
2019-07-25 15:29:46.311324 N | etcdmain: failed to detect default host (default host not supported on darwin_amd64)
2019-07-25 15:29:46.311333 W | etcdmain: no data-dir provided, using default data-dir ./default.etcd
2019-07-25 15:29:46.311584 W | etcdmain: found invalid file/dir .touch under data dir default.etcd (Ignore this if you are upgrading etcd)
2019-07-25 15:29:46.312205 I | embed: listening for peers on http://localhost:2380
2019-07-25 15:29:46.312414 I | embed: listening for client requests on localhost:2379
2019-07-25 15:29:46.320290 I | etcdserver: name = default
2019-07-25 15:29:46.320312 I | etcdserver: data dir = default.etcd
2019-07-25 15:29:46.320322 I | etcdserver: member dir = default.etcd/member
2019-07-25 15:29:46.320335 I | etcdserver: heartbeat = 100ms
2019-07-25 15:29:46.320343 I | etcdserver: election = 1000ms
2019-07-25 15:29:46.320351 I | etcdserver: snapshot count = 100000
2019-07-25 15:29:46.320368 I | etcdserver: advertise client URLs = http://localhost:2379
2019-07-25 15:29:46.320380 I | etcdserver: initial advertise peer URLs = http://localhost:2380
2019-07-25 15:29:46.320397 I | etcdserver: initial cluster = default=http://localhost:2380
2019-07-25 15:29:46.408344 I | etcdserver: starting member 8e9e05c52164694d in cluster cdf818194e3a8c32
2019-07-25 15:29:46.408480 I | raft: 8e9e05c52164694d became follower at term 0
2019-07-25 15:29:46.408511 I | raft: newRaft 8e9e05c52164694d [peers: [], term: 0, commit: 0, applied: 0, lastindex: 0, lastterm: 0]
2019-07-25 15:29:46.408519 I | raft: 8e9e05c52164694d became follower at term 1
2019-07-25 15:29:46.429317 W | auth: simple token is not cryptographically signed
2019-07-25 15:29:46.445567 I | etcdserver: starting server... [version: 3.3.13, cluster version: to_be_decided]
2019-07-25 15:29:46.445886 E | etcdserver: cannot monitor file descriptor usage (cannot get FDUsage on darwin)
2019-07-25 15:29:46.446061 I | etcdserver: 8e9e05c52164694d as single-node; fast-forwarding 9 ticks (election ticks 10)
2019-07-25 15:29:46.446414 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
2019-07-25 15:29:47.013882 I | raft: 8e9e05c52164694d is starting a new election at term 1
2019-07-25 15:29:47.013945 I | raft: 8e9e05c52164694d became candidate at term 2
2019-07-25 15:29:47.013983 I | raft: 8e9e05c52164694d received MsgVoteResp from 8e9e05c52164694d at term 2
2019-07-25 15:29:47.014071 I | raft: 8e9e05c52164694d became leader at term 2
2019-07-25 15:29:47.014107 I | raft: raft.node: 8e9e05c52164694d elected leader 8e9e05c52164694d at term 2
2019-07-25 15:29:47.014571 I | etcdserver: setting up the initial cluster version to 3.3
2019-07-25 15:29:47.014668 I | etcdserver: published {Name:default ClientURLs:[http://localhost:2379]} to cluster cdf818194e3a8c32
2019-07-25 15:29:47.014697 I | embed: ready to serve client requests
2019-07-25 15:29:47.015502 N | embed: serving insecure client requests on 127.0.0.1:2379, this is strongly discouraged!
2019-07-25 15:29:47.020283 N | etcdserver/membership: set the initial cluster version to 3.3
2019-07-25 15:29:47.020393 I | etcdserver/api: enabled capabilities for version 3.3
```



