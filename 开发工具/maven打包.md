在项目中经常会引入第三方jar包，当然最好的方法是将jar包上传到公司私服上，使用maven依赖引入到项目中使用，但是如果公司没有私服或者私服有问题（最近私服挂了），只能使用传统方法将jar包拷贝到项目目录下。然后按照如下配置之后，项目可顺利打包。

在src目录下建立lib目录，拷入jar包。在pom文件中添加如下配置。


    <!--添加外部依赖-->
    <dependency>
        <groupId>middle-util</groupId>
        <artifactId>middle-util</artifactId>
        <version>1.0</version>
        <scope>system</scope>
        <systemPath>${basedir}/src/lib/middle-util-0.0.1-SNAPSHOT.jar</systemPath>
    </dependency>


在build下引入resources  

    <resources>
        <resource>
            <directory>src/lib</directory>
            <targetPath>BOOT-INF/lib</targetPath>
            <includes>
                <include>**/*.jar</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <targetPath>BOOT-INF/classes</targetPath>
        </resource>
    </resources>

此时使用maven打包就成功了。