原文链接：https://www.jianshu.com/p/9e6e84f15b95

# 初识ReentrantLock

```java
public interface Lock {

    void lock();

    void lockInterruptibly() throws InterruptedException;

    boolean tryLock();

    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;

    void unlock();

    Condition newCondition();
}

public class ReentrantLock implements Lock, java.io.Serializable {
    ...
    public ReentrantLock() {
        sync = new NonfairSync();
    }

    public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }

    abstract static class Sync extends AbstractQueuedSynchronizer {
        ...
    }

    static final class NonfairSync extends Sync {
        ...
    }

    static final class FairSync extends Sync {
        ...
    }
}
```

ReentrantLock实现了Lock接口，并且有三个内部类。

- Sync继承了AbstractQueuedSynchronizer。
- NonfairSync继承自Sync实现非公平模式。
- FairSync继承自Sync实现了公平模式。

ReentrantLock的主要方法:

- lock  占有锁，如果当前线程无法占有锁则挂起
- lockInterruptibly  占有锁，除非当前线程已中断
- tryLock  尝试在当前锁空闲时占有锁，如果占有失败并不会挂起，而是返回false
- unlock 释放锁
- newCondition 由当前Lock创建一个Condition对象用于调用await、signal、signalAll等同步方法。

# AbstractQueuedSynchronizer

由于ReentrantLock的实现依赖于其内部类Sync，而Sync继承自AbstractQueuedSynchronizer，因此先分析这个类。

```java
public abstract class AbstractQueuedSynchronizer
    extends AbstractOwnableSynchronizer
    implements java.io.Serializable {

    private transient volatile Node head;

    private transient volatile Node tail;

    private volatile int state;

    static final class Node {

        static final Node SHARED = new Node();

        static final Node EXCLUSIVE = null;
		//该Node放弃竞争锁
        static final int CANCELLED =  1;
		//Node内部的线程将挂起（通过LockSupport的park方法）
        static final int SIGNAL    = -1;
		//Node在Condition中的队列等待
        static final int CONDITION = -2;
		//共享锁，用于解锁下一个节点
        static final int PROPAGATE = -3;
		//Node等待状态
        volatile int waitStatus;

        volatile Node prev;

        volatile Node next;
		//Node绑定的线程
        volatile Thread thread;

        Node nextWaiter;

        /**
         * Returns true if node is waiting in shared mode.
         */
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

        Node() {    // Used to establish initial head or SHARED marker
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
}
```

AbstractQueuedSynchronizer内部有一个以Node对象为节点的<font color='red'>双向链表队列</font>，AbstractQueuedSynchronizer中的head变量为队列头，tail变量为队列尾,并且它们都是transient关键字修饰的，这里解释下它的语义:

> 当对象被序列化时（写入字节序列到目标文件）时，transient阻止实例中那些用此关键字声明的变量持久化；当对象被反序列化时（从源文件读取字节序列进行重构，这样的实例变量值不会被持久化和恢复。

AbstractQueuedSynchronizer中的state属性是一个以<font color='cornflowerblue'>CAS非阻塞同步操作</font>维护的volatile修饰的变量。

Node类中的waitStatus表示当前的等待状态:

- CANCELLED 1 表示该Node放弃竞争锁
- SIGNAL -1 表示Node内部的线程将挂起（通过LockSupport的park方法）
- CONDITION  -2 表示Node在Condition中的队列等待
- PROPAGATE -3 本节点的状态将传递给下一个节点？（不太清楚）

Node中还维护了prev、next等双向链表所必要的引用，并且每个Node中维护一个线程对象，该线程即参与通过CAS操作竞争修改AbstractQueuedSynchronizer中state失败的线程。

## AbstractQueuedSynchronizer acquire方法

```java
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
```

多线程竞争占有锁通过acquire方法实现，其中tryAcquire方法分别由子类NonfairSync和FairSync实现具体的。

<font color='red'>注意</font>：双向队列中的head节点存储当前获取资源的线程，下一个节点为排队第一的节点

### tryAcquire方法

**非公平模式NonfairSync 的tryAcquire方法**

```java
protected final boolean tryAcquire(int acquires) {
    return nonfairTryAcquire(acquires);
}

final boolean nonfairTryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {//非公平模式允许新节点与第一个排队节点竞争
        if (compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0) // overflow
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}
```

