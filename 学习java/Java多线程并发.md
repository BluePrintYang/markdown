## Java多线程并发

### Java线程实现/创建方法

#### 继承Thread类

Thread类本质是实现了Runnable接口的一个实例，代表一个线程的实例。启动一个线程的唯一方法是通过Thread类的start()方法。<u>start方法是一个native方法</u>（一个Native Method就是一个java调用非java代码的接口）

```Java
public class MyThread extends Thread {
    public void run(){
        System.out.println("MyThread.run()");
    }
}
MyThread myThread1 = new MyThread();
myThread1.start();
```

#### 实现Runnable接口

如果自己的类已经extends另一个类，就无法直接extends Thread，此时可以实现一个Runnable接口

```Java
public class MyThread2 extends OtherClass implements Runnable {
    public void run(){
        System.out.println("MyThread2.run()");
    }

    public static void main(String[] args) {
        /*
        启动MyThread2，需要首先实例化一个Thread，并传入自己的MyThread实例
         */
        MyThread2 myThread2 = new MyThread2();
        Thread thread = new Thread(myThread2);
        thread.start();
        /*
        事实上，当传入一个Runnable target参数给Thread后，Thread的run()方法就会调用target.run()
         */

    }
}
```

#### ExecutorService、Callable< Class >、Future有返回值线程

有返回值的任务必须实现Callable接口，无返回值的任务必须实现Runnable接口。执行Callable任务后，可以获取一个Future的对象，在该对象上调用get就可以获取到Callable任务返回的Object了，再结合线程池接口ExecutorService就可以实现有返回结果的多线程了

```Java
public class Pool {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        int taskSize = 3;
        //创建一个线程池
        ExecutorService pool = Executors.newFixedThreadPool(taskSize);
        //创建多个有返回值的任务
        List<Future> list = new ArrayList<Future>();
        for (int i = 0; i < taskSize; i++){
            Callable c = new MyCallable(i+" ");
            //执行任务并获取Future对象
            Future f = pool.submit(c);
            list.add(f);
        }
        //关闭线程池
        pool.shutdown();
        //获取所有并发任务的运行结果
        for (Future f : list){
            //从Future对象上获取任务的返回值，并输出到控制台
            System.out.println("res: "+f.get().toString());
        }
    }
}

public class MyCallable implements Callable {

    private String oid;

    public MyCallable(String oid) {
        this.oid = oid;
    }

    @Override
    public Object call() throws Exception {
        return oid+"任务返回的内容";
    }
}
```

#### 基于线程池的方式

```Java
//创建线程池
ExecutorService threadPool = Executors.newFixedThreadPool(10);
while (true){
	threadPool.execute(new Runnable() {
		@Override
		public void run() {
			System.out.println(Thread.currentThread().getName()+" is running .. ");
	        try{
				Thread.sleep(3000);
		    } catch (InterruptedException e) {
        	e.printStackTrace();
	        }
		}
    });
}
```

### 4种线程池

Java里面线程池的顶级接口是Executor，但是严格意义上讲Executor并不是一个线程池，而是执行线程的工具。真正的线程池接口是ExecutorService

#### newCachedThreadPool

调用executor将重用以前构造的线程（如果线程可用）。如果线程中没有可用的，则创建一个新线程并添加到池中。终止从缓存中移除那些已有60秒钟未被使用的线程

#### newFixedThreadPool

创建一个可重用固定线程数的线程池，以共享的无界队列方式来运行这些线程

#### newScheduledThreadPool

创建一个线程池，它可在给定延迟后运行命令或者定期地执行

```Java
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(3);
       scheduledThreadPool.schedule(new Runnable() {
            @Override
            public void run() {
                System.out.println("延时3秒");
            }
        },3, TimeUnit.SECONDS);
        scheduledThreadPool.scheduleAtFixedRate(new Runnable() {
            @Override
            public void run() {
                System.out.println("延迟1秒后每三秒执行1次");
            }
        },1,3,TimeUnit.SECONDS);
```

#### newSingleThreadExecutor

Executors.newSingleThreadExecutor()返回要给线程池，这个线程池只有线程。这个线程池可以在线程死后（或发生异常）重启一个线程来替代原来的线程继续执行下去



