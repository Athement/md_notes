# 同步控制方法

 - synchronized关键字
 - wait(),notify()方法
 - 重入锁
## 1.关键字 synchronized 的功能扩展： 重入锁
 **可重入锁**:代码已经获得锁,再次获取该锁时,不会被阻塞

 - 重入锁可以替代synchronized (<font color='red'>synchronized也是可重入的</font>)
 - 重入锁使用 java.util.concurrent.Locks.**ReentrantLock** 类来实现

### 可重入锁示例

```java
public class ReenterLock implements Runnable{
	public static ReentrantLock lock=new ReentrantLock();
	public static int i=0;
	@Override
	public void run() {
		for(int j=0;j<10000000;j++){
			lock.lock();
			lock.lock();
			try{
				i++;
			}finally{
				lock.unlock();
				lock.unlock();
			}
		}
	}
	public static void main(String[] args) throws InterruptedException {
		ReenterLock tl=new ReenterLock();
		Thread t1=new Thread(tl);
		Thread t2=new Thread(tl);
		t1.start();t2.start();
		t1.join();t2.join();
		System.out.println(i);
	}
}
```
代码解析

 1. 重入锁必须显式加锁和释放锁,灵活性比synchronized高 
 2. 重入表示锁在一个线程中是可以重复进入的
 3. 获取锁和释放锁的次数必须相匹配
     获取>释放:仍持有锁,没有释放
     获取<释放:抛出<font color=red>java.lang.IllegalMonitorStateException异常</font>
```java
public void lock()//获取锁
public void unlock()//释放锁
```
可重入锁调度原理

1. 如果该锁没有被另一个线程保持，则获取该锁并立即返回，将<font color='cornflowerblue'>锁的保持计数</font>设置为 1
2. 如果当前线程已经保持该锁，则将保持计数加 1(用于释放锁)，并且该方法立即返回
3. 如果该锁被另一个线程保持，线程调度会禁用当前线程，并且在获得锁之前，该线程将处于<font color=red>休眠状态</font>
### 可重入锁的功能

**1. 响应中断**

```java
public void lockInterruptibly() throws InterruptedException
```
- 类似wait/sleep,若lockInterruptibly<font color='cornflowerblue'>执行前</font>或<font color='cornflowerblue'>阻塞时</font>线程被设置了中断状态,则会抛出InterruptedException,之后清空中断

  <font color='red'>lockInterruptibly会**优先处理中断**,而不是获取锁</font>

```java
public class IntLock implements Runnable {
    public static ReentrantLock lock1 = new ReentrantLock();
    public static ReentrantLock lock2 = new ReentrantLock();
    int lock;
    public IntLock(int lock) {
        this.lock = lock;
    }
    @Override
    public void run() {
        try {
            if (lock == 1) {
                lock1.lockInterruptibly();
                try{
                    Thread.sleep(500);
                }catch(InterruptedException e){}
                lock2.lockInterruptibly();
            } else {
                lock2.lockInterruptibly();
                try{
                    Thread.sleep(500);
                }catch(InterruptedException e){}
                lock1.lockInterruptibly();
            }

        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            if (lock1.isHeldByCurrentThread())
                lock1.unlock();
            if (lock2.isHeldByCurrentThread())
                lock2.unlock();
            System.out.println(Thread.currentThread().getId()+":线程退出");
        }
    }
    public static void main(String[] args) throws InterruptedException {
        IntLock r1 = new IntLock(1);
        IntLock r2 = new IntLock(2);
        Thread t1 = new Thread(r1);
        Thread t2 = new Thread(r2);
        t1.start();t2.start();
        Thread.sleep(1000);
        //中断其中一个线程
        t2.interrupt();
    }
}
```
代码解析
>1. t1与t2在资源竞争时,形成死锁
>2. 通过线程外部调用t2.interrupt(),使t2抛出异常,释放锁资源,打破死锁

**2. 锁申请限时**

```java
//不响应中断
public boolean tryLock() 
//若限定时间内获取不到锁则返回false;若请求前或阻塞时被设置了中断则抛出异常
public boolean tryLock(long timeout,TimeUnit unit) throws InterruptedException
```
- lockInterruptibly()相当于tryLock(**inf**)
- 获取锁失败会返回false,而不是线程休眠

**3. 公平锁**

非公平锁:系统从锁的等待队列中随机取出一个线程
公平锁:保证锁的等待线程按时间顺序获取(**不会产生饥饿**)

>实现公平锁需要维护一个有序队列,实现成本高,性能很低.
```java
public ReentrantLock(boolean fair)
true为公平锁,false为非公平锁
```


### ReentrantLock方法与要素
**常用方法**
```java
 1. lock()： 获得锁， 如果锁已经被占用， 则线程休眠。 
 2. lockInterruptibIy()： 获得锁， 但优先响应中断。
 3. tryLock()： 尝试获得锁，成功则返回true;该方法不等待， 立即返回。 
 4. tryLock(Iong time,TimeUnit unit)： 在给定时间内尝试获得锁。
 5. unlock()； 释放锁int 
 6. getHoldCount()查询当前线程对此锁的暂停数量。 
 7. final boolean hasQueuedThread(Thread thread) thread是否在等待锁
 8. Thread getOwner() 获取锁的持有线程(protected)
```
**重入锁的三个要素**
 1. 原子状态:用CAS操作存取锁的状态
 2. 等待队列:未获得锁的线程会进入等待队列
 3. 阻塞原语:park()与unpark(),分别用来挂起和恢复线程

 ## 2.Condition
 **Condition的创建**
```java
Condition cond=lock.newCondition();//将condition与lock关联
```
**Condition方法**
```java
boolean await(long time, TimeUnit unit) 
使当前线程等待直到发出信号或中断，或指定的等待时间过去。  
long awaitNanos(long nanosTimeout) throws InterruptedException;
等待一定时间,返回值为[nanosTimeout-已等待时间]
void signal()/signalAll()
唤醒等待线程。  
```
代码演示

