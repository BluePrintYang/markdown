> 突然发现一个很有意思的现象，学的东西越多，就会发现以前的内容很浅显，甚至是错的。当时明明觉得豁然开朗了，现在看来其实还是蒙在鼓中。这种感觉之前也有过，上初中后说小学学的知识是错误的，上高中后说初中是错的……真的是错的吗？或许是的。但是，如果一开始就讲最正确最新的理论知识，恐怕要“劝退”很多人。这其实也是一个不断进步，不断改正错误的过程吧。哪有什么真正的对与错，我们现在认为正确的事，未来就一定也是正确的吗？学无止境，就是这个道理吧！

言归正传，继续学习Java并发相关知识

## AQS源码

### 概述

首先来看看AQS（AbstractQueuedSynchronizer）的类图

![](https://gitee.com/yonglone/PicHub/raw/master/20200625112946.png)

AQS是一个抽象类，继承了抽象类AbstractOwnableSynchronizer，实现了序列化接口

有两个内部类Node和ConditionObject

### 抽象类AOS

```Java
public abstract class AbstractOwnableSynchronizer
    implements java.io.Serializable {

    //序列化id
    private static final long serialVersionUID = 3737899427754241961L;

    //空参构造方法
    protected AbstractOwnableSynchronizer() { }

    //独占模式同步的当前所有者
    private transient Thread exclusiveOwnerThread;

    // 设置当前拥有独占访问权的线程
    protected final void setExclusiveOwnerThread(Thread thread) {
        exclusiveOwnerThread = thread;
    }

    //返回当前拥有独占访问权的线程
    protected final Thread getExclusiveOwnerThread() {
        return exclusiveOwnerThread;
    }
}
```

### AQS源码

#### Fileds和一些简单Methods

```Java
public abstract class AbstractQueuedSynchronizer
    extends AbstractOwnableSynchronizer
    implements java.io.Serializable {

    private static final long serialVersionUID = 7373984972572414691L;

    /* 空参构造方法。
    * 官方描述： Creates a new  instance with initial synchronization state of zero.
    */
    protected AbstractQueuedSynchronizer() { }
    
    /*内部类Node在下面*/
    
    //等待队列头节点，只能通过setHead方法修改
    //如果head存在，waitStatus不会是CANCELED
    private transient volatile Node head;
    
    //等待队列尾结点，只能通过enq方法修改，添加新的等待结点
    private transient volatile Node tail;
    
    //同步器状态
    private volatile int state;
    
    /*省略state的setter&getter*/
    
    //CAS
     protected final boolean compareAndSetState(int expect, int update) {
        // 调用unsafe的CAS
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }
    
    //自旋的纳秒阈值
    static final long spinForTimeoutThreshold = 1000L;
    
    /*省略一些方法，下面详解*/
    
    //支持CAS操作
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long stateOffset;
    private static final long headOffset;
    private static final long tailOffset;
    private static final long waitStatusOffset;
    private static final long nextOffset;
    
    static {
        try {
            stateOffset = unsafe.objectFieldOffset
                (AbstractQueuedSynchronizer.class.getDeclaredField("state"));
            headOffset = unsafe.objectFieldOffset
                (AbstractQueuedSynchronizer.class.getDeclaredField("head"));
            tailOffset = unsafe.objectFieldOffset
                (AbstractQueuedSynchronizer.class.getDeclaredField("tail"));
            waitStatusOffset = unsafe.objectFieldOffset
                (Node.class.getDeclaredField("waitStatus"));
            nextOffset = unsafe.objectFieldOffset
                (Node.class.getDeclaredField("next"));

        } catch (Exception ex) { throw new Error(ex); }
    }
}
```

#### 内部类Node

```Java
static final class Node {
    //指示节点正在共享模式下等待的标记
    static final Node SHARED = new Node();
    //指示节点正在以独占模式等待的标记
    static final Node EXCLUSIVE = null;

    //waitStatus值，指示线程已取消
    static final int CANCELLED =  1;
    //waitStatus值，指示后续线程需要释放
    static final int SIGNAL    = -1;
    //waitStatus值，指示线程正在等待条件
    static final int CONDITION = -2;
    //waitStatus值，指示下一次获取的Shared应该无条件传播
    static final int PROPAGATE = -3;

    volatile int waitStatus;
   
    volatile Node prev;

    volatile Node next;

    volatile Thread thread;

    Node nextWaiter;

    final boolean isShared() {
        return nextWaiter == SHARED;
    }

    final Node predecessor() throws NullPointerException {
        Node p = prev;
        if (p == null)
            throw new NullPointerException();
        else
            return p;
    }

    Node() {    // 用于建立初始标头或SHARED标记
    }

    Node(Thread thread, Node mode) {     // Used by addWaiter
        this.nextWaiter = mode;
        this.thread = thread;
    }

    Node(Thread thread, int waitStatus) { // Used by Condition
        this.waitStatus = waitStatus;
        this.thread = thread;
    }
}
```



#### 排队工具方法Queuing utilities

##### enq方法

> 插入node到队列

```Java
private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) { // Must initialize
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

##### addwaiter方法

> 为当前线程和给定模式创建并排队节点

```Java
private Node addWaiter(Node mode) {
    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    enq(node);
    return node;
}
```

##### setHead方法

> 设置头结点

```Java
private void setHead(Node node) {
    head = node;
    node.thread = null;
    node.prev = null;
}
```

##### unparkSuccessor方法

```Java
//Wakes up node's successor, if one exists.
private void unparkSuccessor(Node node) {
   
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);

    Node s = node.next;
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    if (s != null)
        LockSupport.unpark(s.thread);
}
```

##### doReleaseShared方法

```Java
private void doReleaseShared() {

    for (;;) {
        Node h = head;
        if (h != null && h != tail) {
            int ws = h.waitStatus;
            if (ws == Node.SIGNAL) {
                if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                    continue;            // loop to recheck cases
                unparkSuccessor(h);
            }
            else if (ws == 0 && !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                continue;                // loop on failed CAS
        }
        if (h == head)                   // loop if head changed
            break;
    }
}
```

##### setHeadAndPropagate方法

```Java
private void setHeadAndPropagate(Node node, int propagate) {
    Node h = head; // Record old head for check below
    setHead(node);
    if (propagate > 0 || h == null || h.waitStatus < 0 ||
        (h = head) == null || h.waitStatus < 0) {
        Node s = node.next;
        if (s == null || s.isShared())
            doReleaseShared();
    }
}
```

#### :star:主要方法Main exported methods

```Java
//Attempts to acquire in exclusive mode.
protected boolean tryAcquire(int arg) {
    throw new UnsupportedOperationException();
}

//Attempts to set the state to reflect a release in exclusive mode.
protected boolean tryRelease(int arg) {
    throw new UnsupportedOperationException();
}

//Attempts to acquire in shared mode
protected int tryAcquireShared(int arg) {
    throw new UnsupportedOperationException();
}

//Attempts to set the state to reflect a release in shared mode
protected boolean tryReleaseShared(int arg) {
    throw new UnsupportedOperationException();
}

//Returns true if synchronization is held exclusively with respect to the current (calling) thread. 
protected boolean isHeldExclusively() {
    throw new UnsupportedOperationException();
}

```

> AQS提供的模板方法

```Java
//独占式获取同步状态。如果当前线程获取同步状态成功，则由该方法返回，否则，将会进入同步队列等待，该方法将会调用重写的tryAcquire方法
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
        selfInterrupt();
}