**公平模式FairSync的tryAcquire方法**

```java
protected final boolean tryAcquire(int acquires) {
    final Thread current = Thread.currentThread();
    int c = getState();
    if (c == 0) {
        if (!hasQueuedPredecessors() &&
            compareAndSetState(0, acquires)) {
            setExclusiveOwnerThread(current);
            return true;
        }
    }
    else if (current == getExclusiveOwnerThread()) {
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        setState(nextc);
        return true;
    }
    return false;
}

public final boolean hasQueuedPredecessors() {
    Node t = tail; // Read fields in reverse initialization order
    Node h = head;
    Node s;
    return h != t &&
        ((s = h.next) == null || s.thread != Thread.currentThread());
}
```

**[非]公平模式的实现区别**

- NonfairSync  在AQS的state为0的情况下，利用CAS将state改为acquires，如果成功则调用setExclusiveOwnerThread方法将exclusiveOwnerThread这个变量设置为当前线程，表明当前线程占有锁，然后返回true。在竞争激烈的情况下，CAS可能返回失败，或者state不为0，表示锁已被其他线程独占。因此第二个判断比较当前线程与exclusiveOwnerThread变量是否相等，如果相等说明是同一线程的操作，将state加上acquires并更新回去返回true。上述条件都不满足，当前线程竞争锁失败返回false。

- FairSync  相比NonfairSync 在CAS这一步前先执行hasQueuedPredecessors方法

  ```
  hasQueuedPredecessors方法用于判断等待队列中是否有Node排在currentNode前，若有则返回true，表示currentNode需要排队；
  若等待队列为空（h==t），则返回false，说明currentNode为第一个等待节点，可以执行CAS操作
  若等待队列不为空(h!=t)，currentNode为等待列表的第一个节点，则返回false，说明排队结束，可以执行CAS操作
  ```

可见公平模式会保证线程占有锁的顺序与AQS内部的Node队列顺序相同，非公平模式允许新插入Node队列尾部的Node线程插队竞争一次锁。

### addWaiter方法

对于尝试获取锁失败的线程下一步执行acquireQueued(addWaiter(Node.EXCLUSIVE), arg)，首先是addWaiter方法:

```java
private Node addWaiter(Node mode) {
    //根据mode构建节点
    Node node = new Node(Thread.currentThread(), mode);
    // 尝试将node加入都队尾
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

private Node enq(final Node node) {
    //自旋
    for (;;) {
        Node t = tail;
        if (t == null) { // 队尾为null，说明队列未初始化，需要先初始化队列
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {//尝试将node添加到队尾
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

addWaiter先将当前线程封装为一个Node对象A，如果队列未初始化就先通过enq方法利用CAS将head和tail节点设置为同一个新Node对象，然后把A Node插入到队列的尾部并返回。如果队列已经有tail节点，就把A Node插入到tail后，并将tail设置为A返回。注意这里用CAS完全是为了并发竞争。

### acquireQueued方法

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            //若当前节点排队到了第一位，则尝试获取锁，并移除节点
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

这里的node参数即刚才插入队列的Node对象，暂且还叫它A，首先判断A的前一个节点是不是head节点，如果是表示A是队列的第一个等待线程。如果在它插入的过程中占有锁的线程可能执行完毕释放了锁，所以接着执行tryAcquire尝试获取锁，如果成功就将head设置为A，并且将原head的next设置为null，这样head节点成了A。

[**shouldParkAfterFailedAcquire方法**](https://blog.csdn.net/anlian523/article/details/106448512/)

如果A不是head节点后的节点，或者尝试获取锁失败，执行shouldParkAfterFailedAcquire方法，判断线程是否需要挂起:

```java
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL)
        return true;
    if (ws > 0) {
        do {//删除之前的“取消节点”
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}

private static final boolean compareAndSetWaitStatus(Node node,int expect,int update) {
    return unsafe.compareAndSwapInt(node, waitStatusOffset, expect, update);
}
```

这里有三种情况：

1. 如果前一个节点的状态为-1（SIGNAL），表示当前线程需要阻塞，返回true。
2. 如果前一个节点的状态大于0，这里只有CANCELLED状态大于0，表示前一个线程已被取消竞争，将前一个节点移除，直到前一个节点的状态不大于0，并返回false。
3. 如果前一个节点的状态为为0，将前一节点的状态通过CAS设置为-1（SIGNAL），返回false，这样下一次循环本线程就会阻塞。

对shouldParkAfterFailedAcquire方法返回false的Node进入下一次循环 ，直到前节点被CAS设置为-1（SIGNAL）。

**parkAndCheckInterrupt方法**

若shouldParkAfterFailedAcquire返回true，表示将要对当前线程挂起

```java
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();
}
```

通过LockSupport的park方法将线程挂起，<font color='red'>避免线程频繁自旋</font>，待前节点处理完后通过release方法可unpark唤起线程。

```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}