```java
public class ReenterLockCondition implements Runnable{
	public static ReentrantLock lock=new ReentrantLock();
	public static Condition condition = lock.newCondition();
	@Override
	public void run() {
		try {
			lock.lock();
			condition.await();
			System.out.println("Thread is going on");
		} catch (InterruptedException e) {
			e.printStackTrace();
		}finally{
			lock.unlock();
		}
	}
	public static void main(String[] args) throws InterruptedException {
		ReenterLockCondition tl=new ReenterLockCondition();
		Thread t1=new Thread(tl);
		t1.start();
		Thread.sleep(2000);
		//通知线程t1继续执行
		lock.lock();
		condition.signal();
		lock.unlock();
	}
}
```
代码分析(**Object与Condition**)
>1. lock+unlock=synchronized{}
>2. cond.await()=obj.wait()
>3. cond.signal()=obj.notify()
>4. await/signal要求获得与cond相关联的锁
>wait/notify要求在相同对象的监视器中
>
>**Lock+Condition的应用**
>ArrayBlockingQueue的put()和take()
## 3.允许多个线程同时访问： 信号量 
- synchronized和可重入锁只允许一个线程访问一个资源
- Semaphore却可以指定多个线程， 同时访问某一个资源

**构造函数**

```java
public Semaphore(int permits)//初始化资源数
public Semaphore(int permits,boolean fair) //指定是否公平获取信号量
```
**信号量方法**

```java
public void acquire(int permits=1)//响应中断
public void acquireUninterruptlbly()//不响应中断
public boolean tryAcquire(long timeoutf,TimeUnit unit)
public void release(int permits=1)
public int availablePermits()//返回此信号量中当前可用的许可数。 
```
<font color='red'>注意</font>：release可释放的资源数可超过初始化资源数

**代码演示**

```java
public class SemapDemo implements Runnable {
	final Semaphore semp = new Semaphore(5);
	@Override
	public void run() {
		try {
			semp.acquire();
			Thread.sleep(2000);
			System.out.println(Thread.currentThread().getId() + ":done!");
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			semp.release();
		}
	}
	public static void main(String[] args) {
		ExecutorService exec = Executors.newFixedThreadPool(20);
		final SemapDemo demo = new SemapDemo();
		for (int i = 0; i < 20; i++) {
			exec.submit(demo);
		}
	}
}
```
>最多同时可以有5个线程访问资源
>线程池保持固有线程数,执行安排的任务

## 4.ReadWriteLock 读写锁
读操作时不修改数据内容,可以通过并发读提高性能.

```java
ReentrantReadWriteLock readWriteLock=new ReentrantReadWriteLock();
Lock readLock = readWriteLock.readLock();
Lock writeLock = readWriteLock.writeLock();
```
**读写锁使用**

>1. ReadLock与WriteLock都是以ReentrantReadWriteLock的静态内部类
>2. ReadLock与WriteLock都是单例模式,并在ReentrantReadWriteLock构造函数中初始化
>3. ReadLock与WriteLock的使用方法与Lock相同.xxxxlock.lock()搭配xxxxlock.unlock()
>4. <font color=red>readLock.lock()之间不会阻塞</font>

## 5.倒计数器： CountDownLatch
CountDownLatch在倒计时结束前会阻塞,倒计时结束后继续执行.
**构造函数**
```java
 public CountDownLatch(int count)//设置倒计时起始数值>=0
```
**主要函数**

```java
public void countDown() // 计数减1
public long getCount()//返回当前计数
public boolean await(long timeout, TimeUnit unit)throws  InterruptedException 
//等待，当计数减到0时，所有线程并行执行;或超时执行
```
**代码演示**	

```java
public class CountDownLatchDemo implements Runnable {
    static final CountDownLatch end = new CountDownLatch(5);
    static final CountDownLatchDemo demo=new CountDownLatchDemo();
    @Override
    public void run() {
        try {
            //模拟检查任务
            Thread.sleep(new Random().nextInt(10)*1000);
            System.out.println("check complete");
            end.countDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) throws InterruptedException {
        ExecutorService exec = Executors.newFixedThreadPool(10);
        for(int i=0;i<5;i++){
            exec.submit(demo);
        }
        //等待检查
        end.await();
        //发射火箭
        System.out.println("Fire!");
        exec.shutdown();
    }
}
check complete
check complete
check complete
check complete
check complete
Fire!
```
代码解析
>1. 主线程中因end.await()阻塞
>2. 在其他线程中使用end.countDown()使计数器减1
>3. 当计数器为0时,end.await()不再阻塞,继续执行后续代码

<font color=red>使用场景:某些事件必须等待先行事件完成才能进行</font>
## 6.循环栅栏： CyclicBarrier

- CountDownLatch只能完成一次倒计时
- CyclicBarrier能循环倒计时,每次倒计时完成执行barrierAction

**构造函数**
```java
CyclicBarrier(int parties, Runnable barrierAction) 
```
**主要函数**
```java
int await(long timeout, TimeUnit unit) 
等待所有 parties已经在此屏障上调用 await ，或指定的等待时间过去。  
int getNumberWaiting() 
返回目前正在等待障碍的各方的数量
int getParties() 
返回旅行这个障碍所需的聚会数量
boolean isBroken() 
查询这个障碍是否处于破坏状态 
void reset() 
将屏障重置为初始状态
```
**代码演示**

```java
public class CyclicBarrierDemo2 {
    public static class Soldier implements Runnable {
        private String soldier;
        private final CyclicBarrier cyclic;
        Soldier(CyclicBarrier cyclic, String soldierName) {
            this.cyclic = cyclic;
            this.soldier = soldierName;
        }
        public void run() {
            try {
                //等待所有士兵到齐
                cyclic.await();
                doWork();
                //等待所有士兵完成工作
                cyclic.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }
        void doWork() {
            try {
                Thread.sleep(Math.abs(new Random().nextInt()%10000));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(soldier + ":劳动完成");
        }
    }
    public static class BarrierRun implements Runnable {
        boolean flag;
        int N;
        public BarrierRun(boolean flag, int N) {
            this.flag = flag;
            this.N = N;
        }
	public void run() {
            if (flag) {
                System.out.println("司令:[士兵" + N + "个，任务完成！]");
            } else {
                System.out.println("司令:[士兵" + N + "个，集合完毕！]");
                flag = true;
            }
        }
    }
    public static void main(String args[]) throws InterruptedException {
        final int N = 10;
        Thread[] allSoldier=new Thread[N];
        boolean flag = false;
        //设置barrier和barrierAction
        CyclicBarrier cyclic = new CyclicBarrier(N, new BarrierRun(flag, N));
        //设置屏障点，主要是为了执行这个方法
        System.out.println("集合队伍！");
        for (int i = 0; i < N; ++i) {
            System.out.println("士兵 "+i+" 报道！");
            allSoldier[i]=new Thread(new Soldier(cyclic, "士兵 " + i));
            allSoldier[i].start();
            //if(i==5)
            	// allSoldier[0].interrupt();
        }
    }
}
```
代码分析
> 1. 当cyclic.await()的线程数等于parties,越过barrier,执行barrierAction
> 2. CyclicBarrier.await()可能会抛出两种异常
> 1)InterruptedException--中断异常
> 2)<font color=red>BrokenBarrierException</font>--CyclicBarrier特有的异常,表示无法再越过barrier
> 2. 若将注释取消,将获得1个InterruptedException和9个BrokenBarrierException(1个中断导致其他无法越过barrier)