### 线程生命周期（状态）

#### 新建状态

使用new关键词创建一个线程后，处于新建状态，仅由JVM为其分配内存，并初始化其成员变量

#### 就绪状态

线程对象调用了start()方法之后。Java虚拟机会为其创建方法调用栈和程序计数器，等待调度运行

#### 运行状态

就绪状态的线程获得了cpu，开始执行run()方法的线程执行体

#### 阻塞状态

由于某种原因放弃了cpu使用权

分三种情况

- 等待阻塞：执行wait()方法
- 同步阻塞：获取对象同步锁时，同步锁被其他线程占用，JVM会把该线程放入锁池中
- 其他阻塞：执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求

#### 线程死亡

线程会以三种方式结束，结束后就是死亡状态

- 正常结束：run()或call()方法执行完成
- 异常结束：线程抛出一个未捕获的Exception或Error
- 调用stop：直接调用线程的stop()方法来结束改线程——容易造成死锁，不推荐



### sleep与wait区别

1. sleep()方法属于Thread类；wait()方法属于Object类
2. sleep()方法让出指定时间的cpu给其他线程，到时间之后自动恢复运行
3. 调用sleep()方法的过程中，线程不会释放对象锁
4. 调用wait()方法时，线程会放弃对象锁，进入等待此对象的等待锁定池，需要调用针对此对象的notify()方法后才会进入运行状态



### start与run区别

1. start()方法用来启动线程，真正实现了多线程运行。无需等待run()方法执行完毕，可以直接继续执行下面的代码
2. 调用Thread类的start()方法来启动一个线程，此线程进入<u>就绪状态</u>
3. 方法run()称为线程体，包含要执行的这个线程的内容，线程就进入了<u>运行状态</u>，开始运行run函数当中的代码



### Java锁

#### 乐观锁

认为读多写少。

Java中基本都是通过CAS操作实现，CAS是一种更新的原子操作，比较当前值跟传入值是否一样，一样就更新，否则失效

#### 悲观锁

认为写多

Java中的悲观锁是Synchronized，AQS框架下的锁则是先尝试cas乐观锁去获取锁，获取不到才会转换为悲观锁

#### 自旋锁





### volatile关键词

变量可见性



禁止重排序





### 在两个线程之间共享数据

#### 将数据抽象成一个类，并将数据的操作作为这个类的方法

要在方法上加synchronized

```Java
public class MyData {
    private int j = 0;
    public synchronized void add(){
        j++;
        System.out.println("线程"+Thread.currentThread().getName()+"j为："+j);
    }
    public synchronized void dec(){
        j--;
        System.out.println("线程"+Thread.currentThread().getName()+"j为："+j);
    }
    public int getData(){
        return j;
    }
}

public class AddRunnable implements Runnable{

    MyData data;
    public AddRunnable(MyData data){
        this.data=data;
    }
    @Override
    public void run() {
        data.add();
    }
}

public class DecRunnable implements Runnable {

    MyData data;
    public DecRunnable(MyData data){
        this.data=data;
    }

    @Override
    public void run() {
        data.dec();
    }
}

public static void main(String[] args) {
	MyData data = new MyData();
    Runnable add = new AddRunnable(data);
    Runnable dec = new DecRunnable(data);
    for (int i = 0; i < 2; i++){
    	new Thread(add).start();
	    new Thread(dec).start();
    }
}
```

#### Runnable对象作为一个类的内部类

```Java
public class MyData {
    private int j = 0;

    public synchronized void add() {
        j++;
        System.out.println("线程" + Thread.currentThread().getName() + "j为：" + j);
    }

    public synchronized void dec() {
        j--;
        System.out.println("线程" + Thread.currentThread().getName() + "j为：" + j);
    }

    public int getData() {
        return j;
    }

}

class TestThread {
    public static void main(String[] args) {
        final MyData data = new MyData();
        for (int i = 0; i < 2; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    data.add();
                }
            }).start();
            new Thread(new Runnable() {
                @Override
                public void run() {
                    data.dec();
                }
            }).start();
        }
    }
}
```

