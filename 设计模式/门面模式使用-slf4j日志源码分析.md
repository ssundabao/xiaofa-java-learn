## 1. 为什么要使用slf4j ##
在阿里巴巴Java开发手册中关于日志使用有这样一条：
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/log/alibaba.png)

**为什么要使用slf4j，举个例子：**  
> 我们自己的系统中使用了logback这个日志系统  
> 我们的系统使用了A.jar，A.jar中使用的日志系统为log4j  
> 我们的系统又使用了B.jar，B.jar中使用的日志系统为slf4j-simple  
> 这样，我们的系统就不得不同时支持并维护logback、log4j、slf4j-simple三种日志框架，非常不便。

解决这个问题的方式就是引入一个适配层，由适配层决定使用哪一种日志系统，而调用端只需要做的事情就是打印日志而不需要关心如何打印日志。**slf4j是一个日志标准，并不是日志系统的具体实现。**

slf4j的直接/间接实现有slf4j-simple、logback、slf4j-log4j12。

## 2. 测试使用slf4j ##

写的测试工具类：

    public class LogDemo {
    
	    public static void main(String[] args) {
	        Logger logger = LoggerFactory.getLogger(LogDemo.class);
	        logger.info("hello world");
	    }

	}

### 2.1 如果只引入slf4j的依赖,不会打印出日志。 ###

pom.xml配置如下：  

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.25</version>
    </dependency>

执行之后控制台打印结果：

    SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
	SLF4J: Defaulting to no-operation (NOP) logger implementation
	SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.

**解释：这说明slf4j不提供日志的具体实现，只有slf4j是无法打印日志的。**

### 2.2 配置中引入slf4j,logback ###

pom.xml配置如下：  

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.25</version>
    </dependency>
	<dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>

执行之后控制台打印结果：

    16:48:40.324 [main] INFO LogDemo - hello world

**解释：这说明引入一个实现类可以正常打印日志。**

### 2.3 配置中引入slf4j,并且引入了三种不同的设置 ###

pom.xml配置如下：

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.25</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.25</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.21</version>
    </dependency>

执行之后控制台打印结果：

    SLF4J: Class path contains multiple SLF4J bindings.
	SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/org/slf4j/slf4j-simple/1.7.25/slf4j-simple-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/org/slf4j/slf4j-log4j12/1.7.21/slf4j-log4j12-1.7.21.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
	SLF4J: Actual binding is of type [ch.qos.logback.classic.util.ContextSelectorStaticBinder]
	16:51:05.496 [main] INFO LogDemo - hello world

**解释：引入多个实现类可以正常打印日志，但会输出一些告警日志,提示我们同时引入了多个slf4j的实现，然后选择其中的一个作为我们使用的日志系统。**


## 3. 从源码看slf4j实现原理 ##

### 3.1 进入LoggerFactory.getLogger方法： ###

    public static Logger getLogger(Class<?> clazz) {
        Logger logger = getLogger(clazz.getName());
        if (DETECT_LOGGER_NAME_MISMATCH) {
            Class<?> autoComputedCallingClass = Util.getCallingClass();
            if (autoComputedCallingClass != null && nonMatchingClasses(clazz, autoComputedCallingClass)) {
                Util.report(String.format("Detected logger name mismatch. Given name: \"%s\"; computed name: \"%s\".", logger.getName(),
                                autoComputedCallingClass.getName()));
                Util.report("See " + LOGGER_NAME_MISMATCH_URL + " for an explanation");
            }
        }
        return logger;
    }


#### 3.1.1 getLogger(clazz.getName())-->getILoggerFactory()-->performInitialization() ####

主讲bind方法：

