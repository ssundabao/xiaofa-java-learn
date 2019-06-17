**Mybatis编程步骤**  

- 创建 SqlSessionFactory 对象。
- 通过 SqlSessionFactory 获取 SqlSession 对象。
- 通过 SqlSession 获得 Mapper 代理对象。
- 通过 Mapper 代理对象，执行数据库操作。
- 执行成功，则使用 SqlSession 提交事务。
- 执行失败，则使用 SqlSession 回滚事务。
- 关闭会话。




**Mapper接口怎么和xml对应起来的**





**Mapper接口类怎么操作数据库的**





