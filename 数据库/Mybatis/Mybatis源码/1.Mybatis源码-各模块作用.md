### @BeforeAll ###
NpeExtendsTest：用在静态方法上，在该类所有测试方法执行前执行。

    @BeforeAll
    static void initDatabase() throws Exception {
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactoryWithConstructor();

        BaseDataTest.runScript(sqlSessionFactory.getConfiguration().getEnvironment().getDataSource(),
                "org/apache/ibatis/submitted/extends_with_constructor/CreateDB.sql");
    }


![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/mybatis/before.png)

