## Java基础

### Java异常

#### 异常分类

Throwable是Java语言中所有错误或异常的超类。下一层为Error和Exception

Error类是指Java运行时系统的内部错误和资源耗尽错误。

Exception有两个分支运行时异常RuntimeException和检查异常CheckException



#### 异常处理方式

1. throw
2. throws
3. 系统自动抛异常



#### Throw与Throws的区别

位置不同

1. throw用在函数上，后面跟异常类，可以跟多个；throws用在函数内，后面跟的是异常对象

功能不同

1. throws用来声明异常，让调用者只知道该功能可能出现的问题；throw抛出具体的问题对象，执行到throw，功能就结束了
2. throws表示出现异常的一种可能性，并不一定会发生异常；throw则是抛出了异常，执行throw则一定抛出了某种异常对象



### Java反射

Java反射机制是指在运行状态中，对于任意一个类都能知道这个类所有的属性和方法；并且对于任意一个对象，都能够调用它的任意一个方法

**运行状态时知道类所有的属性和方法**

编译时类型和运行时类型

```Java
Person p = new Student();//编译时为Person类，运行时为Student类
```

#### 反射API

1. Class类
2. Field类
3. Method类
4. Constructor类

#### 反射使用步骤（获取Class对象，调用对象方法）

1. 获取想要操作的类的Class对象
2. 调用Class类中的方法
3. 使用反射API来操作这些信息



#### 获取Class对象的3种方法

- 调用某个对象getClass()方法

```java
Person p = new Person();
Class aClass = p.getClass();
```

- 调用某个类的class属性来获取该类的Class对象

```java 
Class aClass = Person.class;
```

- 使用Class类中的forName()静态方法（最安全/性能最好）

```Java
Class aClass = Class.forName("类的全路径");//最常用
```

 获取了想要操作的类的Class对象后，可以通过Class类中的方法获取并查看该类中的方法和属性

```Java
public class MyReflection {
    public static void main(String[] args) throws ClassNotFoundException {
        //获取Test类的Class对象
        Class aClass = Class.forName("interview.Test");
        //获取Test类的所有方法信息
        Method[] methods = aClass.getDeclaredMethods();
        for (Method m : methods){
            System.out.println(m.toString());
        }

        //获取Test类的所有成员属性信息
        Field[] fields = aClass.getDeclaredFields();
        for (Field f : fields){
            System.out.println(f.toString());
        }

        //获取Test类的所有构造方法信息
        Constructor[] constructors = aClass.getDeclaredConstructors();
        for (Constructor c : constructors){
            System.out.println(c.toString());
        }
    }

}
```

#### 创建对象的两种方法

Class对象的newInstance()

- 使用Class对象的newInstance()方法来创建改Class对象对应的实例，但是这种方法要求该Class对象对应的类**有默认的空构造器**

调用Constructor对象的newInstance()

- 先使用Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance()方法来创建Class对象对应类的实例

```Java
//获取Test类的Class对象
Class aClass = Class.forName("interview.Test");
//使用.newInstance方法创建对象
Test t = (Test) aClass.newInstance();
//获取构造器方法并创建对象
Constructor c = aClass.getDeclaredConstructor(int.class);
//创建对象并设置属性
Test t1 = (Test) c.newInstance(10);
```





### Java注解

是Java提供的一种对元程序中元素关联信息和元数据的途径和方法

Annotation是一个接口，程序可以通过反射来获取指定程序中的元素的Annotation对象，然后通过该Annotation对象来获取注解中的元数据信息

#### 4种标准元注解

元注解的作用是负责注解其他注解

##### @Target修饰对象的赋值范围

可被用于packages，types，类型成员，方法参数和本地变量

##### @Retention定义被保留的时长

定义注解被保留的时间长短

- SOURCE：在源文件中有效
- CLASS：在class文件中有效
- RUNTIME：在运行时有效

##### @Document描述-javadoc

##### @Inherited阐述了某个被标注的类型是被继承的

#### 注解处理器

```Java
/*定义注解*/
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface FruitProvider {
    /*供应商编号*/
    public int id() default -1;

    /*供应商名称*/
    public String name() default "";

    /*供应商地址*/
    public String address() default "";
}

/*注解使用*/
public class Apple {
    @FruitProvider(id=1,name = "江苏红富士集团",address = "江苏南京")
    private String appleProvider;

    public String getAppleProvider() {
        return appleProvider;
    }

    public void setAppleProvider(String appleProvider) {
        this.appleProvider = appleProvider;
    }
}

/*注解处理器*/
public class FruitInfoUtil {
    public static void getFruitInfo(Class<?> aClass) {
        String strFruitProvider = "供应商信息";
        Field[] fields = aClass.getDeclaredFields();//通过反射获取处理注解
        for (Field field : fields) {
            if (field.isAnnotationPresent(FruitProvider.class)) {
                FruitProvider fruitProvider = (FruitProvider) field.getAnnotation(FruitProvider.class);
                //注解信息的处理地方
                strFruitProvider = " 供应商编号：" + fruitProvider.id() + " 供应商名称:"+fruitProvider.name()+" 供应商地址："+fruitProvider.address();
                System.out.println(strFruitProvider);
            }
        }
    }
}

public class FruitRun {
    public static void main(String[] args) {
        FruitInfoUtil.getFruitInfo(Apple.class);
    }
}
```





### Java内部类

这是一个普通的Java类，有3个成员变量

```Java
public class NestedClass {
    private static int nestedClassPrivateStaticVar;
    public int nestedClassPublicVar;
    protected int nestedClassProtectedVar;
}
```

#### 静态成员类

在里面添加一个静态类StaticMemberClass，StaticMemberClass就是静态成员类

```Java
public class NestedClass {
    
    private static int nestedClassPrivateStaticVar;
    public int nestedClassPublicVar;
    protected int nestedClassProtectedVar;
    
    public static void main(String[] args) {
        //通过类名调用静态内部类的静态方法
        StaticMemberClass.getNestedClassPrivateStaticVar();
        //通过类名访问静态内部类的静态成员
        System.out.println("外部类通过类名访问静态内部类的静态成员："+StaticMemberClass.staticPrivateValue);
    }
    
    /**
     * 1.静态成员类StaticMemberClass
     * ★★★属于整个类，不属于单个实例对象
     */
    static class StaticMemberClass {

        //外部类可通过类名访问
        private static int staticPrivateValue;
        //外部类无法访问
        public int staticValue;

        static void getNestedClassPrivateStaticVar() {
            //可访问外部类的静态成员变量
            nestedClassPrivateStaticVar = 0;
            System.out.println("通过静态内部类访问外部类的静态成员变量：" + nestedClassPrivateStaticVar);
        }
    }
}
//运行结果
//通过静态内部类访问外部类的静态成员变量：0
//外部类通过类名访问静态内部类的静态成员：0
```

#### 非静态成员类



#### 局部类

#### 匿名内部类



函数式接口：只包含一个抽象方法的接口，可以包含多个默认方法、类方法。

例如：Runnable，ActionListener





### Lambda表达式

- Lambda表达式的目标类型必须是函数式接口
- Lambda表达式只能为函数式接口创建对象。Lambda表达式只能实现一个方法

Lambda表达式的结果就是被当成对象

里面的外部变量必须被final修饰

