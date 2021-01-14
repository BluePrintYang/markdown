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

![img](..\img\1620)

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



### 解决跨域问题

添加@CrossOrigin注解



### 加载yml失败

删除带所有带中文的注释





### controller调用service报错空指针异常

我的错误原因： controller层中的service没有注入

解决方法：加注解（@Resource @Autowired） 

其他原因

调用controller报空指针有两种情况： 

1.  service 或者mapper 没有加载到spring容器中 ，引用时候肯定报空指针

2.  service加载到spring容器中了， 但是controller中定义的方法为private，私有方法被接口访问时候，因为private作用域的问题，无法获取该方法，报空指针

3. service层没有使用@Service修饰(接口不需要加，在impl中加)   



### 部署相关



```shell
jps #查看Java进程
kill -9 <pid> #杀死进程
taskkill /pid <pid> #Windows命令行
nohup java -jar xxx.jar #退出命令行后仍然运行
```



### 数据库第三次实验

#### 前端传日期失败

input中使用text不用datetimelocal

#### 日期时区不对导致操作数据库失败

```Java
date.setHours(date.getHours()-14);//获取日期时更改相差的时间
```

```Java
//另
Date date1 = new Date(2020-1900, Calendar.JULY,10,10,11,11);//如果不-2020，年份为3920
Date date2 = new Date(2020-1900, Calendar.JULY,10,10,11,11);
System.out.println(date1==date2);//false
System.out.println(date1.equals(date2));//true
```

#### 通过Session和th:if隐藏一些内容

```Java
@RequestMapping("/login")
public String login(User user, Model model, HttpSession session) {
    if (checkUser(user)) {
        session.setAttribute("loginUser", user.getUsername());
        session.setAttribute("userType", user.getUserType());
        return "redirect:/main.html";
    } else {
        model.addAttribute("msg", "用户名或密码错误");
        model.addAttribute("type", user.getUserType());
        return "login";
    }
}
```

```html
<th th:if="${session.userType==1}">操作</th>
<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
```

#### Thymeleaf传两个参数，Controller接收

```html
<a class="btn btn-sm btn-primary" onclick="return confirm('确定要修改吗？')"
  th:href="@{/toUpdatePage/{flight_id}/{date}/(flight_id=${flight.getFlight_id()},date=${flight.getDate()})}">编辑</a>
<a class="btn btn-sm btn-danger" onclick="return confirm('确定要删除吗？')"
  th:href="@{/deleteFlight/{flight_id}/{date}/(flight_id=${flight.getFlight_id()},date=${flight.getDate()})}">删除</a>
```

```Java
@GetMapping("/toUpdatePage/{flight_id}/{date}")
public String toUpdatePage(@PathVariable("flight_id") String flight_id, @PathVariable("date") Date date) {
}
@GetMapping("/deleteFlight/{flight_id}/{date}")
public String deleteFlight(@PathVariable("flight_id") String flight_id, @PathVariable("date") Date date ){
}
```

