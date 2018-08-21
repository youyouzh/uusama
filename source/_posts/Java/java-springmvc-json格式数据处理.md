---
title: Java JSON格式数据处理-fastjson
tags:
  - Java
  - SpringMVC
url: 422.html
id: 422
categories:
  - 程序开发语言
  - Java
date: 2017-11-01 15:03:47
---

Java 中有很多非常成熟的 JSON 库，常用的有 fastjson，Jackson，Gson，我一般使用阿里的 fastjson，json 解析非常快速和简单。下面针对 fastjson 简单介绍。

## fastjson库引入

fastjson 在Maven中的配置如下：

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>fastjson</artifactId>
   <version>1.2.7</version>
</dependency>
```

如果没有使用Maven，可以去下面的地址下载： 

- github: [https://github.com/alibaba/fastjson](https://github.com/alibaba/fastjson) 
- SVN: [http://code.alibabatech.com/svn/fastjson/trunk/](http://code.alibabatech.com/svn/fastjson/trunk/) 
- 选择相应的版本下载jar包：[http://code.alibabatech.com/mvn/releases/com/alibaba/fastjson/](http://code.alibabatech.com/mvn/releases/com/alibaba/fastjson/) 
- WIKI: [http://code.alibabatech.com/wiki/display/FastJSON/Home](http://code.alibabatech.com/wiki/display/FastJSON/Home)

## fastjson 使用方法

fastjson中导入 JSON 类或者 JSONObject 类，JSONObject 继承自 JSON，它们都提供了比较完善的 JSON 解析方法。 

JSON API 可以参考这儿：[https://www.w3cschool.cn/fastjson/fastjson-api.html](https://www.w3cschool.cn/fastjson/fastjson-api.html) 

fastjson 中常用的有两个方法： toJSONString 和 parseObject，更多详细的API可以参考[fastjson源码](https://github.com/alibaba/fastjson/blob/master/src/main/java/com/alibaba/fastjson/JSON.java)

### 序列化API

序列化方法将 POJO 对象，Map，LIst等转化为 标准的 JSON 格式字符串。
```java 
package com.alibaba.fastjson;

public abstract class JSON {
    // 将Java对象序列化为JSON字符串，支持各种各种Java基本类型和JavaBean
    public static String toJSONString(Object object, SerializerFeature... features);

    // 将Java对象序列化为JSON字符串，返回JSON字符串的utf-8 bytes
    public static byte\[\] toJSONBytes(Object object, SerializerFeature... features);

    // 将Java对象序列化为JSON字符串，写入到Writer中, features为自定义序列化选项
    public static void writeJSONString(Writer writer, Object object, SerializerFeature... features);

    // 将Java对象序列化为JSON字符串，按UTF-8编码写入到OutputStream中
    public static final int writeJSONString(OutputStream os, Object object, SerializerFeature... features);
}
```

### 反序列化API

反序列化方法将标准的 JSON 格式字符串转化为 JSONObject 或者指定的原对象。
```java 
package com.alibaba.fastjson;

public abstract class JSON {
    // 将JSON字符串反序列化为JavaBean
    public static <T> T parseObject(String jsonStr, Class<T> clazz, Feature... features);
    public static final Object parse(String text);
    public static final Object parse(String text, int features);

    // 将UTF-8格式的JSON字符串反序列化为JavaBean
    public static <T> T parseObject(byte\[\] jsonBytes, Class<T> clazz, Feature... features);

    // 将JSON字符串反序列化为泛型类型的JavaBean
    public static <T> T parseObject(String text, TypeReference<T> type, Feature... features);

    // 将JSON字符串反序列为JSONObject
    public static JSONObject parseObject(String text);
    public static final JSONObject parseObject(String text, Feature... features);
}

JSONObject类提供了一系列访问JSON对象特定键值的方法，内部实现是一个 Map<String, Object> map; 该类提供了各种 getType 函数，用于不同的类型，如 getFloat(String key);

