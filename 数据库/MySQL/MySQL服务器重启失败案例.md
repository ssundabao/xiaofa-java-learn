背景：mysql服务正常，修改端口号之后重启发生失败。  
第一次重启方式如下：  

    systemctl restart mysqld

报错如下：

    Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.

百度错误信息之后找到相关的博客（事实证明好像没啥用），博客地址如下：

[https://www.cnblogs.com/ivictor/p/5146247.html](https://www.cnblogs.com/ivictor/p/5146247.html)  
  

解决问题的根据方法是看报错的日志，百度 Could not open or create the system tablespace。删除掉这三个文件 ibdata1  ib_logfile0  ib_logfile1 ，重启之后成功。

    2019-05-27T08:13:05.394404Z 0 [ERROR] InnoDB: Cannot open datafile './ibdata1'
	2019-05-27T08:13:05.394417Z 0 [ERROR] InnoDB: Could not open or create the system tablespace. If you tried to add new data files to the system tablespace, and it failed here, you should now edit innodb_data_file_path in my.cnf back to what it was, and remove the new ibdata files InnoDB created in this failed attempt. InnoDB only wrote those files full of zeros, but did not yet use them in any way. But be careful: do not remove old data files which contain your precious data!
	2019-05-27T08:13:05.394426Z 0 [ERROR] InnoDB: Plugin initialization aborted with error Cannot open a file
	2019-05-27T08:13:05.698711Z 0 [ERROR] Plugin 'InnoDB' init function returned error.
	2019-05-27T08:13:05.698760Z 0 [ERROR] Plugin 'InnoDB' registration as a STORAGE ENGINE failed.
	2019-05-27T08:13:05.698789Z 0 [ERROR] Failed to initialize builtin plugins.
	2019-05-27T08:13:05.698795Z 0 [ERROR] Aborting



最终帮助解决问题的博客地址：

[https://www.linuxidc.com/Linux/2014-05/102229.htm](https://www.linuxidc.com/Linux/2014-05/102229.htm)


该解决方案也出现了问题，问题见这个博客:  
删除之后表能看到，但是无法打开。

[https://www.cnblogs.com/ivictor/p/5784258.html](https://www.cnblogs.com/ivictor/p/5784258.html)


中间有看的：

[https://blog.csdn.net/zhou75771217/article/details/82893997](https://blog.csdn.net/zhou75771217/article/details/82893997)



MySQL远程无法连接处理方案：  
