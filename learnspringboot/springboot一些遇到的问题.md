### springboot启动白页(Whitelable Error Page)的问题

使用SpringBoot写HelloWorld，当配置好启动类后，再创建新的controller或其它类，启动项目后访问对应的映射名，页面显示：

**Whitelabel Error Page**

**This application has no explicit mapping for /error, so you are seeing this as a fallback.**

映射未找到，出现这个异常说明了跳转页面的url无对应的值.

**解决：**

排查过程：

　　1、先确定URL是否正确（正确无误）；

　　2、由于之前自己写过，并且成功访问，但是这次没有成功，所以进行比较，发现启动类的位置不同，上网找了找资料，发现SpringBoot默认扫描的范围为本包及子包：

　　　　进入@SpringBootApplication的源码：

![img](https://ask.qcloudimg.com/http-save/yehe-2161968/sqeschzq6u.png?imageView2/2/w/1620)

　　　　而**@ComponentScan的默认扫描范围为本包及子包**；

所以只需将访问的Controller类放到启动类的同级或子级的路径下，就可以了；

或者在启动类上指定@ComponentScan的范围就OK了！



### 配置mybatis

#### The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone

解决方法是在mysql的url参数后面加上参数：“**?serverTimezone=UTC**”

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/springboot?characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: 991568
    driver-class-name: com.mysql.cj.jdbc.Driver
```

但是之后数据库时间差了八小时

将UTC改成Asia/Shanghai

```
url: jdbc:mysql://localhost:3306/springboot?characterEncoding=utf-8&serverTimezone=Asia/Shanghai
```

**UTC代表全球标准时间，我国时间要领先8小时**





### 配置通用Mapper(代码生成器)

#### At least one base package must be specified

没有扫描到mapper包

方式一： 在Mapper接口上添加`@Mapper`注解

方式二： 在`Application.java`启动类上添加`@MapperScan()`注解扫描所有Mapper接口。注意这里的`@MapperScan`注解来自`tk.mybatis.spring.annotation.MapperScan`，用spring的会报错。

不知道为什么，方法一我没成功

方法二

```java
@MapperScan("top.yll.mapper")
```





#### 解决跨域问题

添加@CrossOrigin注解



#### 加载yml失败

删除带所有带中文的注释