protected final boolean tryRelease(int releases) {
    int c = getState() - releases;
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {//若所有资源都被归还，解除线程对等待队列的占有
        free = true;
        setExclusiveOwnerThread(null);
    }
    setState(c);
    return free;
}

private void unparkSuccessor(Node node) {
    /*
     * If status is negative (i.e., possibly needing signal) try
     * to clear in anticipation of signalling.  It is OK if this
     * fails or if status is changed by waiting thread.
     */
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);

    /*
     * Thread to unpark is held in successor, which is normally
     * just the next node.  But if cancelled or apparently null,
     * traverse backwards from tail to find the actual
     * non-cancelled successor.
     */
    Node s = node.next;
    if (s == null || s.waitStatus > 0) {
        s = null;
        //获取第一个“非取消节点”（为什么不从头开始？）
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    //解除线程挂起，唤起等待队列中下一个节点
    if (s != null)
        LockSupport.unpark(s.thread);
}
```

### 占有锁小结

占有锁的实现分为公平和非公平模式，公平模式下线程会按照请求的顺序依次获取锁，非公平模式下允许最后尝试获取锁的线程插队与队列第一个等待线程竞争一次。

当线程未获取锁，先将自身同步插入到锁等待队列，接着进入循环，如果当前线程是队列的第一个，尝试获取锁。如果当前线程未获取锁（无论是不是队列里第一个线程），都尝试将前一个节点的状态设置为SIGNAL（让前一个节点结束后提醒自己），然后挂起。

![img](https:////upload-images.jianshu.io/upload_images/5222801-b5ad0fc39b85e689.png?imageMogr2/auto-orient/strip|imageView2/2/w/911/format/webp)

<center>AQS锁等待队列.png</center>

所以并发执行acquire方法尝试获取锁的多个线程，最后的结果就是其中一个线程占有锁，其他线程都插入到锁等待队列里挂起，并且除了尾节点状态为0，其它Node的状态是SIGNAL或CANCELLED，引起CANCELLED状态的分析在后面的tryLock(long timeout, TimeUnit unit)方法中。

## AbstractQueuedSynchronizer release方法

释放锁与占有锁的过程是相反的，相比synchronized，ReentrantLock需要在代码中显示调用AQS的release方法:

```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {//尝试释放资源
        Node h = head;
        if (h != null && h.waitStatus != 0)
            //唤醒下一个等待队列
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```

首先是执行tryRelease方法，该方法由子类Sync实现:

```java
protected final boolean tryRelease(int releases) {
    int c = getState() - releases;
    //仅持有资源的线程才能释放资源
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {//置空资源对应的线程
        free = true;
        setExclusiveOwnerThread(null);
    }
    //更新等待队列状态
    setState(c);
    return free;
}
```

首先将AQS的state剪去参数releases，然后判断当前线程是不是持有锁的线程，如果是就继续，不是则抛出llegalMonitorStateException异常。因此在代码层不允许未占有锁的线程执行release方法。

接下来判断state是否为0，如果是就将占有锁的线程设置为NULL，利用CAS将state为0同步更新，返回true。<font color='cornflowerblue'>如果state不为0表示当前线程还将继续占有锁，返回false</font>。

如果tryRelaese方法返回false，则release方法也返回false。如果tryRelease返回true，表示状态已重置为0，当前线程不再占有锁。接下来判断锁等待队列的head是不是空，如果不是空并且waitStatus不为0则执行unparkSuccessor方法。

<font color='red'>为什么waitStatus不为0才执行唤醒方法呢，上文中提到当前节点的线程在挂起前一定要将前一节点的waitStatus更新为-1,所以如果head节点的waitStatus如果还是0，表示head以后的节点线程并未被挂起，该线程会进入下一次循环尝试获取锁。</font>

**unparkSuccessor方法**

unparkSuccessor方法顾名思义就是将head后阻塞中的线程恢复:

```csharp
private void unparkSuccessor(Node node) {
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);
    Node s = node.next;
    if (s == null || s.waitStatus > 0) {
        s = null;
        //找到最近的“非取消节点”
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    if (s != null)
        LockSupport.unpark(s.thread);
}
```

先将head的状态通过CAS同步更新为0，如果head节点的下一个节点为空，则什么都不做。

如果head节点的下一个节点不为空并且waitStatus小于等于0，将head下一节点的线程唤醒。被唤醒的线程会继续执行acquireQueued方法内的死循环竞争获取锁。如果当前为非公平模式，此时如果又有一个新的线程尝试获取锁，这个刚被唤醒的线程可能会竞争失败继续挂起。如果当前为公平模式，唤醒的线程会通过CAS成功获取锁，因为新线程只会插入到锁等待队列的尾部挂起。

如果head节点的下一个节点不为空，但是waitStatus大于0，表示下一个线程被取消竞争，此时会从队列尾部向头部开始遍历，找到第一个waitStatus为-1或0并且非head节点的Node，最后将该Node中的线程唤醒。

### 释放锁小结

在占有锁的小结中提到，多线程竞争锁以后除了一个线程获取锁以外，其他线程都将插入到Node队列并挂起。而释放锁方法的执行只对应于已占有锁的线程，该方法会将AQS的state通过CAS同步更新为0，然后唤醒线程队列中除了head节点的首个处于SIGNAL或0状态的线程。

![img](https:////upload-images.jianshu.io/upload_images/5222801-d451f8f3167619f6.png?imageMogr2/auto-orient/strip|imageView2/2/w/1111/format/webp)

<center>AQS锁的释放2.png</center>

# ReentrantLock详解

## lock()与unlock()

ReentrantLock 的lock方法的实际实现是委托给内部的NonfairSync和FairSync的。

```java
public void lock() {
    sync.lock();
}

//公平模式下ReentrantLock的lock方法等同于调用AQS的acquire(1)；
static final class FairSync extends Sync {
    final void lock() {
        acquire(1);
    }
}
//非公平模式下只是先尝试性通过CAS将state从0更新为1，如果失败等同于调用AQS的acquire(1)方法。
static final class NonfairSync extends Sync {
    final void lock() {
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1);
    }
}
```


 unlock方法

```cpp
//unlock方法等同于调用AQS的release(1)。
public void unlock() {
    sync.release(1);
}
```

## tryLock()与tryLock(long timeout, TimeUnit unit)

**tryLock()**

tryLock的方法实现同**非公平模式**下的tryAcquire方法，尝试占有可重入锁，如果失败返回false，并不像lock方法将线程挂起。

```java
public boolean tryLock() {
    return sync.nonfairTryAcquire(1);
}
```

**tryLock(long timeout, TimeUnit unit)**

```java
public boolean tryLock(long timeout, TimeUnit unit)
        throws InterruptedException {
    return sync.tryAcquireNanos(1, unit.toNanos(timeout));
}
```

tryLock(long timeout, TimeUnit unit)方法就稍微复杂一点，该方法的实现还是在AQS的tryAcquireNanos方法:

```java
public final boolean tryAcquireNanos(int arg, long nanosTimeout)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    return tryAcquire(arg) ||
        doAcquireNanos(arg, nanosTimeout);
}
```

tryAcquireNanos方法先调用两种模式的tryAcquire方法尝试占有锁，如果失败则执行doAcquireNanos方法:

```java
private boolean doAcquireNanos(int arg, long nanosTimeout) throws InterruptedException {
    if (nanosTimeout <= 0L)
        return false;
    //到期时间
    final long deadline = System.nanoTime() + nanosTimeout;
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return true;
            }
            nanosTimeout = deadline - System.nanoTime();
            //在挂起前判断是否超时
            if (nanosTimeout <= 0L)
                return false;
            if (shouldParkAfterFailedAcquire(p, node) && nanosTimeout > spinForTimeoutThreshold)
                //挂起，在超时前会再次唤醒竞争资源
                LockSupport.parkNanos(this, nanosTimeout);
            if (Thread.interrupted())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