public class JSONObject extends JSON {
    public boolean isEmpty();   // 判断是否为空
    public boolean containsKey(Object key);  // 判断是否包含某个key
    public JSONObject getJSONObject(String key);
    public JSONArray getJSONArray(String key);
    public Object get(Object key);
    public  T getObject(String key, Class clazz);
}
```

### 使用方法

下面的例子简单说明如何使用JSON的两个方法将 JSON对象序列化和反序列化。
```java 
import com.alibaba.fastjson.JSON;
// 对象转JSON字符串
Model model = new Model; 
String jsonStr = JSON.toJSONString(model);
byte\[\] jsonBytes = JSON.toJSONBytes(model);
Writer writer = new Writer();
JSON.writeJSONString(writer, model);

// JSON字符串解析
JSONObject jsonObj = JSON.parseObject(jsonStr);
Model model = JSON.parseObject(jsonStr, Model.class);
Type type = new TypeReference<List>() {}.getType(); 
List list = JSON.parseObject(jsonStr, type);
```
### 自定义序列化

在序列化或者反序列化的过程中，我们往往需要对 POJO 类的一些字段进行控制，对特定字段的序列化和反序列化行为进行控制。 fastjson支持多种方式定制序列化。

- 通过@JSONField定制序列化
- 通过@JSONType定制序列化
- 通过调用时传入参数 SerializerFeature 定制序列化
- 通过SerializeFilter定制序列化
- 通过ParseProcess定制反序列化
- 通过调用时传入参数 Feature 定制反序列化

**@JSONField配置**

- 若属性是私有的，必须有set*方法。否则无法反序列化。
- FieldInfo可以配置在getter/setter方法或者字段上。

可用的  @JSONField 参数选项如下：

```java 
package com.alibaba.fastjson.annotation;

public @interface JSONField {
   // 配置序列化和反序列化的顺序，1.1.42版本之后才支持
   int ordinal() default 0;

   // 指定字段的名称
   String name() default "";

   // 指定字段的格式，对日期格式有用，如 yyyyMMdd
   String format() default "";

   // 指定字段是否序列化
   boolean serialize() default true;

   // 指定字段是否反序列化
   boolean deserialize() default true;
}
```

**和JSONField类似，但JSONType配置在类上，而不是field或者getter/setter方法上。** **通过SerializeFilter可以使用扩展编程的方式实现定制序列化。fastjson提供了多种SerializeFilter：**

*   PropertyPreFilter 根据PropertyName判断是否序列化
*   PropertyFilter 根据PropertyName和PropertyValue来判断是否序列化
*   NameFilter 序列化时修改Key，如果需要修改Key,process返回值则可
*   ValueFilter 序列化时修改Value
*   BeforeFilter 序列化时在最前添加内容
*   AfterFilter 序列化时在最后添加内容

以上的SerializeFilter在JSON.toJSONString中可以使用。 **SerializerFeature 可能取值**
```java 
package com.alibaba.fastjson.serializer;

public enum SerializerFeature {
    QuoteFieldNames,  // 字段名使用双引号
    UseSingleQuotes,  // 使用单引号代替双引号
    WriteMapNullValue,  // 是否输出值为null的字段,默认为false 
    WriteEnumUsingToString,
    WriteEnumUsingName,
    UseISO8601DateFormat,
    WriteNullListAsEmpty,  // List字段如果为null,输出为\[\],而非null 
    WriteNullStringAsEmpty, // 字符串类型字段如果为null,输出为”“,而非null 
    WriteNullNumberAsZero,  // 数值字段如果为null,输出为0,而非null
    WriteNullBooleanAsFalse, // 布尔字段如果为null,输出为false,而非null
    SkipTransientField,
    SortField,  // 对字段进行排序
}
```
**Feature 可能取值：**
```java 
public enum Feature {
    AutoCloseSource,
    AllowComment,
    AllowUnQuotedFieldNames,
    AllowSingleQuotes,
    InternFieldNames,
    AllowISO8601DateFormat,
    AllowArbitraryCommas,
    UseBigDecimal,
    IgnoreNotMatch,
    SortFeidFastMatch,
    DisableASM,
    DisableCircularReferenceDetect,
    InitStringFieldAsEmpty,
    SupportArrayToBean,
    OrderedField,
    DisableSpecialKeyDetect;
}
```