## 7.线程阻塞工具类： LockSupport

**LockSupport的线程阻塞特点**
- 可以在**线程内任意位置**阻塞线程,且unpark可在park前
- 阻塞线程**不需要获取锁**,也不会引发InterruptedException

**主要函数**

```java
static void park(Object blocker); 
禁用当前线程,并设置阻塞对象,该对象会出现在线程Dump中
static void parkNanos(long nanos) 
禁用当前线程进行线程调度，直到指定的等待时间，除非许可证可用。 
static void unpark(Thread thread) 
为给定的线程提供许可证（如果尚未提供）。  
```
**代码演示**

```java
public class LockSupportDemo {
	static ChangeObjectThread t1 = new ChangeObjectThread("t1");
	static ChangeObjectThread t2 = new ChangeObjectThread("t2");
	public static class ChangeObjectThread extends Thread {
		public ChangeObjectThread(String name){
			super.setName(name);
		}
		@Override
		public void run() {
			System.out.println("in "+getName());
			LockSupport.park(this);
			System.out.println("in "+getName()+"i");
			LockSupport.park(this);
			System.out.println("in "+getName()+"o");
		}
	}
	public static void main(String[] args) throws InterruptedException {
		t1.start();
		Thread.sleep(100);
		t2.start();
		LockSupport.unpark(t1);
		Thread.sleep(1000);
		System.out.println("ok");
		LockSupport.unpark(t1);
		LockSupport.unpark(t2);
		t1.join();
		t2.join();
	}
}
in t1
in t1i
ok
in t1o
in t2
in t2i
```
代码分析
>1. LockSupport是基于互斥量机制,park消费,unpark生产(<font color=red>start之后</font>)
>2. LockSupport会为<font color='cornflowerblue'>每个线程</font>维护一个互斥量
>3.  LockSupport是<font color=red>不可重入锁</font>,多个park()需要多次获取互斥量
## 8. Guava 和 RateLimiter 限流

**最简单的限流算法**
限制单位时间的请求数目,当请求超过门限时， 余下的请求丢弃或者等待。

><font color=red>边界请求问题</font>:本单位时间后半部分与下一个单位时间前半部分请求集中,超过了门限,但是在各自单位时间未超过门限.
### **两种常见限流算法**

**1. 漏桶算法**

利用一个缓存区， 当有请求进入系统时， 先在缓存区内保存， 然后以固定的流速流出缓存进行处理

>**两个重要参数**
>- 漏桶容积
>- 流出速率

