## jdbc ##

什么是jdbc?

Java与数据库交互的统一API。实际包含两部分：一是面向Java程序，与底层所用数据库无关；二是面向数据库驱动，与具体的数据库产品有关。  

jdbc操作数据库步骤如下：  
- 注册驱动类，确定数据库url，username，password等相关信息，得到 DriverManager；  
- 使用 DriverManager 类创建数据库连接 Connection；  
- 通过数据库连接创建 Statement 对象；  
- 通过 Statement 执行 sql 语句，返回 ResultSet 对象；  
- 解析 ResultSet 对象，得到Java对象；  
- 关闭连接，释放资源。


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

## orm  框架 ##
从上述jdbc操作数据库的过程我们可以发现，步骤1-3，步骤6是完全一样的，步骤4只是每次的sql会不一样，除了步骤5以外，都可以公用，进行封装。
步骤5中将数据库的关系模型转化为java的对象模型。这种封装会比较麻烦，因此诞生了 orm（object relational mapping） 对象-关系映射。

除此之外，orm框架同时解决缓存、数据源、数据库连接池。

## Mybatis ##
本质是对jdbc的封装，增加事务松耦合，并实现了ORM框架的实体类和sql动态对应功能，但并不是完整的ORM框架，国内用Mybatis的公司很多，其实在国外，使用率很低。 

## 基础Mybatis操作数据库 ##
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
 
 