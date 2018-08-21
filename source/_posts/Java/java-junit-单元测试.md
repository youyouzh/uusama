---
title: Java JUnit 单元测试
tags:
  - Java
url: 434.html
id: 434
categories:
  - 程序开发语言
  - Java
date: 2017-11-02 17:34:42
---

一个优秀的开发，不能等到程序交到测试人员手上才发现代码的问题。需要学会自己书写单元测试，保证自己代码的质量。

## 添加单元测试

单元测试需要引入 JUnit 相关的包，一般来说，使用IDE添加JUnit单元测试的时候，会自动添加。Maven的单元测试配置如下：
```xml
<dependency>
   <groupId>junit</groupId>
   <artifactId>junit</artifactId>
   <version>${junit.version}</version>
   <scope>test</scope>
</dependency>
```

### Eclipse平台

如果项目没有 JUnit 依赖，则需要添加，右键项目选择： Properties -> Java Build Path Libaries -> Add Library -> Junit ->Junit 4，添加JUnit依赖，可参考下面的操作示意图，注意红框部分。 [![Eclipse添加JUnit单元测试依赖](http://uusama.com/wp-content/uploads/2017/11/2017110208362189.png)](http://uusama.com/wp-content/uploads/2017/11/2017110208362189.png) 添加依赖之后，选择需要进行单元测试的类，右键选择： New -> Junit Test Case，在弹出来的单元测试向导选择 New JUnit 4 test，这儿要和Maven配置版本相同。一般选择4版本。 注意选择 Source 项，一般放在单独的测试 test 文件夹下，和 src 对应。 注意选择的 Superclass 项，选择适当的父类。下一步，选择需要进行测试的方法。 点击完成以后，就在 test 目录下生产相应的测试类了。一般为原来的类+Test结尾。

## Intellij IDEA平台

IDEA需要进入需要测试的类的源代码里面，把鼠标放在类名上，右键选择： goto -> test ->Create New Test，即可弹出测试类创建引导框： [![IDEA添加Jun体测试](http://uusama.com/wp-content/uploads/2017/11/2017110208464330.png)](http://uusama.com/wp-content/uploads/2017/11/2017110208464330.png) 在弹出的测试引导款中填入相应的项，以及选择需要进行测试的方法，点击OK即可生成测试类，IDEA会自动把生成的测试类放到 test 目录下面。 相比于Eclipse，IDEA要特别注意标记文件夹的属性，要不然后面会出各种各样的问题。标记方法如下： 选择菜单：File -> Project Structure -> Modules，在最右边的文件导向中选择相应的文件和标记，Project Structure配置在IDEA中非常重要，无论是新建一个项目还是导入一个项目，首要的事情一定是标记不同的目录的属性，告诉IDEA该怎么处理这些文件。 [![IDEA项目文件属性配置](http://uusama.com/wp-content/uploads/2017/11/2017110208515212.png)](http://uusama.com/wp-content/uploads/2017/11/2017110208515212.png)

## SpringMVC JUnit单元测试

下面针对SpringMVC类型项目单元测试进行讲解。 对SpringMVC进行单元测试时，需要执行SpringMVC启动过程，加载相关的配置文件，一般我会使用一个公有父类来做这些事情：

```java
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;

@RunWith(SpringJUnit4ClassRunner.class)
//@WebAppConfiguration(value = "src/main/webapp")
@ContextConfiguration(locations = {
        "file:src/main/webapp/WEB-INF/mvc-dispatcher-servlet.xml",
        "file:src/main/webapp/WEB-INF/spring/*.xml"})
public class BaseJunitTest{
}
```

这个父类没有任何的测试示例，只是加载了公有的配置，注意到注解 @ContextConfiguration 加载的配置，这里根据你在项目中配置文件的位置，选择使用 **file: ， classpath， classpath*，**如果你发现出现各种问题，无法启动或者加载配置，请使用 file 。比如下面的方式，如果你进行配置的位置很奇葩，那么下面的加载项会找不到位置。

```config
classpath:applicationContext.xml
# 如果不能找到配置文件的话，使用下面地址代替，file：后面使用你配置中 servlet.xml 的真实位置

file:src/main/webapp/WEB-INF/mvc-dispatcher-servlet.xml
```

### 对Service层和Dao层测试

对Service层和Dao层的测试，只需要在测试类中使用 Service 和 Dao 中依赖注入的资源即可测试它们的方法：

```java
public class UserServiceTest extends BaseJunitTest{
    @Resource  // 直接注入 Service 或 Dao 即可访问方法进行测试
    private UserService userService

    @Test  // 标记这是一个测试方法
    public void getData() throws Exception {
        User user = userService.getUserById(1);
        System.out.println("user");
    }
}
```

## 对Controller层的测试

一般来说，好的系统架构 Controller 层只会进行入口参数处理，返回值封装等简单的操作，可以直接模拟请求等方式测试。 对于Controller返回页面的情况，一般测试返回状态码，cookie等信息。当然也可以使用单元测试：使用MockMvc模拟通过url的接口调用。下面是一个简单例子：
```java
public class UserControllerTest extends BaseJunitTest{
    @Autowired
    private UserController userController;  // 注入控制器实例

    private MockMvc mockMvc;

    @Before
    public void setup(){  // 初始化URL对象
        mockMvc = MockMvcBuilders.standaloneSetup(userController).build();
    }

    @Test
    public void getTest() throws Exception {
        ResultActions resultActions = this.mockMvc.perform(MockMvcRequestBuilders.post("/show_user3").param("id", "1"));// 使用MockMvc模拟请求
        MvcResult mvcResult = resultActions.andReturn();
        String result = mvcResult.getResponse().getContentAsString();
        // 可以从response里面取状态码，header,cookies...
        // System.out.println(mvcResult.getResponse().getStatus());
    }
}
```

## 单元测试规则

单元测试应该是全自动执行的，并且非交互式的。测试框架通常是定期执行的，执行过程必须完全自动化才有意义。输出结果需要人工检查的测试不是一个好的单元测试。单元测试中不准使用 System.out 来进行人肉验证，必须使用 assert 来验证。单元测试尽量做到AIR原则和BCDE原则：

- A：Automatic，单元测试需要测试框架自动化执行，才有意义。
- I：Independent，单元测试用例之间互相独立，不存在依赖关系。
- R：Repeatable，单元测试可以被重复执行，且结果一致。
- B： Border ，边界值测试，包括循环边界、特殊取值、特殊时间点、数据顺序等。
- C： Correct ，正确的输入，并得到预期的结果。
- D： Design ，与设计文档相结合，来编写单元测试。
- E： Error ，强制错误信息输入（如：非法数据、异常流程、非业务允许输入等） ，并得到预期的结果。

## 单元测试注解和断言

JUnit提供了一些针对测试行为的注解，使用这些注解，可以染测试更加灵活。

| 注解| 说明|
| :-- | :-- |
| @Before | 初始化方法  |
| @After | 释放资源  |
| @Test | 测试方法，在这里可以测试期望异常（expected 参数）和超时时间（timeout 参数）  |
| @Ignore | 忽略的测试方法  |
| @BeforeClass | 针对所有测试，只执行一次，且必须为static void  |
| @AfterClass | 针对所有测试，只执行一次，且必须为static void  |
| @RunWith | 指定测试类使用某个运行器  |
| @Parameters | 指定测试类的测试数据集合  |
| @Rule | 允许灵活添加或重新定义测试类中的每个测试方法的行为  |
| @FixMethodOrder | 指定测试方法的执行顺序  |

另外是用于自动化测试的一些断言，应该尽量避免人工查看输出的方式进行单元测试。

| 断言方法名| 功能|
| :-- | :-- |
| assertArrayEquals(expecteds, actuals) | 查看两个数组是否相等。  |
| assertEquals(expected, actual) | 查看两个对象是否相等。类似于字符串比较使用的equals()方法  |
| assertNotEquals(first, second) | 查看两个对象是否不相等。  |
| assertNull(object) | 查看对象是否为空。  |
| assertNotNull(object) | 查看对象是否不为空。  |
| assertSame(expected, actual) | 查看两个对象的引用是否相等。类似于使用“==”比较两个对象  |
| assertNotSame(unexpected, actual) | 查看两个对象的引用是否不相等。类似于使用“!=”比较两个对象  |
| assertTrue(condition) | 查看运行结果是否为true。  |
| assertFalse(condition) | 查看运行结果是否为false。  |
| assertThat(actual, matcher) | 查看实际值是否满足指定的条件  |
| fail() | 让测试失败  |