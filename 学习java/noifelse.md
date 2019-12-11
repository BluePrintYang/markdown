一般来说我们正常的后台管理系统都有所谓的角色的概念

不同管理员权限不一样，能够行使的操作也不一样

比如：

- 系统管理员（ROLE_ROOT_ADMIN）：有A操作权限
- 订单管理员（ROLE_ORDER_ADMIN）：有B操作权限
- 普通用户（ROLE_NORMAL）：有C操作权限

比如一个用户进来，我们需要根据不同用户的角色来判断其有哪些行为，这时候**SAO代码**出现了：

### if/else写法

```java
public class JudgeRole {
    public String judge(String roleName) {
        String result = "";
        if (roleName.equals("ROLE_ROOT_ADMIN")) {
        // 系统管理员有A权限
            result = "ROLE_ROOT_ADMIN: " + "has AAA permission";
        } else if (roleName.equals("ROLE_ORDER_ADMIN")) {
        // 订单管理员有B权限
            result = "ROLE_ORDER_ADMIN: " + "has BBB permission";
        } else if (roleName.equals("ROLE_NORMAL")) {
        // 普通用户有C权限
            result = "ROLE_NORMAL: " + "has CCC permission";
        } else {
            result = "XXX";
        }
        return result;
    }
}
```

​		这样当系统里有**几十个角色**时，那几十个 if/else嵌套可以说是非常酸爽了…… 这样**一来**非常不优雅，别人阅读起来很费劲；**二来**则是以后如果再复杂一点，或者想要再加条件的话**不好扩展**；而且代码一改，以前的老功能肯定还得重测，岂不疯了……



### 使用枚举类

首先定义一个公用接口 RoleOperation，表示不同角色所能做的操作：

```java
public interface RoleOperation {
    String op();//表示某个角色可以做哪些操作
}
```

接下来我们将不同角色的情况全部交由枚举类来做，定义一个不同角色有不同权限的枚举类 RoleEnum：

```java
public enum RoleEnum implements RoleOperation {
    // 系统管理员(有A操作权限)
    ROLE_ROOT_ADMIN {
        @Override
        public String op() {
            return "ROLE_ROOT_ADMIN:" + " has AAA permission";
        }
    },

    // 订单管理员(有B操作权限)
    ROLE_ORDER_ADMIN {
        @Override
        public String op() {
            return "ROLE_ORDER_ADMIN:" + " has BBB permission";
        }
    },

    // 普通用户(有C操作权限)
    ROLE_NORMAL {
        @Override
        public String op() {
            return "ROLE_NORMAL:" + " has CCC permission";
        }
    };

}
```

接下来调用就变得异常简单了，一行代码就行了， if/else也灰飞烟灭了：

```java
public class JudgeRoleByEnum {
    public String judge(String roleName) {
    // 一行代码搞定！之前的if/else没了！
        return RoleEnum.valueOf(roleName).op();
    }
}
```

而且这样一来，以后假如我想扩充条件，只需要去枚举类中加代码即可，而不是去改以前的代码，这岂不很稳！

### 工厂模式

> 需要上面定义的接口

首先，针对不同的角色，单独定义其业务类：

```java
// 系统管理员(有A操作权限)
public class RootAdminRole implements RoleOperation {
    private String roleName;
    public RootAdminRole(String roleName ){
        this.roleName = roleName;
    }
    @Override
    public String op() {
        return roleName +" has AAA permission";
    }
}
```
```java
// 订单管理员(有B操作权限)
public class OrderAdminRole implements RoleOperation {
    private String roleName;
    public OrderAdminRole(String roleName ){
        this.roleName = roleName;
    }
    @Override
    public String op() {
        return roleName +" has BBB permission";
    }
}
```
```java
// 普通用户(有C操作权限)
public class NormalRole implements RoleOperation {
    private String roleName;
    public NormalRole(String roleName ){
        this.roleName = roleName;
    }
    @Override
    public String op() {
        return roleName +" has CCC permission";
    }
}
```

接下来再写一个**工厂类** **RoleFactory**对上面不同角色进行聚合：

```java
public class RoleFactory {
    static Map<String, RoleOperation> roleOperationMap = new HashMap<>();


    //在静态块中先把初始化工作全部做完
    static {
        roleOperationMap.put("ROLE_ROOT_ADMIN",new RootAdminRole("ROLE_ROOT_ADMIN"));
        roleOperationMap.put("ROLE_ORDER_ADMIN",new RootAdminRole("ROLE_ORDER_ADMIN"));
        roleOperationMap.put("ROLE_NORMAL",new RootAdminRole("ROLE_NORMAL"));
    }
    public static RoleOperation getOp(String roleName){
        return roleOperationMap.get(roleName);
    }
}
```

接下来借助上面这个工厂，业务代码调用也只需一行代码， if/else同样被消除了

```java
public class JudgeRoleByFactory {
    public String judge(String roleName){
        //1行代码搞定！之前的if/else也没了!
        return RoleFactory.getOp(roleName).op();
    }
}
```

这样的话以后想扩展条件也很容易，只需要增加新代码，而不需要动以前的业务代码，非常符合**“开闭原则”**。

### 策略模式

在上面工厂模式代码的基础上(需要接口)

按照策略模式的指导思想，我们也来创建一个所谓的**策略上下文类**，这里命名为 RoleContext：

```java
public class RoleContext {
    //可更换的策略，传入不同的策略对象，业务即相应变化
    private RoleOperation operation;


    public RoleContext(RoleOperation operation){
        this.operation=operation;
    }


    public String execute(){
        return operation.op();
    }
}
```

很明显上面传入的参数 operation就是表示不同的**“策略”**。我们在业务代码里传入不同的角色，即可得到不同的操作结果：

```java
public class JudgeRoleByContext {
    public String judge(RoleOperation roleOperation){
        RoleContext roleContext = new RoleContext(roleOperation);
        return roleContext.execute();
    }
}
```

```java
public static void main(String[] args) {
    JudgeRoleByContext judgeRoleByContext = new JudgeRoleByContext();
    String result1 = judgeRoleByContext.judge(new RootAdminRole("ROLE_ROOT_ADMIN"));
    System.out.println(result1);
    String result2 = judgeRoleByContext.judge(new OrderAdminRole("ROLE_ORDER_ADMIN"));
    System.out.println(result2);
    String result3 = judgeRoleByContext.judge(new NormalRole("ROLE_NORMAL"));
    System.out.println(result3);
}
```

