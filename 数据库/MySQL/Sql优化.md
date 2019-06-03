## LIMIT 语句 ##
看下面一条sql，一般情况下我们会在 type, name, create_time 字段上加组合索引。这样条件排序都能有效的利用到索引，性能迅速提升。
    
	SELECT * 
	FROM   test 
	WHERE  type = 'a' 
	       AND name = 'b' 
	ORDER  BY create_time 
	LIMIT  1000, 10;

但当 LIMIT 子句变成 “LIMIT 1000000,10” 时，你会发现我只取10条记录为什么还是慢？要知道数据库也并不知道第1000000条记录从什么地方开始，即使有索引也需要从头计算一次。  

在前端数据浏览翻页，或者大数据分批导出等场景下，是可以将上一页的最大值当成参数作为查询条件的。SQL 重新设计如下：  

    SELECT * 
	FROM   test 
	WHERE  type = 'a' 
	       AND name = 'b' 
	AND      create_time > '2017-03-16 14:00:00'
	ORDER  BY create_time 
	LIMIT  1000, 10;

但是这里其实也有一个问题，如果出现两个同样的create_time怎么办？是不是就漏掉了一条数据，这种大数据量的分页查询还是用条件筛选会比较好。此案例就当做提供了一种思路吧，具体能否使用就看实际业务场景了。  


## 隐式转换索引失效 ##

前置条件：city_id为vachar,有索引。**MySQL的策略是将字符串转换为数字之后再比较。函数作用于表字段，索引失效。**

    explain SELECT * FROM `city` WHERE city_id = 201;
	show warnings;
	
show warnings的结果为：  

    Cannot use ref access on index 'city_id' due to type or collation conversion on field 'city_id'
	Cannot use range access on index 'city_id' due to type or collation conversion on field 'city_id'



	
