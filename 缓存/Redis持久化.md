## 1. Redis持久化的意义

数据备份、故障恢复——用于在遇到灾难性的故障时做数据恢复

比如支付宝之前因为电缆被挖出现几小时的服务故障。

## 2. 持久化方案

RDB和AOF