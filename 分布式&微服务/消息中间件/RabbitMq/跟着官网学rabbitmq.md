# 跟着官网学rabbitmq #

申明队列时报错：

     Caused by: com.rabbitmq.client.ShutdownSignalException: channel error; protocol method: #method<channel.close>(reply-code=406, reply-text=PRECONDITION_FAILED - inequivalent arg 'durable' for queue 'task_queue' in vhost '/': received 'true' but current is 'false', class-id=50, method-id=10)
    at com.rabbitmq.utility.ValueOrException.getValue(ValueOrException.java:66)
 
队列的持久化设置冲突，队列已经存在，并且durable之前和现在的设置值不同。queue的durable状态可在控制台查看。截图如下：  
![](https://raw.githubusercontent.com/zhaoxiaofa/xiaofa-java-learn/master/pictures/rabbitmq/rabbitmq-durable.png)   