> Set<URL定义一个logger绑定路径集合，说明源码是支持可能多配置日志框架实现类的情况的；   
> **isAndroid() 这方法没看太懂**，看命名好像是校验是不是安卓端调用的，如果是安卓的话，就跳过校验，有懂的可以指导下，这个不影响；   
> 进入最重要的方法 findPossibleStaticLoggerBinderPathSet()，详见3.1.1.1；  
> reportMultipleBindingAmbiguity()方法是报告多配置框架，详见3.1.1.2；
> reportActualBinding()方法是报告实际使用的框架，详见详见3.1.1.3；

    private final static void bind() {
        try {
            Set<URL> staticLoggerBinderPathSet = null;
            // skip check under android, see also
            // http://jira.qos.ch/browse/SLF4J-328
            if (!isAndroid()) {
                staticLoggerBinderPathSet = findPossibleStaticLoggerBinderPathSet();
                reportMultipleBindingAmbiguity(staticLoggerBinderPathSet);
            }
            // the next line does the binding
            StaticLoggerBinder.getSingleton();
            INITIALIZATION_STATE = SUCCESSFUL_INITIALIZATION;
            reportActualBinding(staticLoggerBinderPathSet);
            fixSubstituteLoggers();
            replayEvents();
            // release all resources in SUBST_FACTORY
            SUBST_FACTORY.clear();
        } catch (NoClassDefFoundError ncde) {
            String msg = ncde.getMessage();
            if (messageContainsOrgSlf4jImplStaticLoggerBinder(msg)) {
                INITIALIZATION_STATE = NOP_FALLBACK_INITIALIZATION;
                Util.report("Failed to load class \"org.slf4j.impl.StaticLoggerBinder\".");
                Util.report("Defaulting to no-operation (NOP) logger implementation");
                Util.report("See " + NO_STATICLOGGERBINDER_URL + " for further details.");
            } else {
                failedBinding(ncde);
                throw ncde;
            }
        } catch (java.lang.NoSuchMethodError nsme) {
            String msg = nsme.getMessage();
            if (msg != null && msg.contains("org.slf4j.impl.StaticLoggerBinder.getSingleton()")) {
                INITIALIZATION_STATE = FAILED_INITIALIZATION;
                Util.report("slf4j-api 1.6.x (or later) is incompatible with this binding.");
                Util.report("Your binding is version 1.5.5 or earlier.");
                Util.report("Upgrade your binding to version 1.6.x.");
            }
            throw nsme;
        } catch (Exception e) {
            failedBinding(e);
            throw new IllegalStateException("Unexpected initialization failure", e);
        }
    }

**3.1.1.1 findPossibleStaticLoggerBinderPathSet 详解**

这个方法是查询logger绑定路径，解释下就是看哪些框架提供了日志功能。  
获取类加载器，之后通过方法loggerFactoryClassLoader.getResources(String name)对资源进行查找，name是 org/slf4j/impl/StaticLoggerBinder.class，返回path中包含以下三个元素。遍历之后如下：

> file:/D:/Java/maven/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar!/org/slf4j/impl/StaticLoggerBinder.class
> file:/D:/Java/maven/repository/org/slf4j/slf4j-simple/1.7.25/slf4j-simple-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class
> file:/D:/Java/maven/repository/org/slf4j/slf4j-log4j12/1.7.21/slf4j-log4j12-1.7.21.jar!/org/slf4j/impl/StaticLoggerBinder.class

而实际上项目的依赖中确实是这三个，截图如下：  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/log/org.slfj.impl.png)

返回的staticLoggerBinderPathSet集合中包含上面的三个jar包

    static Set<URL> findPossibleStaticLoggerBinderPathSet() {
        // use Set instead of list in order to deal with bug #138
        // LinkedHashSet appropriate here because it preserves insertion order
        // during iteration
        Set<URL> staticLoggerBinderPathSet = new LinkedHashSet<URL>();
        try {
            ClassLoader loggerFactoryClassLoader = LoggerFactory.class.getClassLoader();
            Enumeration<URL> paths;
            if (loggerFactoryClassLoader == null) {
                paths = ClassLoader.getSystemResources(STATIC_LOGGER_BINDER_PATH);
            } else {
                paths = loggerFactoryClassLoader.getResources(STATIC_LOGGER_BINDER_PATH);
            }
            while (paths.hasMoreElements()) {
                URL path = paths.nextElement();
                staticLoggerBinderPathSet.add(path);
            }
        } catch (IOException ioe) {
            Util.report("Error getting resources from path", ioe);
        }
        return staticLoggerBinderPathSet;
    }