![漏桶算法](https://img-blog.csdnimg.cn/20191022085339417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70#pic_center)
**2. 令牌桶算法**
桶中存放的不再是请求， 而是令牌。 处理程序只有拿到令牌后， 才能对请求进行处理。 
为了限制流速， 该算法在每个单位时间产生一定量的令牌存入桶中。

>**两个主要参数**
>- 令牌产生速率
>- 令牌桶容量

![令牌桶算法](https://img-blog.csdnimg.cn/20191022090404142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70#pic_center)
### RateLimiter(令牌桶算法)

**构造方法**
```java
public static RateLimiter create(double permitsPerSecond)
静态工厂方法,每隔一段时间产生一个令牌(令牌的寿命默认1s)
```
**主要函数**

```java
public double acquire(int permits)
阻塞线程,直到获取令牌,并返回获取令牌所消耗的时间
public boolean tryAcquire(int permits, long timeout, TimeUnit unit)
阻塞线程,直到获得令牌,超时或被中断
public final void setRate(double permitsPerSecond)
设置令牌产生速率
```
**代码演示**
```java
import com.google.common.util.concurrent.RateLimiter;
public class RateLimiterDemo {
	static RateLimiter limiter = RateLimiter.create(2);
	public static class Task implements Runnable {
		@Override
		public void run() {
			System.out.println(System.currentTimeMillis());
		}
	}
	public static void main(String args[]) throws InterruptedException {
		for (int i = 0; i < 50; i++) {
			limiter.acquire();
			new Thread(new Task()).start();
		}
	}
}
```
代码分析
>1. 使用RateLimiter.create(2)设置每2秒产生一个令牌
>2. limiter.acquire()未获得令牌前会阻塞,获得令牌后,继续执行

## 问题记录

[CountDownLatch的实现](https://zhuanlan.zhihu.com/p/60884042)

**线程的生命周期**
>线程在创建后，通过start执行了run方法后，会被自动回收。

**复用线程的原因**
- 线程频繁创建和关闭花费大量时间
- 线程本身占用内存，创建过多线程可能导致OOM

# 线程复用

## 1. 线程池

线程池中存放空闲线程,每当有任务执行,**不再创建线程而是从线程池中获取**,任务执行完后,将线程归还给线程池.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191121201509185.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70#pic_center)

<center>图片来源https://blog.csdn.net/Growing_stu/article/details/84144808</center>

### 1) JDK对线程池的支持

**ThreadPoolExecutor 类的构造函数**

```java
public ThreadPoolExecutor(int corePoolSize,//核心线程数
						int maximumPoo1Sizer//最大线程数
						long keepAliveTime,//非核心线程数保活时间
						TimeUnit unit,//时间单位
						BlockingQueue<Runnable> workQueue,//被提交但未执行任务的队列
						ThreadFactory threadFactory,//线程工厂
						RejectedExecutionHandler handler)//拒绝策略
```
**线程池执行流程**
>1. 当前任务数< corePoolSize，将使用已有的线程执行任务。
>2. 当前任务数>= corePoolSize，新提交任务将被放入workQueue中，等待线程池中任务调度执行
>3. 当workQueue已满,且corePoolSize<MaxmumPoolSize,<font color=red>新提交任务会创建新线程执行任务</font>
>4. 当工作线程= maximumPoolSize时，且workQueue已满,新提交任务由RejectedExecutionHandler处理

![任务调度](https://img-blog.csdnimg.cn/20191024083641819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)

### 2) 任务队列

1. 直接提交队列(SynchronousQueue)
>SynchronousQueue没有容量
>每一个插入需要等待对应的删除操作,反之亦然
>提交的任务不会保存在队列,而是准备直接提交给线程

2. 有界的任务队列(ArrayBlockingQueue)
>`public ArrayBlockingQueue(int capacity)`
>当任务队列已满,则准备将任务提交给线程

3. 无界的任务队列(LinkedBlockingQueue )
> 任务会直接进入任务队列直到线程池中有空闲的线程

4. 优先任务队列(PriorityBlockingQueue)
>`public PriorityBlockingQueue(int initialCapacity,Comparator<? super E> comparator`
>无界的任务队列,并会对入队的任务按照比较器排序

### 3) 拒绝策略

当**任务队列已满**且**线程数=maximumPoolSize**，再有新的任务到达,就要用到拒绝策略

**JDK内置拒绝策略**
1. AbortPolicy 策略(<font color='red'>默认</font>)： 该策略策略会直接抛出异常， 阻止系统正常工作
2. DiscardPolicy 策略： 该策略默默地丢弃无法处理的任务， 不予任何处理。
3. CallerRunsPolicy 策略： 只要线程池未关闭， 该策略直接在调用者线程中， 运行当前被丢弃的任务。 
4. DiscardOldestPolicy 策略： 读策略将丢弃最老的一个请求， 并尝试再次提交当前任务。

**自定义拒绝策略**
拒绝策略都需要实现**RejectedExecutionHandler**接口,并实现**rejectedExecution()**方法

```java
public class RejectThreadPoolDemo {
	public static class MyTask implements Runnable {
		@Override
		public void run() {
			System.out.println(System.currentTimeMillis() + ":Thread ID:"
					+ Thread.currentThread().getId());
			try {
				Thread.sleep(10000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
	public static void main(String[] args) throws InterruptedException {
		MyTask task = new MyTask();
		ExecutorService es = new ThreadPoolExecutor(5, 5,
                0L, TimeUnit.MILLISECONDS,
                new LinkedBlockingQueue<Runnable>(5),
                Executors.defaultThreadFactory(),
                new RejectedExecutionHandler(){
					@Override
                    /**
                    * 默认丢弃新任务r
                    * r 新任务
                    * executor 线程池
                    * @return
                    */
					public void rejectedExecution(Runnable r,
							ThreadPoolExecutor executor) {
						System.out.println(r.toString()+" is discard");
					}
		});
		for (int i = 0; i < Integer.MAX_VALUE; i++) {
			es.submit(task);
			Thread.sleep(10);
		}
	}
}
1571878291492:Thread ID:19
1571878291502:Thread ID:20
1571878291512:Thread ID:21
1571878291522:Thread ID:22
1571878291532:Thread ID:23
java.util.concurrent.FutureTask@33909752 is discard
java.util.concurrent.FutureTask@55f96302 is discard
```
*代码分析*
>1. 当任务队列已满,且线程数=maximumPoolSize,再有任务提交时,会执行拒绝策略
>2.  LinkedBlockingQueue也可以指定任务队列大小,但一般使用ArrayBlockingQueue
### 4) 线程工厂

线程池中的线程由线程工厂创建.当线程池需要创建线程时,会执行ThreadFactory以下方法
```java
Thread newThread(Runnable r);
```
**自定义线程工厂**:设置线程的名称,组,是否守护,优先级等信息

```java
public class TFThreadPoolDemo {
	public static class MyTask implements Runnable {
		@Override
		public void run() {
			System.out.println(System.currentTimeMillis() + ":Thread Name:"
					+ Thread.currentThread().getName());
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
	public static void main(String[] args) throws InterruptedException {
		MyTask task = new MyTask();
		ExecutorService es = new ThreadPoolExecutor(5, 5,
                0L, TimeUnit.MILLISECONDS,
                new SynchronousQueue<Runnable>(),
                new ThreadFactory(){
					@Override
					public Thread newThread(Runnable r) {
						Thread t= new Thread(r);
						t.setDaemon(true);//设置为守护进程
						t.setName(System.currentTimeMillis()+"");
						return t;
					}
				}
               );
		for (int i = 0; i < 5; i++) {
			es.submit(task);
		}
		Thread.sleep(2000);
	}
}
1571879131576:Thread Name:1571879131575
1571879131577:Thread Name:1571879131575
1571879131577:Thread Name:1571879131576
1571879131576:Thread Name:1571879131576
1571879131576:Thread Name:1571879131575
```
代码分析
>1. 通过自定义线程工厂,将创建线程的时间作为线程名,并将线程设置为守护线程
>2. 在线程执行时,打印当前时间和线程名中的时间,可以分析线程调度消耗的时间

**Executors工厂方法**

1. 固定大小的线程池

```java
public static ExecutorService newFixedThreadPool(int nThreads)
核心线程数和最大线程数都为nThreads,任务队列为LinkedBloddngQueue.
public static ExecutorService newSingleThreadExecutor()
核心线程数和最大线程数都为1,任务队列为LinkedBloddngQueue.
public static ExecutorService newCachedThreadPool()
核心线程数为0,最大线程数都为max,任务队列为SynchronousQueue
```
2. 计划任务线程池

```java
public static ScheduledExecutorService newSingleThreadScheduledExecutor()
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)
```
计划任务调度方法
```java
public ScheduledFuture<?> schedule(Runnable command,long delay, TimeUnit unit);
public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
											long initialDelay,
											long period,
											TimeUnit unit);
public ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,
											long initialDelay,
											long delay,
											TimeUnit unit);
```
- ScheduledExecutorService不会立即安排任务,而是按照**计划时间安排任务**
- schedule只对任务调度一次,其他2个方法对任务进行周期调度
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191024083341615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70#pic_center)
- 周期地调度不会让任务堆叠执行.如果周期太短,任务会在上次任务结束后立即调度
## 2. 扩展线程池

ThreadPoolExecutor 是一个可以扩展的线程池
```java
任务执行前运行
protected void beforeExecute(Thread t, Runnable r)
任务结束后运行
protected void afterExecute(Runnable r, Throwable t)
线程池退出时运行
protected void terminated()
```
[**源码分析**](https://www.jianshu.com/p/8089ceb980ab)
在ThreadPoolExecutor.Worker.runTask()中

```java
final void runWorker(Worker w) {
    Thread wt = Thread.currentThread();
    Runnable task = w.firstTask;
    w.firstTask = null;
    w.unlock(); // allow interrupts
    boolean completedAbruptly = true;
    try {
        while (task != null || (task = getTask()) != null) {
            w.lock();
            // If pool is stopping, ensure thread is interrupted;
            // if not, ensure thread is not interrupted.  This
            // requires a recheck in second case to deal with
            // shutdownNow race while clearing interrupt
            if ((runStateAtLeast(ctl.get(), STOP) ||
                 (Thread.interrupted() &&
                  runStateAtLeast(ctl.get(), STOP))) &&
                !wt.isInterrupted())
                wt.interrupt();
            try {
                beforeExecute(wt, task);//运行前
                Throwable thrown = null;
                try {
                    task.run();//运行任务，在worker.start()中运行task.run()
                } catch (RuntimeException x) {
                    thrown = x; throw x;
                } catch (Error x) {
                    thrown = x; throw x;
                } catch (Throwable x) {
                    thrown = x; throw new Error(x);
                } finally {
                    afterExecute(task, thrown);//运行结束后
                }
            } finally {
                task = null;
                w.completedTasks++;
                w.unlock();
            }
        }
        completedAbruptly = false;
    } finally {
        processWorkerExit(w, completedAbruptly);
    }
}
```
>1. beforeExecute会在任务执行前运行,包含<font color=red>工作线程</font>和<font color=red>工作任务</font>两个参数
>2. afterExecute会在任务执行后运行,尽管任务抛出异常.包含<font color=red>工作任务</font>和<font color=red>异常信息</font>两个参数
## 3. 优化线程池数量

线程池的大小对系统的性能有一定的影响。 过大或者过小的线程数量都无法发挥最优的系统性能
$$Ncpu = CPU 的数量$$

```java
获取cpu数量：Runtime.getRuntime().availableProcessors()
```

$$Ucpu =目标 CPU 的使用率， 0≤Ucpu≤1$$
$$W/C= 等待时间与计算时间的比率$$
**最优线程池大小**
$$Nthreads = Ncpu×Ucpu ×(1 + W/C)$$

## 4. 在线程池中寻找堆栈

[**线程池中的幽灵般错误**](https://mp.weixin.qq.com/s/mcg5elKl7e2-9DV0jsDQag)

```java
class DivTask implements Runnable {
    int a,b;
    public DivTask(int a,int b){
        this.a=a;
        this.b=b;
    }
    @Override
    public void run() {
        double re=a/b;
        System.out.println(re);
    }
}
public class TraceMain {
	public static void main(String[] args) {
		ThreadPoolExecutor pools=new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                0L, TimeUnit.SECONDS,
                new SynchronousQueue<Runnable>());
		for(int i=0;i<5;i++){
			pools.submit(new DivTask(100,i));
		}
	}
}
100.0
25.0
33.0
50.0
```
代码分析
> 1. 线程池执行了一个除法运算任务,但除以0并为抛出异常,而是直接跳过

获取异常信息
1. 将submit()改为execute()
2. 改造submit()方法

	```java
	Future retools.submit(new DivTask(100,i));
	re.get();
	//Future会将异常信息封装在outcome中，在get中才会再次抛出
	```
```

异常信息

​```java
	Exception in thread "pool-1-thread-1" java.lang.ArithmeticException: / by zero
	at geym.conc.ch3.trace.DivTask.run(DivTask.java:11)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
```
&emsp;<font color=red>错误信息包含了出现异常的任务,但是没有提示任务提交的位置</font>

**保存任务提交的堆栈信息**

```java
public class TraceThreadPoolExecutor extends ThreadPoolExecutor {
	public TraceThreadPoolExecutor(int corePoolSize, int maximumPoolSize,
			long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) {
		super(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue);
	}
	@Override
	public void execute(Runnable task) {
		super.execute(wrap(task, clientTrace(), Thread.currentThread()
				.getName()));
	}
	@Override
	public Future<?> submit(Runnable task) {
		return super.submit(wrap(task, clientTrace(), Thread.currentThread()
				.getName()));
	}
	private Exception clientTrace() {
		return new Exception("Client stack trace");
	}
	private Runnable wrap(final Runnable task, final Exception clientStack,
			String clientThreadName) {
		return new Runnable() {
			@Override
			public void run() {
				try {
					task.run();
				} catch (Exception e) {
					clientStack.printStackTrace();
					throw e;
				}
			}
		};
	}
}
```
代码解析
>1. 在**TraceThreadPoolExecutor**中重写了execute和submit方法
>2. 提交的任务被wrap成新的Runnable,若抛出异常
>&emsp;通过clientStack先打印任务提交位置的堆栈信息,再将任务异常抛出
>&emsp;捕获任务异常,打印任务异常堆栈信息

打印任务提交的堆栈信息

```java
public class TraceMain {
	public static void main(String[] args) {
		ThreadPoolExecutor pools=new TraceThreadPoolExecutor(0, Integer.MAX_VALUE,
                0L, TimeUnit.SECONDS,
                new SynchronousQueue<Runnable>());
		for(int i=0;i<5;i++){
			pools.execute(new DivTask(100,i));
		}
	}
}
```
异常信息
```java
java.lang.Exception: Client stack trace
	at geym.conc.ch3.trace.TraceThreadPoolExecutor.clientTrace(TraceThreadPoolExecutor.java:27)
	at geym.conc.ch3.trace.TraceThreadPoolExecutor.execute(TraceThreadPoolExecutor.java:16)
	at geym.conc.ch3.trace.TraceMain.main(TraceMain.java:14)
Exception in thread "pool-1-thread-1" java.lang.ArithmeticException: / by zero
	at geym.conc.ch3.trace.DivTask.run(DivTask.java:11)
	at geym.conc.ch3.trace.TraceThreadPoolExecutor$1.run(TraceThreadPoolExecutor.java:36)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
```
&emsp;堆栈信息包含了任务提交时的位置信息
## 5.分而治之:Fork/Join框架

<img src="https://img-blog.csdnimg.cn/20191024105904340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom: 80%;" />
[ForkJoinPool原理一](https://www.jianshu.com/p/de025df55363)
[ForkJoinPool原理二](https://www.cnblogs.com/liquan/p/9495229.html)
[任务窃取算法](https://blog.csdn.net/a724888/article/details/72625224)
**ForkJoinTask**

- ForkJoinTask实现了Future接口,可通过get()获取线程的状态.
- compute()方法在RecursiveAction子类没有返回值,RecursiveTack子类有返回值

**任务分割**
ForkJoinPool主要用来使用分治法(Divide-and-Conquer Algorithm)来解决问题
![Fork/Join执行逻辑](https://img-blog.csdnimg.cn/20191024113003250.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70#pic_center)
**线程互助**
每个物理线程实际上是需要处理多个逻辑任务的。 每个线程必然需要拥有一个任务队列
当一个线程试图“ 帮助” 其他线程时， 总是从任务队列的底部开始获取数据， 而线程试图执行自己的任务时， 则是从相反的顶部开始获取数据。
![互相帮助的线程](https://img-blog.csdnimg.cn/20191024113054989.png#pic_center)
**ForkJoinPool示例**

使用ForkJoinPool分布式计算列表和（类似MapReduce）

```java
public class CountTask extends RecursiveTask<Long>{
    private static final int THRESHOLD = 10000;
    private long start;
    private long end;
    public CountTask(long start,long end){
        this.start=start;
        this.end=end;
    }
    public Long compute(){
        long sum=0;
        boolean canCompute = (end-start)<THRESHOLD;
        if(canCompute){//如果任务规模小,直接进行
            for(long i=start;i<=end;i++){
                sum +=i;
            }
        }else{//如果任务规模大,分解成多个子任务
            //分成10个小任务
            long step=(end-start)/10;
            ArrayList<CountTask> subTasks=new ArrayList<CountTask>();
            long pos=start;
            for(int i=0;i<10;i++){
                long lastOne=pos+step;
                if(lastOne>end)lastOne=end;
                CountTask subTask=new CountTask(pos,lastOne);//创建子任务
                pos+=step+1;
                subTasks.add(subTask);//记录子任务
                subTask.fork();//提交子任务列表
            }
            for(CountTask  t:subTasks){
                sum+=t.join();//获取子任务返回值
            }
        }
        return sum;//返回计算值
    }
    public static void main(String[]args){
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        CountTask task = new CountTask(0,2000L);//创建任务
        ForkJoinTask<Long> result = forkJoinPool.submit(task);//提交任务
        try{
            long res = result.get();
            System.out.println("sum="+res);
        }catch(InterruptedException e){
            e.printStackTrace();
        }catch(ExecutionException e){
            e.printStackTrace();
        }
    }
}
```
**fork()**
```java
public final ForkJoinTask<V> fork() 
```
- 将任务提交到工程线程的工作队列
- 线程的工作队列为双端队列
&emsp;自身的任务:LIFO(队尾添加,队尾取用)
&emsp;偷窃的任务:FIFO(队头添加,队尾取用)

**join()**

```java
public final V join()
```
- 检查调用 join() 的线程是否是 ForkJoinThread 线程。
&emsp; 如果不是，则阻塞当前线程，等待任务完成。
&emsp; 如果是，则不阻塞。

- 查看任务的完成状态，如果已经完成，直接返回结果。

- 如果任务尚未完成，查看每个工作线程的工作队列
&emsp;若工作队列不为空,完成自身任务
&emsp;若工作队列为空,实行[任务窃取算法](https://blog.csdn.net/a724888/article/details/72625224)
直到所有工作都完成.

**代码解析**
>1. forkJoinPool.submit(task)会自动执行task中compute()
>2. compute()将大任务分解成小任务,大任务的返回值依赖于小任务的返回值
>3. compute()的返回值由task.join()获取

## 6. Guava 中对线程池的扩展

Guava并非JDK内置的,其中的DirectExecutor对线程池进行了扩展.
### 特殊的 DirectExecutor 线程池

DirectExecutor 没有真的创建或者使用额外线程， 而是将任务在当前线程中直接执行.

**广义线程池**
 任何一个可以运行 Runnable 实例的模块都可以被视为线程池， 即便它没有真正创建线程
 **DirectExecutor示例**

```java
public class MoreExecutorsDemo {
    public static void main(String[] args) {
        Executor exceutor = MoreExecutors.directExecutor();
        exceutor.execute(() -> System.out.println("I am running in " + Thread.currentThread().getName()));
    }
}
输出:
I am running in main
```
*代码解析*
>1. DirectExecutor将Runnable示例在当前线程执行
>2. 可以使用狭义线程池代替DirectExecutor.实现异步和同步在代码上的统一
### Daemon 线程池

**将普通线程池转换为Daemon线程池**
```java
public static ExecutorService getExitingExecutorService(ThreadPoolExecutor executor)
```
**代码演示**

```java
public class MoreExecutorsDemo2 {
    public static void main(String[] args) {
        ThreadPoolExecutor exceutor = (ThreadPoolExecutor)Executors.newFixedThreadPool(2);
        MoreExecutors.getExitingExecutorService(exceutor);
        exceutor.execute(() -> System.out.println("I am running in " + Thread.currentThread().getName()));
    }
}
输出:
I am running in pool-1-thread-1(程序结束)
```
代码分析
>1. getExitingExecutorService()将线程池转换为Daemon线程池.不会阻止程序退出
>2. 普通的线程池必须调用shutdown才能正常退出.

# 3. JDK并发容器
## 3.1 ConcurrentHashMap:线程安全的HashMap
**Collections.synchronizedMap()**
```java
public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m) {
	return new SynchronizedMap<>(m);
}
```
 SynchronizedMap 内包装了一个 Map,对Map的所有操作都需要获取mutex监视器.
```java
private static class SynchronizedMap<K,V> implements Map<K,V>, Serializable {
    private final Map<K,V> m;     // Backing Map
    final Object      mutex;        // Object on which to synchronize
	...
}
```
**ConcurrentHashMap**

## 3.2 有关 List 的线程安全
**底层由数组实现**.
- Vector:线程安全
- ArrayList:线程不安全

**底层由链表实现**
- LinkedList:线程不安全

```java
public static <T> List<T> synchronizedList(List<T> list) {
	return (list instanceof RandomAccess ?
           new SynchronizedRandomAccessList<>(list) :
           new SynchronizedList<>(list));
}
```
## 3.3 ConcurrentLinkedQueue:高效读写队列
ConcurrentLinkedQueue是并发环境下性能最好的队列.

```java
public class ConcurrentLinkedDeque<E> extends AbstractCollection<E> implements Deque<E>, java.io.Serializable {
	private transient volatile Node<E> head;
	private transient volatile Node<E> tail;
	public E poll()           { return pollFirst(); }
    public E peek()           { return peekFirst(); }
	static final class Node<E> {
        volatile Node<E> prev;
        volatile E item;
        volatile Node<E> next;
        ...
    }
}
```
**保证高并发下的安全性,对Node进行操作时,使用了CAS**

```java
boolean casItem(E cmp, E val) {
	return UNSAFE.compareAndSwapObject(this, itemOffset, cmp, val);
}
void lazySetNext(Node<E> val) {
	UNSAFE,putOrderedObject(this, nextOffset, val);
}
boolean casNext(Node<E> cmp, Node<E> val) (
	return UNSAFE,compareAndSwapObject(this, nextOffset, cmp, val);
}
```
**head**
head表示链表的头部,永远不会为null,可通过head和succ()遍历链表.

```java
final Node<E> succ(Node<E> p) {
    Node<E> q = p.next;
    return (p == q) ? first() : q;
}
```
&emsp;succ()会返回后续节点,或者第一个节点(<font color=red>哨兵</font>)
**tail**
tail一般指向尾节点
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191028205429345.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70#pic_center)

```java
public boolean offer(E e) {
	checkNotNull(e);
	final Node<E> newNode = new Node<E>(e);

	for (Node<E> t = tail, p = t;;) {
		Node<E> q = p.next;
		if (q == null) {
		// P 是最后一个节点
			if (p.casNext(nullf newNode)) {
			//每两次更新一下 tail
				if (p != t)
					casTail(t, newNode);
				return true;
			}
			//CAS 竞争失败， 再次尝试
		}
		else if (p == q)
			//遇到哨兵节点， 从head 开始遍历
			//但如果 tail 被修改， 则使用 tail (因为可能被修改正确了)
			p = (t != (t = tail)) ? t : head;
		 else
			//取下一个节点或者最后一个节点
			p = (p != t & & t != (t = tail)) ? t : q;
	}		
}
```
代码分析
为保证并发环境的安全性,添加元素到队尾时,通过在"死循环"中使用**CAS**添加元素

1. 队列为空时,head=tail,则q=null,p.casNext(null,newNode)将新节点链接到队尾
判断p!=t
&emsp;p不是尾节点,casTail(t,newNode),更新尾节点,返回true.
&emsp;p是尾节点,不需要更新尾节点<font color=red>(尾节点隔次更新)</font>
2. 当前节点为哨兵<font color=red>(next指向自身)</font>,p = (t != (t = tail)) ? t : head;
&emsp;若t不为尾节点<font color=red>(并发更新了尾节点)</font>,将p设置为尾节点
&emsp;若尾节点未改变,将p设置为头结点,从头遍历
3. 其他情况,p = (p != t & & t != (t = tail)) ? t : q;
&emsp;若p为尾节点或t不为尾节点,p=p.next查找下一个节点<font color=red>(哨兵导致从头查找)</font>
&emsp;否则,将p设置为为尾节点
4. 继续循环,保证节点被加入队列

[head和tail不及时更新的设计意图](https://blog.csdn.net/qq_38293564/article/details/80798310)

- 通过增加对volatile变量的读操作来减少了对volatile变量的写操作，而对volatile变量的写操作开销要远远大于读操作，所以入队和出队效率会有所提升

**哨兵的产生**
哨兵:节点的next指向节点本身.一般表示被删除的节点.

```java
ConcurrentLinkedQueue<String> q=new ConcurrentLinkedQueue<String>();
q.add("l");
q.poll();
```
```java
public E poll() {
	restartFromHead:
	for (;;) {
		for (Node<E> h = head, p = h, q;;) {
			E item = p.item;
			if (item != null && p.casItem(item, null)) {
				if (p != h)
					updateHead(h,((q = p.next) != null) ? q : p);
				return item;
			}
			else if ((q = p * next) == null) {
				updateHead(hr p);
				return null;
			}
			else if (p == q)
				continue restartFromHead;
			else
				p = q
		}
	}
}
```
代码分析
 1. 队列中只有一个元素时,tail=head.head的item为空,且head.next不为空,执行p=q(p=p.next)
 2.  第一个元素item不为null,进入第7行
 &emsp;p不是头结点,执行第8行,updateHead(h, p);将p设置为head,h设置为哨兵.

  **CAS利弊分析**
 - **利**:保证线程安全的情况下,相比阻塞,性能飞速提升
 - **弊**:可能存在ABA问题,且程序设计和实现难度增大

## 3.4 高效读取:不变模式下的CopyOnWriteArrayList类
**CopyOnWriteArrayList类**
- 读读不冲突
- 读写不冲突
- 写写需要同步等待

**实现原理**
- 对数据修改时,并不修改原内容.而是对原数据进行一次复制<font color=red>(保证数据一致性,数据要么全改,要么全不改)</font>,
- 将修改的内容写入副本,再用副本替换原数据.

**实现代码**
 - 读取实现
	```java
	private volatile transient Object[] array;
	public E get(int index) {
		return get(getArray(), index);
	}
	final Object[] getArray() {
		return array;
	}
	private E get(Object[] a, int index) {
		return (E) a[index];
	}
	```
	代码分析
 - array有volatile修饰,修改后立即可见
	 - 读取数据没有任何的同步控制和锁操作 	
	
	
2. 写实现
	```java
	public boolean add(E e) {
		final ReentrantLock lock = this.lock;
		lock.lock();
		try {
			Object[] elements = getArray() ;
			int len = elements.length;
			Object[] newElements = Arrays.copyOf(elementsf len + 1);//创建副本
			newElements[len] = e;//修改副本
			setArray(newElements);//副本替代原数据
			return true;
		} finally {
			lock.unlock();
		}
	}
	```

代码分析

- 写入操作使用了锁,但此锁只用于写入控制,不会影响读操作
- 若一个事务中包含2个读操作,可能会出现<font color=red>不可重复读</font>的问题

## 3.5 数据共享通道:BlockingQueue
若线程A与线程B需要通信,且A不需要知道B的存在.可使用BlockQueue作为"信箱"
- A将要传递的信息存在信箱中
- B从信箱中获取信息

**信箱对信息的处理**

 1. 循环监控"信箱",有信息则报告(循环周期难以确定,且容易造成资源浪费)
 2.  服务线程在"信箱"为空时,等待信息到来

BlockingQueue接口有2个常用的**实现类**
1. **ArrayBlockingQueue**(之后重要介绍)
2. LinkedBlockingQueue

**ArrayBlockingQueue底层实现**
```java
final Object[] items;//元素存储数组\
int takeIndex;//队头
int putIndex;//队尾
final ReentrantLock lock;//锁
private final Condijtion notEmpty;//非空条件
private final Condition notFull;//未满条件
```
ArrayBlockingQueue采用items的**循环队列**,takeIndex为队头,putIndex为队尾

**压入元素**

```java
boolean offer(E e)
	若队列已满,返回false
	若队列未满,元素入队,返回true
void put(E e) throws InterruptedException
	若队列已满,则阻塞
	若队列为满,元素入队
```
put()具体实现
```java
public void put(E e) throws InterruptedException {
	checkNotNuli(e);
	final ReentrantLock lock = this.lock;
	lock.locklnterruptibly();
	try {
		while (count == items ,length)
			notFull.await{);//若队列已满,未满等待
		insert(e);
	} finally{
		lock.unlock();
}
private void insert(E x) {
	items[putIndex] = x;
	putIndex = inc(putlndex);
	++count;
	notEmpty,signal();//唤醒非空等待的线程
}
```
**弹出元素**
```java
E poll(long timeout, TimeUnit unit) throws InterruptedException
	有限时间等待
E take() throws InterruptedException
	永久等待,除非被中断
```
take()具体实现

```java
public E take{) throws InterruptedException (
	final ReentrantLock lock = this.lock;
	lock.locklnterruptibly();
	try {
		while (count==0)
			notEmpty.await(};//若队列为空,则非空等待
		return extract();
	} finally {
		lock.unlock();
	}
}
private E extract() {
	final Object[] items = this.items;
	E x = this.<E>cast(items[takelndex]);
	items[takeIndex]=null;
	takelndex = inc(takelndex);
	--count;
	notFull.signal();//唤醒未满等待的线程
	return x;
}
```
## 随机数据结构:跳表
**跳表数据结构**
![跳表](https://img-blog.csdnimg.cn/20191029152432803.png#pic_center)
- 跳表中元素是有序的
- 跳表中高层是底层的子集

**跳表与平衡树**
- 平衡树和跳表都能实现**快速查找**,O(logN)
- 平衡树中元素的插入和删除需要全局调整
跳表中元素的插入和删除只需局部调整

**跳表与hash表**
- 都用于实现Map
- hash表不会保存元素的顺序
跳表中元素是有序的

**跳表元素查找过程**
![跳表查找](https://img-blog.csdnimg.cn/20191029153409796.png#pic_center)
1. 在本层找到不大于target的key
2. 从下一层的key开始查找
3. 重复1,2直至找到key,或失败
**跳表的实现:ConcurrentSkipListMap**

```java
public class ConcurrentSkipListMap<K,V> extends AbstractMap<K,V>
    implements ConcurrentNavigableMap<K,V>, Cloneable, Serializable {
	private transient volatile HeadIndex<K,V> head;
	
	static final class HeadIndex<K,V> extends Index<K,V> {
        final int level;//记录层数
        HeadIndex(Node<K,V> node, Index<K,V> down, Index<K,V> right, int level) {
            super(node, down, right);
            this.level = level;
        }
    }
    
    static class Index<K,V> {
        final Node<K,V> node;
        final Index<K,V> down;
        volatile Index<K,V> right;
    }
    
	static final class Node<K,V> {
		final K key;
        volatile Object value;
        volatile Node<K,V> next;
        boolean casValue(Object cmp, Object val){...}
        boolean casNext(Node<K,V> cmp, Node<K,V> val){...}
       }
}
```
## 问题记录

跳表中index数据结构中down不需要volatile修饰？

> 因为down节点引用的节点不会被修改（地址不修改，内容可能修改）

# 4. JMH性能测试

**性能测试的原因**

1. 部分并发程序是由串行程序改造而来,需要比较两种算法的性能
2. 由于业务原因引入多线程,多线程并发控制导致性能损耗,评估损耗比重是否能够接受.

## 4.1 JMH

JMH ( Java Microbenchmark Harness ) 是一个在 OpenJDK 项目中发布的， 专门用于性能
测试的框架， 其精度可以到达**毫秒级**.

## 4.2 JMH的基本概念与配置
**模式(Mode)**

1. Throughput： 	  **整体吞吐量**， 表示 1 秒内可以执行多少次调用。
2. AverageTime： **调用的平均时间**， 指每一次调用所需要的时间。
3. SampleTime： **随机取样**， 最后输出取样结果的分布， 例如“ 99%的调用在 xxx 毫秒” 。
4. SingleShotTime： **只运行一次**。 同时把 warmup 次数设为 0, 用于测试冷启动时的性能(不预热)。

**迭代(Iteration)**
&emsp;JMH的一次**测试单位**,一次迭代为1s,期间不断调用被测方法,并采样计算吞吐量,平均时间等参数.

**预热(Warmup)**
- 由于JVM中JIT的存在,同一方法在JIT编译前后时间不同.
- 预热代码,使代码得到充分JIT编译,通常只考虑方法在JIT后的性能

**状态(State)**

通过State可指定对象的作用范围

1. 线程范围(**Thread**):为每个线程生成一个对象
2. 基准测试范围(**Benchmark**):多个线程共享一个实例

**配置类(Options/OptionsBuilder)**

测试前对测试参数配置

 1. 指定测试类(include)
 2.  使用进程个数(fork)
 3. 预热迭代次数(warmupIterations)
	```java
	Options opt = new OptionsBuilder()
		.include(JMHSample_01_HelloWorld.class.getSimpleName())
		.forks(1).build();
	new Runner(opt)•run();
	```