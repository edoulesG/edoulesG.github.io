---
layout: post
title: "java 处理时间、数组、字符等小轮子的集合"
author: martube
categories: technology
tags: [tool]
image: mountains.jpg
---

# java 处理时间、数组、字符等小轮子的集合

# banner 生成利器 http://patorjk.com/software/taag

## 时间

### 获得当天零时零分零秒
```java
    /**
     * 获得当天零时零分零秒
     * @return
     */
    public Date getDayBeginDate(Date date){
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(date);
        calendar.set(Calendar.HOUR, 0);
        calendar.set(Calendar.MINUTE, 0);
        calendar.set(Calendar.SECOND, 0);
        return calendar.getTime();
    }

```


### 获取某一日期是周几

```java
    //获取某一日期是周几
    public static String dayForWeek(String pTime) throws ParseException {

        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");

        Date tmpDate = format.parse(pTime);

        Calendar cal = Calendar.getInstance();

        String[] weekDays = { "7", "1", "2", "3", "4", "5", "6" };

        try {

            cal.setTime(tmpDate);

        } catch (Exception e) {

            e.printStackTrace();

        }

        int w = cal.get(Calendar.DAY_OF_WEEK) - 1; // 指示一个星期中的某天。

        if (w < 0)

            w = 0;

        return weekDays[w];

    }

```


### 获取n天后是多少号
```java
    public Timestamp addDate(Date date,int addDay){
        /*
         * 获取n天后是多少号?
         *
         * add(int field,int n)
         * 为当前calendar指定时间分量上累加给定值
         * 若传入的是负数，则是累减
         */
        Calendar cal = Calendar.getInstance();
        cal.setTime(date);
        cal.add(Calendar.DATE, addDay);//天数可正可负：88或者-88
        String tmpTime = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(cal.getTime());
        return Timestamp.valueOf(tmpTime);
    }
```



### 使用 Calendar 进行日期的加减

```java
      //获取时间加一年或加一月或加一天
        Date date = new Date();
        Calendar cal = Calendar.getInstance();
        cal.setTime(date);//设置起时间
        //System.out.println("111111111::::"+cal.getTime());

        //cal.add(Calendar.YEAR, 1);//增加一年
        //cal.add(Calendar.DATE, n);//增加一天  
        //cal.add(Calendar.DATE, -10);//减10天  
        cal.add(Calendar.MONTH, 1);//增加一个月 
        Date newDate = cal.getTime();

        Timestamp result = new Timestamp(newDate.getTime());

        System.out.println(result);
```

### timestamp自带的比较方法 .after() .before()
```java

gnTestPlannedWork.getPlannerDate().after(todayTime)

```



### 将Date转换为儒略日(julian day) 

TODO : julian day 来源 转换规律（JDE定义？）

```java


public class JulianUtils {

    public static Date julianToDate(int date) {
        int year = (date / 1000) + 1900;
        int dayOfYear = date % 1000;
        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.YEAR, year);
        calendar.set(Calendar.DAY_OF_YEAR, dayOfYear);
        return calendar.getTime();
    }

    public static int dateToJulian(Date date) {
        Calendar calendar = Calendar.getInstance();
        calendar.setTime(date);
        int year = calendar.get(Calendar.YEAR) - 1900;
        int dayOfYear = calendar.get(Calendar.DAY_OF_YEAR);
        return year * 1000 + dayOfYear;
    }
}

```



## String的常用方法

