# 1. 一个简单的生产环境可用的shell脚本 #
## 1.1 hello world ##
使用vim创建一个文件，hello.sh，编辑文件如下：  

    #!/bin/bash
    echo "Hello World !"

保存退出并赋权，赋权命令如下（执行之后文件会变为绿色）：  

    chmod +x ./hello.sh  #使脚本具有执行权限

脚本执行命令：  

    ./hello.sh  #执行脚本
注意：每个shell脚本的开头都是 #!/bin/bash 固定写法。

## 1.2 springboot项目启动脚本 ##
以下脚本为网上找的一个springboot项目启动脚本，从实际应用中学习会更快，更实用。


    #!/bin/bash
	version="1.0.1";
	
	appName=$2
	if [ -z $appName ];then
	    appName=`ls -t |grep .jar$ |head -n1`
	fi
	
	function start()
	{
		count=`ps -ef |grep java|grep $appName|wc -l`
		if [ $count != 0 ];then
			echo "Maybe $appName is running, please check it..."
		else
			echo "The $appName is starting..."
			nohup java -jar ./$appName -XX:+UseG1GC -XX:+HeapDumpOnOutOfMemoryError -Xms512M -Xmx4G > /dev/null 2>&1 &
		fi
	}
	
	function stop()
	{
		appId=`ps -ef |grep java|grep $appName|awk '{print $2}'`
		if [ -z $appId ];then
		    echo "Maybe $appName not running, please check it..."
		else
	        echo "The $appName is stopping..."
	        kill $appId
		fi
	}
	
	function restart()
	{
	    # get release version
	    releaseApp=`ls -t |grep .jar$ |head -n1`
	    
	    # get last version 
	    lastVersionApp=`ls -t |grep .jar$ |head -n2 |tail -n1`
	
	    appName=$lastVersionApp
	    stop
	    for i in {5..1}
	    do
	        echo -n "$i "
	        sleep 1
	    done
	    echo 0
	    
	    backup
	    
	    appName=$releaseApp
	    start
	}
	
	function backup() 
	{
	    # get backup version
	    backupApp=`ls |grep -wv $releaseApp$ |grep .jar$`
	    
	    # create backup dir
	    if [ ! -d "backup" ];then
	        mkdir backup
	    fi
	    
	    # backup
	    for i in ${backupApp[@]}
	    do
	        echo "backup" $i
	        mv $i backup
	    done
	}
	
	function status()
	{
	    appId=`ps -ef |grep java|grep $appName|awk '{print $2}'`
		if [ -z $appId ] 
		then
		    echo -e "\033[31m Not running \033[0m" 
		else
		    echo -e "\033[32m Running [$appId] \033[0m" 
		fi
	}
	
	
	function usage()
	{
	    echo "Usage: $0 {start|stop|restart|status|stop -f}"
	    echo "Example: $0 start"
	    exit 1
	}
	
	case $1 in
		start)
		start;;
	
		stop)
		stop;;
		
		restart)
		restart;;
		
		status)
		status;;
		
		*)
		usage;;
	esac
	
<br/> 
现在开始解析该脚本：
  
    appName=$2

解释下$符号的使用：  
$0：当前脚本的文件名  
$1：第一个参数  
$2：第二个参数  
appName=$2 的意思就是把执行命令时传入的第二个参数赋值给appName，我的执行命令是：  

    ./app.sh start 

没有指定第二个参数，如果有多个web项目在一台服务器上同时启动，就要在第二个参数指定项目名，例如有两个项目 hello.jar 和 world.jar ：

    ./app.sh start hello.jar

<br/> 
顺带解释下：  
    
    case $1 in
		start)
		start;;
	
		stop)
		stop;;
		
		restart)
		restart;;
		
		status)
		status;;
		
		*)
		usage;;
	esac

case ... esac 与Java中的 switch ... case 语句类似。$1取的是执行命令中的第一个参数，如上述例子中的 start。  
;; 类似Java中的break  
*) 类似Java中的default 没有找到匹配值   
<br/> 

    -z $appName
$appName是取值  
[ -z STRING ] “STRING” 的长度为零则为真。  
<br/>

    appName=`ls -t |grep .jar$ |head -n1`
ls -t :按时间排序列出  
|grep .jar$ 列出结果中匹配后缀为.jar的文件   $指定后缀  
head -n1 取一个值  
执行结果是将目前正在运行的第一个项目文件名称赋值给appName变量。  

<br/>
解析start函数

    function start()
	{
		count=`ps -ef |grep java|grep $appName|wc -l`
		if [ $count != 0 ];then
			echo "Maybe $appName is running, please check it..."
		else
			echo "The $appName is starting..."
			nohup java -jar ./$appName -XX:+UseG1GC -XX:+HeapDumpOnOutOfMemoryError -Xms512M -Xmx4G > /dev/null 2>&1 &
		fi
	}

ps -ef |grep java：搜索java进程  
ps -ef |grep java|grep $appName:在搜索结果中筛选想要操作的项目进程  
wc命令的功能为统计指定文件中的字节数、字数、行数, 并将统计结果显示输出。  
-l 统计行数  
ps -ef |grep java|grep $appName|wc -l 完整命令意思是：查询想要操作进程是否存在 
