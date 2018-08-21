---
title: Java一些基本操作
tags:
  - Java
url: 440.html
id: 440
categories:
  - 程序开发语言
  - Java
date: 2017-11-07 11:26:24
---

## 常量定义：Map，List

定义Map，List的常量，在定义的时候初始化值，如果只是使用 final 关键字，仍然可以对集合进行修改，需要加 unmodified 修饰，如下：

```java
// 定义常量Map
public static final Map<String, String> CONST_MAP = Collections.unmodifiableMap(new HashMap<String, String>() {
    private static final long serialVersionUID = 1L;
    {
        put("AAA", "111");
        put("BBB", "222");
        put("CCC", "333");
    }
});
// 定义常量List
public static final List<String> CONST_LIST = Collections.unmodifiableList(new ArrayList<String>() {
    private static final long serialVersionUID = 1L;
    {
        add("aaa");
        add("bbb");
        add("ccc");
    }
});
// 定义常量List的另一方法
public static final List<String> CONST\_LIST\_2 = new ArrayList<>(Arrays.asList("aaa", "bbb", "ccc"));
```

## Java集合遍历

集合遍历一般推荐使用迭代器遍历，其实 foreach 遍历的底层实现也是使用迭代器。

### Map遍历

Map的遍历方式主要是通过获取相应的集合使用foreach遍历，或者使用迭代器遍历，如果需要对元素删除，需要使用迭代器：

- 使用Map的Key集合遍历：map.keySet()
- 使用Map的Value集合遍历（只能遍历值）：map.values()
- 使用Map的Entry集合遍历（推荐）：map.entrySet()
- 使用Map的Entry迭代器遍历

```java
Map<String, String> map = new HashMap<>();
map.put("key\_aaa", "value\_bbb");
map.put("key\_bbb", "value\_bbb");

// 使用Key集合遍历
for (String key : map.keySet()) {
    String value = map.get(key);
    System.out.println("key: " + key + " value: " + value);
}
// Value集合遍历
for (String value : map.values()) {
    System.out.println("value: " + value);
}

// Entry集合遍历（推荐）
for (Map.Entry<String, String> entry: map.entrySet()) {
    String key = entry.getKey();
    String value = entry.getValue();
    System.out.println("key: " + key + " value: " + value);
}

// 使用Entry迭代器遍历
Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
while(iterator.hasNext()) {
    Map.Entry<String, String> entry = iterator.next();
    String key = entry.getKey();
    String value = entry.getValue();
    System.out.println("key: " + key + " value: " + value);
}
```

### List遍历

List方式可以使用for结合 size() 函数遍历，也可以使用 foreach 遍历，但是如果遍历的过程中，有对列表中元素的删除操作，则只能使用迭代器遍历：

- for 循序遍历，结合list.size() 函数
- 使用 foreach 遍历
- 使用迭代器遍历
```java
List<String> list = new ArrayList<>();
list.add("aaa");
list.add("bbb");
list.add("ccc");
// 使用for结合size()遍历
for (int i = 0; i < list.size(); i++) {
    String element = list.get(i);
    System.out.println("Element: " + element);
}
// 使用foreach遍历
for (String element : list) {
    System.out.println("Element: " + element);
}
// 使用迭代器遍历
Iterator iterator = list.iterator();
while(iterator.hasNext()){
    int i = (Integer) iterator.next();
    String element = list.get(i);
    System.out.println("Element: " + element);
}
```

## Java String与基本类型之间转换

Java的8种基本类型都提供了相应的包装类，这些保证类提供了相应的静态方法用来处理类型转换。基本类型和包装类对应关系如下：

- cahr  <->  Character
- byte  <->  Byte
- short  <->  Short
- int  <->  Integer
- long  <->  Long
- float  <->  Float
- double  <->  Double
- bool  <->  Boolean

调用这些包装类的 toString() 方法，可以将基本类型转换为 String。
```java
int numberInt = 45;
String numberString = Integer.toString(numberInt);
```

