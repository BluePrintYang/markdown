#### String和StringBuffer的区别

String:

1. 是对象不是原始类型。
2. 为==不可变对象==,一旦被创建,就不能修改它的值。
3. 对于已经存在的String对象的修改都是重新创建一个新的对象,然后把新的值保存进去。
4. String是final类,即不能被继承。



StringBuffer:

1. 是一个==可变对象==,当对它进行修改的时候不会像String那样重新建立对象。

2. 它只能通过构造函数来建立，StringBuffer subffer=new StringBuffer();

3. 对象被建立以后,在内存中就会分配内存空间,并初始保存一个null,通过它的append方法向其赋值 subffer.append(“hello word”);

   

   > 字符串连接操作中StringBuffer的效率要明显比String高;
   > String对象是不可变对象,每次操作String都会建立新的对象来保存新的值。
   > StringBuffer对象实例化后,只对这一个对象操作。







### 常用类和方法

#### 一 Object类

##### 1.1 toString()

> 主要用途：重写这个方法可以自定义对象输出格式（默认格式 ：类名 + @ +哈希码值）
>

##### 1.2 equals()

> 主要用途 : 判断两个对象是否相同，默认的是比较两个对象的内存地址，一般重写equals方法的时候也会重写hasCode方法
>

##### 1.3 hasCode()

> 主要用途 : 获取类的哈希码值，哈希码值相当于是类的内存地址
>

#### 二 String类和StringBuffer

##### 2.1 String类

###### 2.1.1 字节数组构建字符串

```java
String(byte[] bytes)
```

###### 2.1.2 字符数组构建字符串

```java
String(char[] value)
```

###### 2.1.3 获取的方法

```java
int length() //获取字符串长度
char charAt(int index) //获取特定位置的字符
int indexOf(String str) //获取字符的位置
```

###### 2.1.4 判断方法

```java
boolean endsWith(String str) //是否以指定的字符结尾
boolean isEmpty() //是否为空
boolean contains(Char Sequences) //是否包含指定的内容
boolean equalsIgnoreCase(String anotherString)//忽略大小写是否相等
```

###### 2.1.5 转换方法

```java
char[] toCharArray()  //将字符串转换为字符数组
byte[] getBytes()  //将字符串转换为字节数组
```

###### 2.1.6 其他方法

```java
String[] split(String regex) //分割字符串的方法
String substring(int beginIndex) //截取字符串的方法
String toUpperCase() //转大写
String trim() //去除字符串首尾空格
```

##### 2.2 StringBuffer 与 StringBuilder

> StringBuffer和StringBuilder主要用于对字符串频繁增删改查的情况

###### 2.2.1 添加的方法

```java
append() //添加字符
insert(int offset,b) //插入索引值，插入的内容
```

###### 2.2.2 删除的方法

```java
delete(int start, int end) //根据指定的开始与结束的索引值删除对应的内容
deleteCharAt(int index) //根据指定的索引删除一个字符
```

###### 2.2.3 修改

```java
replace(int start, int end , String str) //把指定索引值的字符替换为指定的字符
reverse() // 反转字符串
```

###### 2.2.4 查看

```java
 indexOf(String str, int fromIdnex) // 查找指定的字符串第一次出现的索引值，并且指定开始查找的位置
```

###### 2.2.5 StringBuffer 与 StringBuilder(常用) 的相同与不同

相同：

> 1.两个类都是字符串缓冲类
> 2.两个类的方法都一样

不同:

> 1.StringBuffer是线程安全的，操作效率低，StringBuilder是线程非安全的,操作效率高
> 2.StringBuffer是jdk1.0出现的, StringBuilder 是jdk1.5的时候出现的.

#### 三 System 和 RunTime类

##### 3.1 System类

###### 3.1.1 System常用方法

```java
currentTimeMillis() //获取当前系统时间
exit(int status) //退出jvm 
gc() //建议jvm启动垃圾回收机制
getProperties();//获取系统的所有属性
```

##### 3.2 RunTime类

###### 3.2.1 RunTime常用方法