```java
String s1 = "aaa"; 
String s2 = "bbb";

//比较
s1.equals(s2); //将此字符串与指定对象进行比较。
// equalsIgnoreCase 进行比较，忽略大小写。


//获取
String s3 = s1.concat(s2);//将指定的字符串连接到该字符串的末尾 : "aaabbb"
s1.charAt(0); //返回指定索引处的 char值 "a"
s3.indexOf("b");//获取子字符串第一次出现在该字符串内的索引，没有返回-1 :3
s3.substring(3);//从beginIndex开始截取字符串到字符串结尾 : "bbb" 含beginIndex
String result = str.substring(strStartIndex, strEndIndex);

s3.replace("a","A");// 将与参数1匹配的字符串使用参数2字符串替换： "AAAbbb"


//转换
String toUpperCsae()//大转小
String toLowerCsae()//小转大
String trim();//将字符串两端的多个空格去除

//分割
String s4 = "aa|bb|cc";
String[] strArray = s4.split("\\|"); //有些特殊符号需要用 反斜杠 \ 转义，在Java要用两个反斜杠 \\
for(int i = 0; i < strArray.length; i++){
   System.out.print(strArray[i]);
}


//String.join (java8)
List names=new ArrayList<String>();
names.add("1");
names.add("2");
names.add("3");

System.out.println(String.join("-", names));

String[] arrStr=new String[]{"a","b","c"};
System.out.println(String.join("-", arrStr));
/**
输出：
1-2-3
a-b-c
*/
```


## 字符串处理

### 某些正则表达式匹配

```java 
    /*
     * 正则表达式匹配
     * @param str 传入的字符串
     * @return 符合匹配返回true,否则返回false
     */

    //是否是整数 比如 5 0 20
    //这个方法里因为要包容6.0这种类似的情况 所以多加了一层 
    public static boolean isInteger(String str) {
        Pattern pattern = Pattern.compile("^[-\\+]?[\\d]*$");
        // 如果是 6.0 这种情况 也不会被这个表达式通过，所以要再加一层
        boolean result = pattern.matcher(str).matches();
        if(!result){
            Pattern pattern1 = Pattern.compile("^[0-9\\.]*$");
            return pattern1.matcher(str).matches();
        }else{
            return true;
        }
    }

    //只允许有数字+字母 和 /、, 这三种字符
    public static boolean checkClass(String str) {
        Pattern pattern = Pattern.compile("^[A-Za-z0-9\\/、,]*$");
        return pattern.matcher(str).matches();
    }

    public static boolean checkSection(String str) {
            Pattern pattern = Pattern.compile("^[0-9\\-~、]*$");
        return pattern.matcher(str).matches();
    }


```



## 类型处理(各种转和格式化)

### String(带小数点的) 转 int

```java
    public int changeString2int(String str){
        Float tmpstr = Float.valueOf(str);
        return tmpstr.intValue();
    }
```


### String 转 Long 

```java
    String a = "111111";
    Long b = Long.parseLong(a); //基本类型
    Long c = Long.valueOf(a); // 包装类型

```


### String 转 int 

```java
    String a = "111111";
    Long b = Integer.parseInt(a); //基本类型
    Long c = Integer.valueOf(a); // 包装类型

```



### Date 转 String 转 timestamp
```java

Date today = new Date();
//置成0点 getDayBeginDate
today = getDayBeginDate(today);

String tmpTodaystr = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(today);

Timestamp todayTime =  Timestamp.valueOf(tmpTodaystr);


//String转timestamp
new Timestamp(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").parse(tmpTodaystr).getTime())


```
## 数组与list

### 数组排序(快速排序--递归)

```java 
 /**
     *  快速排序
     * @param array 需要排序的数组
     * @param lowIndex 数组(排序)开始下标
     * @param highIndex 数组(排序)结束下标
     */
    private static void arrayRank(int[] array,int lowIndex,int highIndex) {
        int low=lowIndex;
        int high=highIndex;
        int temp;
        int tempindex;
        if(lowIndex<highIndex) {
            while (low<=high) {
                tempindex = (lowIndex + highIndex) / 2;
                temp = array[tempindex];
                while ((low<highIndex)&&(array[low]<temp)) {
                    ++low;

                }
                while ((high>lowIndex)&&(array[high]>temp)) {
                    --high;
                }
                if(low<=high) {
                    swop(array, low, high);
                    ++low;
                    --high;
                }
            }
            if(low<highIndex) {

                arrayRank(array, low, highIndex);
            }
            if(high>lowIndex) {
                arrayRank(array, lowIndex, high);
            }
        }

    }
    //交换数组元素
    private static void swop(int[] array ,int low,int high) {
        int temp = array[low];
        array[low] = array[high];
        array[high] = temp;
    }

    //使用
    public static void main(String[] args){

        int[] allnums = new int[]{0, 8, 11, 1, 6, 9, 30, 31}；
        arrayRank(allnums, 0, allnums.length-1);

        System.out.println(Arrays.toString(allnums));
    }

```