另外还可以使用 String.valueOf()，或者将基本类型加上空字符串实现。 **基本类型转String的方法：**

- 调用包装类的 toString() 方法：Integer.toString(number)
- 调用String的valueOf()方法：String.valueOf(number)
- 使用隐式类型转换：amount + ""；// 创建两个String对象

包装类中还有相应的 parseInt，pasrseDouble 等方法可以将String转为基本类型：

- 使用包装类的 parseInt 等方法：Integer.parseInt()，Double.parseDouble
- 使用包装类的 valueOf 方法：Tnteger.valueOf(numberString).intValue()

包装类中，还提供了基本类型之间转换的方法：

- intValue()：将当期对象转换为 int；等价于强制类型转换：(int)value；
- floatValue()：将当期对象转换为 float；等价于强制类型转换：(float)value；
- doubleValue()：将当期对象转换为 double；等价于强制类型转换：(double)value；
- ……………

查看包装类的源码可以发现，这些基本类型转换的实现就是强制类型转换。

## Java字符串：String，StringBuffer，StringBuild

这三个类都可以用来表示字符串，实现细节是 char 数组，可以参考源码，下面简单说明

### String

- 定义一个字符串常量，只能赋值一次，不可再更改，常量会放在常量池中
- 指向唯一常量的应用，会在编译时优化
- 所有对它的对象的操作都会生成另外一个字符串常量
- intern方法会在运行时常量池中查找是否存在内容相同的字符串，如果存在则返回指向该字符串的引用，如果不存在则会将该字符串入池并返回一个指向该字符串的引用。
```java
String a = "sama",
        b = "sa" + "ma",
        c = "sa";
String d = c + "ma"; // c是引用，不再编译时优化，在堆上创建对象
final String e = "sa";
String f = e + "ma";
String g = d.intern();  // 返回常量池的应用
System.out.println(a == b); // true
System.out.println(a == d); // false
System.out.println(a == f); // true
System.out.println(a == g); // true
```
### StringBuffer

- 字符串变量（Synchronized，即线程安全）
- 主要操作是 append 和 insert 方法，可重载这些方法，以接受任意类型的数据
- 调用StringBuffer的toString()方法可转为String

### StringBuild

StringBuild 和 StringBuffer 类似，是字符串变量，不过非线程安全，在单线程下比 StringBuffer 快速。

### 使用策略

- 操作少量的数据，用String
- 单线程操作大量数据，用StringBuilder
- 多线程操作大量数据，用StringBuffer
- 不要使用String类的"+"来进行频繁的拼接，如在循环中避免使用String +

## Java时间：Date，Calendar

### Date

获取当前时间：Date date = new Date()，使用时间戳（ms）初始化：Date date = new Date(15098792983);

| 序号 | 方法和描述  |
| :-- | :--  |
| 1 | boolean after(Date date)
若当调用此方法的Date对象在指定日期之后返回true,否则返回false。  |
| 2 | boolean before(Date date)
若当调用此方法的Date对象在指定日期之前返回true,否则返回false。  |
| 3 | Object clone( )
返回此对象的副本。  |
| 4 | int compareTo(Date date)
比较当调用此方法的Date对象和指定日期。两者相等时候返回0。调用对象在指定日期之前则返回负数。调用对象在指定日期之后则返回正数。  |
| 5 | int compareTo(Object obj)
若obj是Date类型则操作等同于compareTo(Date) 。否则它抛出ClassCastException。  |
| 6 | boolean equals(Object date)
当调用此方法的Date对象和指定日期相等时候返回true,否则返回false。  |
| 7 | long getTime( )
返回自 1970 年 1 月 1 日 00:00:00 GMT 以来此 Date 对象表示的毫秒数。  |
| 8 | int hashCode( )
返回此对象的哈希码值。  |
| 9 | void setTime(long time)
用自1970年1月1日00:00:00 GMT以后time毫秒数设置时间和日期。  |
| 10 | String toString( )
转换Date对象为String表示形式，并返回该字符串。  |