该方法的实现与acquireQueued非常类似，它在内部调用addWaiter先将线程添加到线程队列中，然后计算出终止阻塞的时间，接着进入死循环，先尝试占有锁，如果成功就将节点从队列移除返回true；未占有锁就通过shouldParkAfterFailedAcquire的CAS操作将前一节点设置为SIGNAL状态后就阻塞nanosTimeout 长度的时间，该时间是终止时间减去当前时间。当该线程再次被唤醒时会再次尝试获取锁，若还是获取不到就返回false。并且在失败的情况下执行cancelAcquire方法:

```csharp
private void cancelAcquire(Node node) {
    if (node == null)
        return;

    node.thread = null;
    Node pred = node.prev;
    while (pred.waitStatus > 0)//删除过期节点
        node.prev = pred = pred.prev;
    Node predNext = pred.next;
    node.waitStatus = Node.CANCELLED;

    // 如果当前为“尾节点”，删除自身
    if (node == tail && compareAndSetTail(node, pred)) {
        compareAndSetNext(pred, predNext, null);
    } else {
        // If successor needs signal, try to set pred's next-link
        // so it will get one. Otherwise wake it up to propagate.
        int ws;
        if (pred != head && ((ws = pred.waitStatus) == Node.SIGNAL ||
             (ws <= 0 && compareAndSetWaitStatus(pred, ws, Node.SIGNAL))) && pred.thread != null) {
            Node next = node.next;
            if (next != null && next.waitStatus <= 0)
                compareAndSetNext(pred, predNext, next);
        } else {
            unparkSuccessor(node);
        }
        node.next = node; // help GC
    }
}
```