### List 转 array
```java

    //arraylist转 array
   String[] arrAdd1 = addList.toArray(new String[addList.size()]);

   //数组的复制
   //其他方法可参考(https://blog.csdn.net/jaycee110905/article/details/9179227)

    public static void main(String[] args){
        String[] nums1 = new String[]{"0","8","11"};
        String[] nums2 = new String[]{"1","6","9","30"};
        String[] both = concat(nums1, nums2);
        System.out.println(Arrays.toString(both));
    }
    static String[] concat(String[] a, String[] b) {
        String[] c= new String[a.length+b.length];
        System.arraycopy(a, 0, c, 0, a.length);
        System.arraycopy(b, 0, c, a.length, b.length);
        return c;
    }
    // System.out.println(Arrays.toString(allnums));

```

```java
    private static final List AAA_HEAD = Arrays.asList("AAA", "AAA", "AAA");
    private static final List BBB_HEAD = Arrays.asList("BBBB", "BBBB", "BBBB");

    public static String[] createReportHeader(String reportType, List<String> orgs) {
        switch (reportType) {
            case ReportType.AAA_HEAD:
                orgs.addAll(AAA_HEAD);
                break;
            case ReportType.BBB_HEAD:
                orgs.addAll(BBB_HEAD);
                break;
            default:
        }

        String[] array = new String[orgs.size()];
        orgs.toArray(array);
        return array;
    }
```

### array 转 list

[Java数组转List的三种方式对比](https://blog.csdn.net/qq_34626859/article/details/91355067)
[java数组转list](https://www.cnblogs.com/zheyangsan/p/6910476.html)

1. Arrays.asList

> 通过Arrays.asList(T… a)
> 将数组转换为List，转换后的list只能进行查改，不能进行增删，否则就会跑出异常。
> 
> asList方法返回的是数组的一个视图
> 视图意味着，对这个list的操作都会反映在原数组上，而且这个list是定长的，不支持add、
> remove等改变长度的方法。

```java
    String[] strArray = new String[0];
    List<String> list = Arrays.asList(strArray);
    // 添加一个元素
    list.add("mark");
    System.out.println(list);


    //运行结果 java.lang.UnsupportedOperationException
```

> 不能把基本数据类型转化为列表
> 仔细观察可以发现asList接受的参数是一个泛型的变长参数，而基本数据类型是无法泛型化的


```java 
    int[] intarray = {1, 2, 3, 4, 5};
    //List<Integer> list = Arrays.asList(intarray); 编译通不过
    List<int[]> list = Arrays.asList(intarray);
    System.out.println(list); 
    //output: [ [I@4554617c ]
```

> 这是因为把int类型的数组当参数了，所以转换后的列表就只包含一个int[]元素
> 解决方案：
> 要想把基本数据类型的数组转化为其包装类型的list，可以使用 **guava类库** 的工具方法
   
```java
int[] intArray = {1, 2, 3, 4};
List<Integer> list = Ints.asList(intArray);
```

2. 使用java.util.ArrayList的 **构造器**，将Arrays.asList(T... a)的结果作为参数传递进去(支持增删改查)

> 在List的数据量不大的情况下，可以使用


```java

    String[] strArray = new String[2];
    List<String> list = new ArrayList<>(Arrays.asList(strArray));
    list.add("mark");
    list.add("hello");
    list.removeAll(Collections.singleton(null)); //去除List中为null的元素
    System.out.println(list);

    //output: [mark,hello]
```

3.通过集合工具类Collections.addAll()方法

> 这是最高效的一种方式，通过Collections.addAll(arrayList, strArray)方式转换，根据数
> 组的长度创建一个长度相同的List，然后通过Collections.addAll()方法，将数组中的元素转
> 为二进制，然后添加到List中。

```java

        String[] strArray = new String[1];
        List< String> list = new ArrayList<String>(strArray.length);
        Collections.addAll(list,strArray);
        list.add("mark");
        System.out.println(list);

```

