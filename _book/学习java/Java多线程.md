### 线程的生命周期

#### 新建状态

使用new关键词创建一个线程后，处于新建状态，仅由JVM为其分配内存，并初始化其成员变量

#### 就绪状态

线程对象调用了start()方法之后。Java虚拟机会为其创建方法调用栈和程序计数器，等待调度运行

#### 运行状态

就绪状态的线程获得了cpu，开始执行run()方法的线程执行体

#### 阻塞状态

由于某种原因放弃了cpu使用权

分三种情况

- 等待阻塞：执行wait()方法，进入等待队列
- 同步阻塞：获取对象同步锁时，同步锁被其他线程占用，JVM会把该线程放入锁池中
- 其他阻塞：执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求

#### 线程死亡

线程会以三种方式结束，结束后就是死亡状态

- 正常结束：run()或call()方法执行完成
- 异常结束：线程抛出一个未捕获的Exception或Error
- 调用stop：直接调用线程的stop()方法来结束改线程——容易造成死锁，不推荐



![image-20200420150209648](https://www.yangllong.top/PicHub/image-20200420150209648.png)





### 线程创建方式

```Java
public class ThreadTest {
    public static void main(String[] args) {

        //1.继承Thread类
        Thread1 thread = new Thread1();
        thread.start();

        //2.实现Runnable接口
        Runnable r = () -> {
            for (int i = 0; i < 10; i++) {
                System.out.println("子线程2的run方法" + i);
            }
        };

        //实例化线程
        Thread t2 = new Thread(r);
        t2.start();

        System.out.println("主线程启动了");
    }
}

class Thread1 extends Thread {
    //重写run方法
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("子线程1的run方法" + i);
        }
    }
}
```

### 线程常用方法

#### 线程的优先级

```java
private static void threadPriority() {

        //只是修改线程抢到cpu时间片的概率
        //并不一定优先级高就能抢到
        //优先级是个整数 [1,10] 默认5 ，最高10
        Runnable r = () -> {
            for (int i = 0; i < 100; i++) {
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        };

        Thread t1 = new Thread(r, "Thread1");
        Thread t2 = new Thread(r, "Thread2");


        //设置优先级,必须在start方法之前设置
        t1.setPriority(10);
        t2.setPriority(1);

        t1.start();
        t2.start();
    }
```

#### 线程的礼让

```Java
/**
* 线程的礼让
* 放弃cpu时间片，然后重新抢夺，也可能还是抢到
*/
private static void threadYield(){
        Runnable r = () ->{
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName()+":"+i);
                if (i==3)Thread.yield();
            }
        };

        Thread t1 = new Thread(r,"Thread1");
        Thread t2 = new Thread(r,"Thread2");

        t1.start();
        t2.start();
    }
```



### 多线程并发

#### 多线程卖票程序

```Java
public class SourceConflict {
    public static void main(String[] args) {

        Runnable r = () ->{
            while (TicketCenter.restTicket>0){
                System.out.println(Thread.currentThread().getName()+"卖出一张票，剩余"+--TicketCenter.restTicket+"张票");
            }
        };

        Thread t1 = new Thread(r,"Thread1");
        Thread t2 = new Thread(r,"Thread2");
        Thread t3 = new Thread(r,"Thread3");
        Thread t4 = new Thread(r,"Thread4");

        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }
}

class TicketCenter{
    public static int restTicket=100;
}
//运行结果 每次执行结果不同
//Thread4卖出一张票，剩余98张票
//Thread1卖出一张票，剩余97张票
//Thread3卖出一张票，剩余99张票
//Thread2卖出一张票，剩余99张票
//Thread3卖出一张票，剩余94张票
//Thread3卖出一张票，剩余92张票
//Thread1卖出一张票，剩余95张票
//Thread4卖出一张票，剩余96张票
//Thread4卖出一张票，剩余89张票
//……
```

结果并不是我们想要得到的，是由于多线程并发时执行顺序导致出现的问题

#### 改进方法1 使用同步代码段

```Java
public class SynchronizeDemo {
    public static void main(String[] args) {

        Runnable r = () ->{
                while (TicketCenter.restTicket>0){
                    //同步代码段
                    //synchronized小括号里面可以是对象也可以是类
                    //但是必须保证所以线程看到的锁是同一个
                    synchronized (""){
                        if (TicketCenter.restTicket<=0)return;
                        System.out.println(Thread.currentThread().getName()+"卖出一张票，剩余"+--TicketCenter.restTicket+"张票");
                }
            }
        };

        Thread t1 = new Thread(r,"Thread1");
        Thread t2 = new Thread(r,"Thread2");
        Thread t3 = new Thread(r,"Thread3");
        Thread t4 = new Thread(r,"Thread4");

        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }
}
//运行结果
//Thread1卖出一张票，剩余99张票
//Thread1卖出一张票，剩余98张票
//Thread2卖出一张票，剩余97张票
//Thread2卖出一张票，剩余96张票
//……
//Thread2卖出一张票，剩余2张票
//Thread2卖出一张票，剩余1张票
//Thread2卖出一张票，剩余0张票    
```

#### 改进方法2 使用同步方法

```Java
public class SynchronizeFunction {
    public static void main(String[] args) {

        Runnable r = SynchronizeFunction::sellTicket;

        Thread t1 = new Thread(r,"Thread1");
        Thread t2 = new Thread(r,"Thread2");
        Thread t3 = new Thread(r,"Thread3");
        Thread t4 = new Thread(r,"Thread4");

        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }

    /**
     * 方法为静态，则同步锁为类.class
     * 非静态，同步锁为this
     */
    private synchronized static void sellTicket(){
        while (TicketCenter.restTicket>0){
            System.out.println(Thread.currentThread().getName()+"卖出一张票，剩余"+--TicketCenter.restTicket+"张票");
        }
    }
}
//运行结果同上
```

#### 改进方法3 使用显式锁

```Java
public class LockDemo {
    public static void main(String[] args) {

        //实例化一个锁对象
        ReentrantLock lock = new ReentrantLock();

        Runnable r = () -> {
            while (TicketCenter.restTicket > 0) {
                lock.lock();
                if (TicketCenter.restTicket <= 0) return;
                System.out.println(Thread.currentThread().getName() + "卖出一张票，剩余" + --TicketCenter.restTicket + "张票");
                lock.unlock();
            }
        };

        Thread t1 = new Thread(r, "Thread1");
        Thread t2 = new Thread(r, "Thread2");
        Thread t3 = new Thread(r, "Thread3");
        Thread t4 = new Thread(r, "Thread4");

        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }
}
//运行结果同上
```



### 死锁

```Java
//多个线程彼此持有对方需要的锁标记而不释放自己的锁造成的
public class DeadLock {
    public static void main(String[] args) {

        Runnable r1 = ()->{
            synchronized ("A"){
                System.out.println("线程1获取了锁A，等待锁B");
                synchronized ("B"){
                    System.out.println("线程1同时获取A和B");
                }
            }
        };

        Runnable r2 = ()->{
            synchronized ("B"){
                System.out.println("线程2获取了锁B，等待锁A");
                synchronized ("A"){
                    System.out.println("线程2同时获取A和B");
                }
            }
        };

        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);

        t1.start();
        t2.start();
    }
}
```

#### 解除死锁

```Java
/*
wait():等待，Object类中的一个方法，当前线程释放自己的锁标记，让出CPU资源，进入等待队列
notify()：通知，Object类中的一个方法，唤醒等待队列中的一个线程（由CPU调度），使这个线程进入锁池
notifyAll()：通知，Object类中的方法，唤醒等待队列中所有线程，并使这些线程进入锁池
*/
public class DeadLock2 {
    public static void main(String[] args) {

        Runnable r1 = ()->{
            synchronized ("A"){
                try {
                    "A".wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("线程1获取了锁A，等待锁B");
                synchronized ("B"){
                    System.out.println("线程1同时获取A和B");
                }
            }
        };

        Runnable r2 = ()->{
            synchronized ("B"){
                System.out.println("线程2获取了锁B，等待锁A");
                synchronized ("A"){
                    System.out.println("线程2同时获取A和B");
                    "A".notifyAll();
                }
            }
        };

        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);

        t1.start();
        t2.start();
    }
}
```

### 单例模式

```Java
public class SingletonTest {
    public static void main(String[] args) {

        Runnable r = Boss::getBoss;

        for (int i = 0; i < 100; i++) {
            new Thread(r).start();
        }

    }
}

class Boss {
    private Boss() {
        System.out.println("1个Boss对象被实例化了");
    }

    private static Boss instance = null;

    /*
    public static Boss getBoss(){
        synchronized (""){
            if (instance==null){
                instance = new Boss();
            }
        }
        return instance;
    }
*/
    public static synchronized Boss getBoss() {
        if (instance == null) instance=new Boss();
        return instance;
    }
}
//运行结果
//1个Boss对象被实例化了
```