该方法将Node节点前waitStatus大于0即CANCELLED状态的节点移除队列，然后将当前超时的Node节点设置为CANCELLED状态。

如果当前节点为队列的尾部，利用CAS将其移除队列。

如果当前节点不是尾部节点，在上个节点不是head节点的情况下，如果上个节点处于SIGNAL状态或尝试将其设置为SIGNAL状态成功，并且上个节点的线程不为null，如果下一个节点为SIGNAL状态，将当前节点移除。

不满足上述两个条件将继续执行unparkSuccessor方法，即唤醒继任者线程(继任节点会删除当前“取消节点”)

**tryLock方法小结**

不带参数的tryLock方法尝试非公平获取锁，如果获取失败并不会挂起，而是返回结果false。
 带参数的tryLock(long timeout, TimeUnit unit)通过tryAcquire方法尝试获取锁，如果获取失败当前线程会挂起timeout长度的时间，如果在指定时间还未占有锁就返回false，并且将当前Node置为CANCELLED状态，这就是CANCELLED状态的由来。

## lockInterruptibly()

允许线程未占有锁而阻塞时被中断

```java
public void lockInterruptibly() throws InterruptedException {
    sync.acquireInterruptibly(1);
}

public final void acquireInterruptibly(int arg)
        throws InterruptedException {
    if (Thread.interrupted())//响应中断异常
        throw new InterruptedException();
    if (!tryAcquire(arg))
        doAcquireInterruptibly(arg);
}
```

它的实现依然是AQS定义的acquireInterruptibly方法。注意到如果线程是中断的则抛出InterruptedExeception。然后先调用tryAcquire尝试占有锁，在获取锁失败的情况下执行doAcquireInterruptibly方法:

