# 1. Mybatis原理 #
本质是对jdbc的封装，增加事务松耦合，并实现了ORM框架的实体类和sql动态对应功能，但并不是完整的ORM框架，国内用Mybatis的公司很多，其实在国外，使用率很低。 
   
## 1.1 jdbc对于数据库的操作


贴一段最基础的jdbc操作数据库的代码。

     public static void main(String[] args) throws Exception {

        String URL = "jdbc:mysql://127.0.0.1:3306/xiaofa?useUnicode=true&amp;characterEncoding=utf-8";
        String USER = "root";
        String PASSWORD = "123456";
        //1.加载驱动程序
        Class.forName("com.mysql.jdbc.Driver");
        //2.获得数据库链接
        Connection conn = DriverManager.getConnection(URL, USER, PASSWORD);
        //3.通过数据库的连接操作数据库，实现增删改查（使用Statement类）
        Statement st = conn.createStatement();
        ResultSet rs = st.executeQuery("select * from user");
        //4.处理数据库的返回结果(使用ResultSet类)
        while(rs.next()){
            System.out.println(rs.getString("table_name")+" "
                    +rs.getString("remark"));
        }
        //关闭资源
        rs.close();
        st.close();
        conn.close();

    }


## 1.2 基础Mybatis操作数据库 ##
结合一段最基础的Mybatis操作数据库的代码一步步debug了解其底层原理。

     public static void main(String[] args) throws IOException {
        User user = new User();
        user.setMobile("13812345678");
        user.setPassword("123456");
        InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        sqlSession.insert("insert", user);
        sqlSession.commit();
        sqlSession.close();
    }


### 1.2.1 Resources.getResourceAsStream("mybatis-config.xml") ###


### 1.2.2 new SqlSessionFactoryBuilder().build(inputStream) ###


### 1.2.3 sqlSessionFactory.openSession() ###


### 1.2.4 sqlSession.insert("insert", user) ###
 
 