**3.1.1.2 reportMultipleBindingAmbiguity 详解**

这个方法很简单，就是校验Set集合长度是否大于1,即是否配置了多个日志框架，如果是，则打印。
这也是2.3中打印的结果。
> SLF4J: Class path contains multiple SLF4J bindings.
> SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar!/org/slf4j/impl/StaticLoggerBinder.class]
> SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/org/slf4j/slf4j-simple/1.7.25/slf4j-simple-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
> SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/org/slf4j/slf4j-log4j12/1.7.21/slf4j-log4j12-1.7.21.jar!/org/slf4j/impl/StaticLoggerBinder.class]
> SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.

	private static void reportMultipleBindingAmbiguity(Set<URL> binderPathSet) {
        if (isAmbiguousStaticLoggerBinderPathSet(binderPathSet)) {
            Util.report("Class path contains multiple SLF4J bindings.");
            for (URL path : binderPathSet) {
                Util.report("Found binding in [" + path + "]");
            }
            Util.report("See " + MULTIPLE_BINDINGS_URL + " for an explanation.");
        }
    }

**3.1.1.3 reportActualBinding 详解**

该方法同样校验Set集合长度是否大于1，即是否配置了多个日志框架，如果是，则打印最终选择哪个框架。
这也是2.3中打印的结果。
> SLF4J: Actual binding is of type[ch.qos.logback.classic.util.ContextSelectorStaticBinder]

    private static void reportActualBinding(Set<URL> binderPathSet) {
        // binderPathSet can be null under Android
        if (binderPathSet != null && isAmbiguousStaticLoggerBinderPathSet(binderPathSet)) {
            Util.report("Actual binding is of type [" + StaticLoggerBinder.getSingleton().getLoggerFactoryClassStr() + "]");
        }
    }


### 3.2 为什么最终选择的是logback： ###
我们看到最后打印了这样一行日志：

    SLF4J: Actual binding is of type [ch.qos.logback.classic.util.ContextSelectorStaticBinder]

bind()方法里有这样一行 ： `StaticLoggerBinder.getSingleton();`  
进入该方法发现是logback的实现。这里就有个疑问了，在LoggerFactory类中引入的是 `import org.slf4j.impl.StaticLoggerBinder;`  
而三个框架中都有这个类，并且路径完全一样，这时候是随机选择了一个吗？
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/log/logbackbinder.png)

这里做一个测试，新建两个项目模拟。每个项目都有一个LogTest类，且包路径完全一样，都有helloWorld方法。


![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/log/logdemo1.png)

![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/log/logdemo2.png)


将这两个项目打成jar包，引入到测试项目中。

    import com.xiaofa.log.LogTest;

	public class LogDemo {
	
	    public static void main(String[] args) {
	//        Logger logger = LoggerFactory.getLogger(LogDemo.class);
	//        logger.info("hello world");
	        LogTest.HelloWorld();
	    }
	
	}

执行后结果：

    logdemo1 hello world

**解释**：个人认为jvm选择了其中一个执行。


## 4. TODO Spring日志加载 ##

- 在debug的时候不要使用spring的web项目，最开始偷懒，在自己的公司项目中加了行日志，想打断点进入，可死活进入不到 bind 方法体内，原因是spring初始化的时候已经加载了很多框架中的日志，在加载第一个的时候已经初始化了。

    Logger logger = LoggerFactory.getLogger(Object.class);

    


    

## 5. 其他注意事项 ##


- 创建的项目不要是springboot项目，springboot项目在parent的依赖里面已经默认引入了slf4j和logback，不需要自己手动在pom文件中添加依赖；  