```java
private void doAcquireInterruptibly(int arg)
    throws InterruptedException {
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return;
            }
            if (shouldParkAfterFailedAcquire(p, node) && parkAndCheckInterrupt())
                throw new InterruptedException();//线程被唤醒时，若线程已经被中断，抛出异常
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

对比doAcquireNanos方法，发现两者的实现十分类似。先将线程Node插入到队列尾部，然后将上个节点状态更新为SIGNAL，接着调用parkAndCheckInterrupt方法挂起当前线程:

```java
private final boolean parkAndCheckInterrupt() {
    LockSupport.park(this);
    return Thread.interrupted();
}
```

这里的不设置阻塞时间上限，如果线程唤醒后已经中断，则抛出InterruptedException异常。

**lockInterruptibly方法小结**

lockInterruptibly方法对比lock方法的区别是，该方法在**调用时就检查**当前线程是否中断，如果当前线程中断就不再尝试获取锁而是直接抛出InterruptedException异常。如果线程被挂起，**唤醒后同样也会检查**中断状态，一旦发现线程被中断就会抛出InterruptedException异常。

而lock方法在调用时不检查线程的中断状态，调用lock方法挂起的线程唤醒后虽然也检查线程是否中断，但是不会抛出异常，<font color='cornflowerblue'>lock方法把中断延时到了同步区域去处理异常（在同步代码块实现自定义中断处理）。</font>

# Condition

在使用synchronized同步代码块内使用Object的wait()、notify()、notifyAll()方法能够实现线程间的生产者消费者模型，而在ReentrantLock中也有它的实现方法。通过newCondition方法可以调用AQS的newCondition方法:

```java
final ConditionObject newCondition() {
    return new ConditionObject();
}
```

ConditionObject 是AQS内的一个成员类，也就是AQS的状态和方法也可以在ConditionObject类访问,它的内部也有一个队列，firstWaiter为队列头，lastWaiter为队列尾。

```java
唤醒所有等待中的线程public class ConditionObject implements Condition, java.io.Serializable {
        /** First node of condition queue. */
        private transient Node firstWaiter;
        /** Last node of condition queue. */
        private transient Node lastWaiter;
}

public interface Condition {
    // 让当前线程等待直到被通知或中断
    void await() throws InterruptedException;
    //让当前线程等待直到被通知，无法被中断
    void awaitUninterruptibly();
    //让当前线程等待直到被通知、中断或超时
    long awaitNanos(long nanosTimeout) throws InterruptedException;
    boolean await(long time, TimeUnit unit) throws InterruptedException;
    //让当前线程等待直到被通知、中断或越过指定时间
    boolean awaitUntil(Date deadline) throws InterruptedException;
    //唤醒等待中的一个线程
    void signal();
    //唤醒所有等待中的线程
    void signalAll();
}
```

通过newCondition方法可以产生多个ConditionObject 对象，即一个ReentrantLock可以对应多个ConditionObject对象 ，而每个ConditionObject对象的await()、signal()、signalAll()方法是相互独立的。而在Object的wait()、notify()、notifyAll()方法中，这些同步方法只是针对这一个对象。

## await()

```java
public final void await() throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    //新增节点加入condition队列
    Node node = addConditionWaiter();
    //释放资源，更新state
    int savedState = fullyRelease(node);
    int interruptMode = 0;
    while (!isOnSyncQueue(node)) {//若节点不在sync队列中，挂起线程
        LockSupport.park(this);
        //判断挂起期间是否被中断
        if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
            break;
    }
    //中断处理
    if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
        interruptMode = REINTERRUPT;
    if (node.nextWaiter != null) // clean up if cancelled
        unlinkCancelledWaiters();
    if (interruptMode != 0)
        reportInterruptAfterWait(interruptMode);
}

private Node addConditionWaiter() {
    Node t = lastWaiter;
    // If lastWaiter is cancelled, clean out.
    if (t != null && t.waitStatus != Node.CONDITION) {
        unlinkCancelledWaiters();
        t = lastWaiter;
    }
    //新建节点并加入condition队列
    Node node = new Node(Thread.currentThread(), Node.CONDITION);
    if (t == null)
        firstWaiter = node;
    else
        t.nextWaiter = node;
    lastWaiter = node;
    return node;
}

private void unlinkCancelledWaiters() {
    Node t = firstWaiter;
    Node trail = null;
    while (t != null) {//遍历condition队列，删除“非condition节点”（一般是“超时取消节点”）
        Node next = t.nextWaiter;
        if (t.waitStatus != Node.CONDITION) {
            t.nextWaiter = null;
            if (trail == null)
                firstWaiter = next;
            else
                trail.nextWaiter = next;
            if (next == null)
                lastWaiter = trail;
        }
        else
            trail = t;
        t = next;
    }
}