### 使用 SimpleDateFormat 格式化日期

可以使用 format() 方法把 Date 格式化为相应格式的时间字符串 还可以使用 parse() 方法把时间字符串转化为 Date 对象，可能会抛出解析异常：ParseException
```java
Date date = new Date();
SimpleDateFormat formater = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
String strDate = formater.format(date);
try {
    date = formater.parse(strDate);
} catch (ParseException e) {
    e.printStackTrace();
}
```
SimpleDateFormat 参数为pattern，格式如下：

| 字母 | 描述 | 示例  |
| :-- | :-- | :--  |
| G | 纪元标记 | AD  |
| y | 四位年份 | 2001  |
| M | 月份 | July or 07  |
| d | 一个月的日期 | 10  |
| h | A.M./P.M. (1~12)格式小时 | 12  |
| H | 一天中的小时 (0~23) | 22  |
| m | 分钟数 | 30  |
| s | 秒数 | 55  |
| S | 毫秒数 | 234  |
| E | 星期几 | Tuesday  |
| D | 一年中的日子 | 360  |
| F | 一个月中第几周的周几 | 2 (second Wed. in July)  |
| w | 一年中第几周 | 40  |
| W | 一个月中第几周 | 1  |
| a | A.M./P.M. 标记 | PM  |
| k | 一天中的小时(1~24) | 24  |
| K | A.M./P.M. (0~11)格式小时 | 10  |
| z | 时区 | Eastern Standard Time  |
| ‘ | 文字定界符 | Delimiter  |
| “ | 单引号 | `  |

### Calendar类

Calendar类的功能要比Date类强大很多，而且在实现方式上也比Date类要复杂一些。 Calendar类是一个抽象类，在实际使用时实现特定的子类的对象，创建对象的过程对程序员来说是透明的，只需要使用getInstance方法创建即可。
```java
// 获取表示当前时间的Calendar对象
Calendar c = Calendar.getInstance();
// 设置特定的时间:2017-19-28 12:34:56
c.set(2017, 10,28, 12, 34, 56);
```

Calendar类中用以下这些常量表示不同的意义

| 常量 | 描述  |
| :--| :--|
| Calendar.YEAR | 年份  |
| Calendar.MONTH | 月份  |
| Calendar.DATE | 日期  |
| Calendar.DAY_OF_MONTH | 日期，和上面的字段意义完全相同  |
| Calendar.HOUR | 12小时制的小时  |
| Calendar.HOUR_OF_DAY | 24小时制的小时  |
| Calendar.MINUTE | 分钟  |
| Calendar.SECOND | 秒  |
| Calendar.DAY_OF_WEEK | 星期几  |

Calendar可以使用 set() 方法设置时间，还可以使用 add() 方法添加时间
```java
// 设置某个领域（年，月，日等）特定值
public void set(int field, int value)
// 某个领域（年，月，日等）添加一个值，职位负数表示减
public void add(int field, int amount);
// 获得某个领域的值
public int get(int field)
```
还可已使用 after() before() 等方法进行时间比较

### Date，String，Calendar之间的转换

Date和String之间的转换借助 SimpleDateFormat 的 format() 方法和 parse() 方法实现，Date和Calendar之间的转换Calendar的 getTime() 和 setTime() 方法可以实现，如下：
```java
Date date = new Date();
SimpleDateFormat dateFormat  = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
Calendar calendar = Calendar.getInstance();
String str = "";

// String -> Date
date = dateFormat.parse(str);

// Date -> String
str = dateFormat.format(date);

// Date -> Calendar
calendar.setTime(date);

// Calendar -> Date
date = calendar.getTime();

// String -> Calendar
str = dateFormat.format(calendar.getTime());

// Calendar -> String
calendar.setTime(dateFormat.parse(str));
```