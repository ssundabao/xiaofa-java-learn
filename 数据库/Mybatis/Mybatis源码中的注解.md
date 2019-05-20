### @BeforeAll ###
NpeExtendsTest：

    @BeforeAll
    static void initDatabase() throws Exception {
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactoryWithConstructor();

        BaseDataTest.runScript(sqlSessionFactory.getConfiguration().getEnvironment().getDataSource(),
                "org/apache/ibatis/submitted/extends_with_constructor/CreateDB.sql");
    }