//与acquire方法相同，但是该方法响应中断，当前线程未获取到同步状态而进入同步队列中，如果当前线程被中断，则抛出异常
public final void acquireInterruptibly(int arg)
    throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    if (!tryAcquire(arg))
        doAcquireInterruptibly(arg);
}

//在acquireInterruptibly方法的基础上加了超时限制，超时时间内没有获取到同步状态返回false，获取到了返回true
public final boolean tryAcquireNanos(int arg, long nanosTimeout)
    throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    return tryAcquire(arg) ||
        doAcquireNanos(arg, nanosTimeout);
}

//独占式的释放同步状态，该方法会在释放同步状态之后，将同步队列中第一个节点包含的线程唤醒
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}

//共享式的获取同步状态。如果当前线程未获取到同步状态，将会进入同步队列等待。与独占式获取的主要区别是在同一时刻可以有多个线程获取到同步状态
public final void acquireShared(int arg) {
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}

//与acquireShared方法相同，该方法响应中断
public final void acquireSharedInterruptibly(int arg)
    throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    if (tryAcquireShared(arg) < 0)
        doAcquireSharedInterruptibly(arg);
}

//在acquireSharedInterruptibly方法基础上加了超时限制
public final boolean tryAcquireSharedNanos(int arg, long nanosTimeout)
    throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    return tryAcquireShared(arg) >= 0 ||
        doAcquireSharedNanos(arg, nanosTimeout);
}

//共享式的释放同步状态
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}

//获取等待在同步队列上的线程集合
public final Collection<Thread> getQueuedThreads() {
    ArrayList<Thread> list = new ArrayList<Thread>();
    for (Node p = tail; p != null; p = p.prev) {
        Thread t = p.thread;
        if (t != null)
            list.add(t);
    }
    return list;
}
```



#### CAS方法

```Java
// CAS head field. Used only by enq.
private final boolean compareAndSetHead(Node update) {
    return unsafe.compareAndSwapObject(this, headOffset, null, update);
}

// CAS tail field. Used only by enq.
private final boolean compareAndSetTail(Node expect, Node update) {
    return unsafe.compareAndSwapObject(this, tailOffset, expect, update);
}

// CAS waitStatus field of a node.
private static final boolean compareAndSetWaitStatus(Node node,int expect,int update) {
    return unsafe.compareAndSwapInt(node, waitStatusOffset,expect, update);
}

// CAS next field of a node.
private static final boolean compareAndSetNext(Node node,Node expect,Node update) {
    return unsafe.compareAndSwapObject(node, nextOffset, expect, update);
}
```