final int fullyRelease(Node node) {
    boolean failed = true;
    try {
        int savedState = getState();
        if (release(savedState)) {//释放资源
            failed = false;
            return savedState;
        } else {
            throw new IllegalMonitorStateException();
        }
    } finally {
        if (failed)
            node.waitStatus = Node.CANCELLED;
    }
}
```

当一个线程调用了await方法，调用addConditionWaiter方法，首先调用unlinkCancelledWaiters方法清除ConditionObject队列中非Condition状态的节点，接着将自身线程封装为一个waitStatus为CONDITION的Node， 并插入到ConditionObject内部的队列，这个队列同AQS的Node队列也是先进先出。

接着调用fullyRelease方法获取当前AQS的state，并调用release方法释放锁，唤醒AQS Node队列中第一个挂起的线程。到这一步当前线程已经交出了锁的控制权。

接着进入循环，退出条件是isOnSyncQueue方法返回true，SyncQueue即上文中AQS内的Node队列，该队列上的线程都在等待锁，下面分析下这个方法:

```kotlin
final boolean isOnSyncQueue(Node node) {
    if (node.waitStatus == Node.CONDITION || node.prev == null)
        return false;
    if (node.next != null) // If has successor, it must be on queue
        return true;
    return findNodeFromTail(node);
}

private boolean findNodeFromTail(Node node) {
    Node t = tail;
    for (;;) {
        if (t == node)
            return true;
        if (t == null)
            return false;
        t = t.prev;
    }
}
```

- 情况一， 如果插入的节点为CONDITION 状态或它的前一节点为空，表示该节点处于等待状态，并未加入锁的等待队列返回false。
- 情况二，如果插入的节点有下一个节点，返回true。
- 情况三，上述都不满足调用findNodeFromTail，该方法从AQS的tail节点开始找当前节点是否在AQS的Node队列中。

循环内，当前线程会通过LockSupport的park方法挂起，当线程被唤醒，调用checkInterruptWhileWaiting方法判断线程是不是中断了:

```java
private int checkInterruptWhileWaiting(Node node) {
    return Thread.interrupted() ? (transferAfterCancelledWait(node) ? THROW_IE : REINTERRUPT) : 0;
}

final boolean transferAfterCancelledWait(Node node) {
    if (compareAndSetWaitStatus(node, Node.CONDITION, 0)) {
        enq(node);
        return true;
    }
    while (!isOnSyncQueue(node))
        Thread.yield();
    return false;
}
```

如果线程中断，就尝试通过CAS将Node状态更新为0，如果成功就插入AQS的Node队列，返回THROW_IE 标记。如果Node当前状态不为CONDITION，则返回REINTERRUPT标记。然后跳出循环。

跳出循环后，执行acquireQueued方法,该方法在上述AQS的acquire方法的过程中分析过，用于尝试获取锁。取得锁后先清除ConditionObject中条件等待队列非Condition状态的Node，然后根据interruptMode标记决定抛出异常（THROW_IE ），还是交给同步代码块处理（REINTERRUPT）。

**await方法小结**

从await代码的分析中得知，await方法将当前线程封装为Node对象插入到Condition的条件等待队列，然后将AQS锁完全释放，唤醒AQS锁等待队列中的下一个SIGNAL线程。

接着将当前线程挂起，直到线程被中断或唤醒，尝试调用acquireQueued方法获取AQS的锁，该方法在AQS的aquire方法中介绍过，它会判断node是不是锁等待队列HEAD后的节点，如果是就尝试占有锁，否则该线程会清除之前的CACELLED状态的节点后再次判断，如果还不是就挂起。占有AQS同步锁后根据中断标记决定是直接抛出中断异常还是由同步代码块处理中断。

![img](https:////upload-images.jianshu.io/upload_images/5222801-e25eee17f6f570d1.png?imageMogr2/auto-orient/strip|imageView2/2/w/840/format/webp)

<center>await方法执行过程.png</center>

疑问???

```
执行await方法的线程的Node这是添加到ConditionObject的条件等待队列，为何线程醒来后要把自己当成AQS的锁等待队列的节点？见signal方法的分析。
```

## signal()

```java
public final void signal() {
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    Node first = firstWaiter;
    if (first != null)
        doSignal(first);
}
```

首先判断调用 signal方法的线程是不是锁的持有者线程，然后获取ConditionObject条件等待队列的头结点，对其调用doSignal方法:

```java
private void doSignal(Node first) {
    do {
        if ( (firstWaiter = first.nextWaiter) == null)//更新尾结点
            lastWaiter = null;
        first.nextWaiter = null;
    } while (!transferForSignal(first) &&
             (first = firstWaiter) != null);
}

