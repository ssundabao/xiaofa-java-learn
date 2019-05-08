## 1. 门面模式使用-日志源码分析 ##
写的测试工具类：

    public class LogDemo {
    
	    public static void main(String[] args) {
	        Logger logger = LoggerFactory.getLogger(LogDemo.class);
	        logger.info("hello world");
	    }

	}


进入LoggerFactory.getLogger方法：

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










    
控制台打印结果：

    SLF4J: Class path contains multiple SLF4J bindings.
	SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/D:/Java/maven/repository/org/slf4j/slf4j-simple/1.7.25/slf4j-simple-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
	SLF4J: Actual binding is of type [ch.qos.logback.classic.util.ContextSelectorStaticBinder]