```java
getRuntime(); // 返回当前应用程序的运行环境对象
exec(String command) //根据指定的路径执行对应的可执行文件
freeMemory() //获取jvm的空闲内存,以字节为单位
totalMemory() //返回jvm的内存总量
```

#### 四 日期类

##### 4.1 Calendar类

```java
Calendar calendar = getInstance(); //获取当前的系统时间类
calendar.get(Calendar.YEAR)//获取年份
calendar.get(Calendar.MONTH)+1 //获取月份
calendar.get(Calendar.HOUR_OF_DAY） //获取小时,24小时制
```

##### 4.2 SimpleDateFormat类(常用)

###### 4.2.1 把日期转换成指定格式的字符串

```java
Date date = new Date();//获取当前的系统时间
SimpleDateFormat simpleFormat = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");//设置格式化模式
String time = simpleFormat.format(date);
```

###### 4.2.2 把字符串转换为对应的日期

```java
SimpleDateFormat simpleFormat = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
String birthday = "2000年12月26日 11:29:08";
Date date = simpleFormat.parse(birthday);//注意:指定的字符串格式必须要与SimpleDateFormat模式一至
```

#### 五 Math类

##### 5.1常用的方法

```java
abs(double a) //获取绝对值 Math.abs(-3)
ceil(double a) //向上取整
floor(double a) //向下取整
round(float a) //四舍五入
```

##### 5.2 随机数类(Random)

```java
Random random = new Random();
int num = random.nextInt(10); //产生0-10之间的随机数
```



#### 六 Scanner类

重点讲一下next()和nextLine()的区别

##### next()

只读取输入直到空格。它不能读两个由空格或符号隔开的单词。此外，next()在读取输入后将光标放在同一行中。(next()只读空格之前的数据,并且光标指向本行)

##### nextLine()

读取输入，包括单词之间的空格和除回车以外的所有符号(即。它读到行尾)。读取输入后，nextLine()将光标定位在下一行。

演示1

```Java
public class Text {
	public static void main(String []args) {
		Scanner input = new Scanner(System.in);
		System.out.println("请输入一个字符串(中间能加空格或符号)");
		String a = input.nextLine();
		System.out.println("请输入一个字符串(中间不能加空格或符号)");
		String b = input.next();
		System.out.println("请输入一个整数");
		int c;
		c = input.nextInt();
		System.out.println("请输入一个double类型的小数");
		double d = input.nextDouble();
		System.out.println("请输入一个float类型的小数");
		float f = input.nextFloat();
		System.out.println("按顺序输出abcdf的值：");
		System.out.println(a);
		System.out.println(b);
		System.out.println(c);
		System.out.println(d);
		System.out.println(f);
	}
}
```

结果

```
请输入一个字符串(中间能加空格或符号)
我爱祖国！
请输入一个字符串(中间不能加空格或符号)
ILoveChina
请输入一个整数
520
请输入一个double类型的小数
12.26e3
请输入一个float类型的小数
3.1415926
按顺序输出abcdf的值：
我爱祖国！
ILoveChina
520
12260.0
3.1415925
```



演示2

```Java
  public static void main(String[] args) {
            Scanner scan = new Scanner(System.in);	//构造Scanner类的对象scan，接收从控制台输入的信息
            System.out.println("请输入你的姓名");
            String name = scan.nextLine();//接收一个字符串，可以加除Enter以外的所有符号，包括空格和Tab
            System.out.println("请输入你的ID");
            String ID ;
            while(scan.hasNextLine()) {// hasNextLine()方法判断当前是否有输入，当键盘有输入后执行循环
                if(scan.hasNextInt()) {// 判断输入的值是否为整数类型，当为整数类型时执行循环
                    ID = String.valueOf(scan.nextInt());
                    System.out.println("你输入的姓名为："+name);
                    System.out.println("你输入的ID为："+ID);
                    break;
                }else {
                    System.out.println("请输入数字哦！");
                    ID = String.valueOf(scan.nextInt());
                }
            }
        }
```

结果

```
请输入你的姓名
西地那非
请输入你的ID
q764323221
请输入数字哦！
764323221
```