final boolean transferForSignal(Node node) {
    /*
     * If cannot change waitStatus, the node has been cancelled.
     */
    if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
        return false;
    Node p = enq(node);
    int ws = p.waitStatus;
    if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
        LockSupport.unpark(node.thread);
    return true;
}
```

首先将ConditionObjcect内的Node队列的头结点指向下一个节点，即将头节点移除，然后执行transferForSignal方法，该方法先尝试同步更新头节点为0，如果失败说明当前Node是无效的，继续循环将头节点后移一个。

如果节点成功更新为状态0，将该Node插入到AQS的线程队列中。仅当该Node为取消状态或更新为SIGNAL状态失败才唤醒该线程。

通常情况下，ConditionObject中的Node插入到AQS的锁等待队列中后，由unlock方法释放锁后由AQS的release方法去唤醒线程，也就是调用LockSupport的unpark方法，在上文分析过。这里也是Node从条件等待队列转换到AQS的锁等待队列的实现，并且将Node从CONDITION状态更新为0.

**signal方法小结**

await方法执行后，线程A会封装为Node插入到ConditionObject的条件等待队列中，并且会挂起交出AQS锁的控制权。当另一个线程B调用了signal方法，ConditionObject条件等待队列中的头个处于CONDITION状态的Node（线程A）会被插入到AQS的锁等待队列中并同步更新状态为0，当线程B释放了锁，会唤醒线程A，线程A获取锁后可以继续执行await方法后的同步代码。

![img](https:////upload-images.jianshu.io/upload_images/5222801-ccda6b1a12fd645f.png?imageMogr2/auto-orient/strip|imageView2/2/w/986/format/webp)

<center>signal.png</center>

## signalAll()

相比signal方法，signalAll其实就是唤醒ConditionObject中条件等待队列里所有状态为CONDITION的线程去竞争锁。

```java
public final void signalAll() {
    if (!isHeldExclusively())
        throw new IllegalMonitorStateException();
    Node first = firstWaiter;
    if (first != null)
        doSignalAll(first);
}

private void doSignalAll(Node first) {
    lastWaiter = firstWaiter = null;
    do {
        Node next = first.nextWaiter;
        first.nextWaiter = null;
        transferForSignal(first);
        first = next;
    } while (first != null);
}
```

signal方法只会将条件等待队列的头个节点插入到AQS的锁等待队列，而signalAll方法尝试将条件等待队列的所有状态为CONDITION的节点插入到AQS的锁等待队列。



# ReentrantLock与synchronize

|          | ReentrantLock                                    | synchronize                  |
| -------- | ------------------------------------------------ | ---------------------------- |
| 锁对象   | lock=new ReentrantLock(),lock为锁对象            | synchronize(obj),obj为锁对象 |
| 同步开始 | lock.lock();                                     | {                            |
| 同步结束 | lock.unlock();                                   | }                            |
| 等待     | condition=lock.newCondition()，condition.await() | obj.await();                 |
| 唤醒     | condition.signal()                               | obj.signal();                |

每次newCondition()都会创建一个新的ConditionObject，持有不同的condition等待队列。

# 总结

AbstractQueuedSynchronizer的锁等待队列和ConditionObject的条件等待队列是ReentrantLock实现的关键，这两个队列共用了Node类，所不同的是条件等待队列的Node状态一般是CONDITION，而锁等待队列的状态一般是SIGNAL或者0，两种队列的NODE都有CANCELLED状态。

本以为ReentrantLock的内部实现像AtomicInteger一样简单的调用UnSafe类的CAS算法就实现了，实际的分析过程中发现还是挺复杂的，主要是这个类牵扯到大量的CAS同步操作竞争锁，所以看源码就不能仅仅靠单线程思维，还要发散成多线程思维，想想这里的同步操作是不是为了避免某个并发问题。



