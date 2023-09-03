# 并发编程的三个必要因素
- 原子性
- 可见性
- 有序性
# 进程和线程
## 进程
- 操作系统资源分配的基本单位
- 独立的代码和数据空间
- 进程崩溃，不会对其他进程产生影响
- 有程序运行对入口、顺序执行序列和程序出口
## 线程
- CPU任务调度和执行的基本单位
- 共享进程的代码和数据空间，有独立的运行栈和PC
- 线程崩溃，可能导致整个进程挂掉
- 不能独立执行，必须依存于进程
# 线程死锁
## 定义
- 两个或以上线程在执行过程中，由于资源竞争导致彼此阻塞
## 形成死锁的四个必要条件
- 互斥：某种资源只能由一个线程占用，其他线程必须等待
- 占有且等待：线程已经占用至少一个资源，又提出了新的资源请求，新的资源被其他的线程占用，请求阻塞，但又不放弃自身已占用的资源
- 不可抢占：不能抢夺已被其他线程占用的资源
- 循环等待：两个或以上的线程形成环状的资源等待关系
## 如何避免死锁
- 避免一个线程同时获取多个锁
- 避免一个线程的锁同时占用多个资源
- 尝试使用定时锁，lock.tryLock(timeout)替代内部的锁机制
# 创建线程的方式
- extends Thread
- implements Runnable
- implements Callable<T>
- 匿名内部类 new Thread{}.start()
## Runnable和Callable的区别
- 都采用Thread.start()启动线程
- Runnable接口的run方法没有返回值
- Callable接口的call方法有返回值
- Runnable向线程池提交任务采用execute()
- Callable向线程池提交任务采用submit()
## run和start的区别
- run用来执行线程操作，线程直接执行，和顺序执行一样
- start用来启动线程，线程就绪
- run可以重复调用，start只能调用一次
- run属于本线程的方法，不属于多线程
- 多线程下调用start来执行
## Future接口
- FutureTask类是Future接口的唯一实现类
- future.isDone判断线程是否执行完成
- future.get获取线程的执行结果
## Callable和Future搭配
```java
public static void main(String[] args) {
    // 初始化线程池
    ExecutorService threadPoll = Executors.newFixedThreadPool(5);
    // 初始化主线程任务
    MyTask myTask = new MyTask();
    // 向线程池提交任务
    Future<Integer> future = threadPool.submit(myTask);
    future.get();
    future.isDone();
}
static class MyTask implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // 任务执行
        return integer;
    }
}
```
## Callable和FutureTask搭配
```java
public static void main(String[] args) {
    // 初始化线程池
    ExecutorService threadPool = Executors.newFixedThreadPool(5);
    // 初始化主线程任务
    MyTask myTask = new MyTask();
    // 初始化FutureTask对象
    FutureTask<Integer> futureTask = new FutureTask<>(myTask);
    // 向线程池提交任务
    threadPool.submit(futureTask);
    futureTask.get();
    futureTask.isDone();
}
static class MyTask implements Callable<Integer> {
    @Override
    public Ingeter call() throws Exception {
        // 任务执行
        return integer;
    }
}
```
# 线程的状态
## 新建
```java
Thread thread = new Thread(...);
```
## 就绪
```java
thread.start();
```
## 运行
- 就绪状态的线程获得了CPU时间片
- 就绪状态是运行状态的唯一入口
## 阻塞
- 运行状态的线程放弃CPU的使用权
### 等待阻塞
- 线程执行wait方法，JVM会将线程放入等待队列
- 其他线程notify/notifyAll，JVM将线程放入锁池
- 释放锁
### 同步阻塞
- 线程获取synchronized同步锁失败，JVM会将线程放入锁池
- 拿到所标识转到就绪状态
### 其他阻塞
- sleep
- join
- I/O请求
- 不会释放锁
- 直接转到就绪状态
## 结束
- run、main方法执行结束
- 因异常退出run方法
- 结束的线程不能复生
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/13/17172bab4672a149~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.awebp)
# 线程调度和同步的方法
## wait
- Object类方法
- 释放锁
- 线程间通信
- 调用后需要被其他线程调用notify
## sleep
- Thread类方法
- 不释放锁
- 执行完成后，自动苏醒
## notify
- 唤醒处于等待状态的线程，不确定唤醒哪个线程，由JVM决定唤醒哪个线程
## notifyAll
- 唤醒所有处于等待状态的线程，线程竞争，获得锁的线程才能进入就绪状态
## join
- Thread类方法
- 调用哪个线程的join，哪个线程执行，当前线程阻塞
- 用于让多个线程顺序执行
```java
public static void main(String[] args) {
    Thread t1 = new Thread(new MyTask(null));
    Thread t2 = new Thread(new MyTask(t1));
    Thread t3 = new Thread(new MyTask(t2));
    t1.start();
    t2.start();
    t3.start();
}
static class MyTask implements Runnable {
    private Thread thread;
    public MyTask(Thread thread) {
        this.thread = thread;
    }
    @Override
    public void run() {
        if (null != thread) {
            try {
                thread.join();
                // 执行当前线程任务
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        } else {
            // 执行当前线程任务
        }
    }
}
```
## yield
- 使当前线程从运行状态转为就绪状态
# 指令重排
- 处理器对代码优化
- 不保证代码执行顺序和编写顺序一致，但保证最终执行结果和顺序执行的结果一致
# synchronized
- java关键字
- 用来控制线程同步
- 修饰实例方法：对对象实例加锁
- 修饰静态方法：对类加锁
- 修饰代码块：对指定对象加锁
## 实现原理
- 每个对象有一个监视器锁monitor
- monitor的进入数为0，则线程进入monitor，monitor进入数为1
- 线程重新进入，monitor进入数+1
- 线程释放锁，monitor进入数-1
- 其他线程等待对象的monitor进入数为0
## 锁升级原理
- 锁对象的对象头有一个threadid字段
- 第一次访问时，threadid为空，直接获取偏向锁，并将threadid设置为当前线程id
- 再次访问，判断threadid是否一致，一致则直接使用，不一致，则升级为轻量级锁，CAS自旋
- 自旋一定次数后，还没拿到锁对象，则升级为重量级锁synchronized，JVM阻塞获取锁失败的线程
# volatile
- 修饰变量
- 对象可见
- 禁止指令重排
- 和CAS结合使用，保证原子性
# synchronized和Lock的区别
- synchronized是java关键字，Lock是java类
- synchronized可以给类、方法、代码块加锁，Lock只能给代码块加锁
- synchronized不需要手动获取锁和释放锁，发生异常会自动释放锁
- Lock需要lock.lock获取锁，lock.unLock释放锁，可能导致死锁
- Lock可以知道是否成功获取锁，synchronized无法获取
# synchronized和ReentrantLock的区别
- synchronized是java关键字，ReentrantLock是java类
- 都是可重入锁
- 区别和上面的相似
- 锁的机制不同，ReentrantLock底层调用的是Unsafe的park方法进行加锁，synchronized操作的是对象头的markword
# CAS
- compare and swap
- ABA问题：加一个版本号解决问题
- 循环时间长开销大
- 只能保证一个共享变量的原子性
# 线程池
## 定义
- 并发框架
## 作用
- 减少资源消耗，减少线程的创建、销毁的开销
- 提高响应速度
- 提高线程的可管理性
## ThreadPoolExecutor类(不推荐直接使用来创建线程池)
- 线程池类
### 构造函数参数
- corePoolSize：核心线程数
- maximumPoolSize：最大线程数
- keepAliveTime：线程保存时间
- unit：时间单位
- workQueue：阻塞队列
- threadFactory：线程工厂类
- handler：拒绝策略
## Executors类(线程池创建方式推荐)
- Executros类方法，创建线程池
- newFixedThreadPool：创建一个定长线程池，可控制最大线程数，超出的线程在阻塞队列中等待
- newSingleThreadExecutor：创建一个单线程化的线程池，只有一个线程，线程异常结束，会创建新的线程替代
- newCachedThreadPool：创建一个可缓存池，缓存池的长度无限制
- newScheduledThreadPool：创建一个固定长度的线程池，定时或周期性的执行任务
## Executor、ExecutorService
- ExecutorService接口实现了Executor接口
- ExecutorService接口提供了获取线程任务的状态和返回值的方法
## 线程池的状态
- Running：接收新任务，处理阻塞队列中的任务
- Shutdown：不接受新任务，处理阻塞队列中的任务
- Stop：不接受新任务，不处理阻塞队列中的任务，中断正在执行的线程
- Tidying：销毁所有任务，workCount = 0，执行钩子方法terminated
- Terminated：terminated执行结束后的状态
## execute和submit方法的区别
- 都开启线程池中的任务
- execute只能接收Runnable类型的任务
- submit可以接收Runnable和Callable类型的任务
- execute没有返回值
- submit可以返回Future类型对象
# CopyOnWriteArrayList
## CopyOnWrite定义
- 写时复制容器
- 读写分离
- 写入前，复制一份，并发读，写完后，将原容器引用指向新的容器
## 原理
- add时，加锁，复制，添加，改变指向
```java
public boolean add(E e) {
    synchronized (lock) {
        Object[] es = getArray();
        int len = es.length;
        es = Arrays.copyOf(es, len + 1);
        es[len] = e;
        setArray(es);
        return true;
    }
}
```
## 缺点
- 内存占用：可能导致GC或Full GC
- 数据一致性：不能保证实时一致性，只能保证最终一致性
# 并发工具类
## CountDownLatch
-  实现计数器功能
-  多个线程按顺序执行
## CyclicBarrier
- 规定多少个线程在等待后，所有等待状态的线程都被唤醒
## Semaphore
- synchronized的加强版
- 可以控制线程的并发数量
# ThreadLocal
## 定义
- 本地线程变量
- 线程私有
## 原理
- ThreadLocal类有一个ThreadLoaclMap静态内部类
- 每个线程有一个ThreadLocalMap对象，每个ThreadLocalMap对象可以有多个ThreadLocal对象的key
- ThreadLocalMap类有一个Entry静态内部类，继承了弱引用，使用ThreadLocal作为key，value是我们设置的value
- 线程操作ThreadLocal都是对ThreadLocalMap进行操作
- set方法和get方法，都是先获取当前线程，再从ThreadLocal中获取ThreadLocalMap对象
## 内存泄漏问题
- ThreadLocal为null，ThreadLocalMap的key为null，value还在，内存泄漏
## 解决办法
- 使用完ThreadLocal后，调用ThreadLocal的remove方法，清除整个ThreadLocalMap对应的value