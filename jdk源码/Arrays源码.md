## Arrays.asList()的坑 ##
使用Arrays.asList()是想将数组或一些元素转为集合,而你得到的集合并不一定是你想要的那个集合。先看几个示例，常用错误用法。 
### 错误一、基本类型的数组作为asList参数 ###

    public static void baseTypeTest() {
        int[] array = {1, 2, 3};
        List list = Arrays.asList(array);
        System.out.println(list.size());
    }

打印结果为1。

### 错误二、数组作为asList参数后，修改数组或List ###

    public static void updateArrays() {
        String[] arrays = {"aa", "bb", "cc"};
        List list = Arrays.asList(arrays);
        arrays[1] = "dd";
        list.set(2, "ee");
        System.out.println(JSON.toJSONString(arrays));
        System.out.println(JSON.toJSONString(list));
    }

输出结果：  

    ["aa","dd","ee"]
	["aa","dd","ee"]
可以发现，所以当外部数组或集合改变时，数组和集合会同步变化。

### 错误三、 数组作为asList参数后，添加或删除List元素###

    public static void addAndDeleteArrays() {
        String[] arrays = {"aa", "bb", "cc"};
        List list = Arrays.asList(arrays);
        list.add("dd");
        list.remove("aa");
        System.out.println(JSON.toJSONString(list));
    }

输出结果：

    Exception in thread "main" java.lang.UnsupportedOperationException
	at java.util.AbstractList.add(AbstractList.java:148)
	at java.util.AbstractList.add(AbstractList.java:108)
	at com.xiaofa.common.Arrays.ArraysTest.addAndDeleteArrays(ArraysTest.java:35)
	at com.xiaofa.common.Arrays.ArraysTest.main(ArraysTest.java:47)

无法进行添加和删除，会直接抛异常。

## 源码分析 ##

### 错误一原理解析： ###

