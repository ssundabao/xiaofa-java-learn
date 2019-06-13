## 解析器模块的功能
- 一个功能，是对 XPath 进行封装，为 MyBatis 初始化时解析 mybatis-config.xml 配置文件以及映射配置文件提供支持。  
- 另一个功能，是为处理动态 SQL 语句中的占位符提供支持。

![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/mybatis/%E8%A7%A3%E6%9E%90%E5%99%A8%E8%B7%AF%E5%BE%84%E6%88%AA%E5%9B%BE.png)


**先仔细研读下所有类的源码实现。**

## XPathParser ##

[https://github.com/zhaoxiaofa/xiaofa-mybatis/blob/master/src/main/java/org/apache/ibatis/parsing/XPathParser.java](https://github.com/zhaoxiaofa/xiaofa-mybatis/blob/master/src/main/java/org/apache/ibatis/parsing/XPathParser.java "XPathParser源码详解")



## GenericTokenParser ##

