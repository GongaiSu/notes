# JUC

## 共享模型之管程

### 问题

* 同一个共享变量，由于多个线程进行操作会导致最终的结果与预想的值不一致。导致这个原因是，线程之间的切换会导致结果不一致。

### 解决方案

1. 阻塞式的解决方案：synchronized,Lock
2. 非阻塞式的解决方案：原子变量

### synchronized解决方案

* 

* 基本用法

  ```java
  synchronized(对象){
  }
  ```

* 不同的用法

  ```java
  //锁在方法上
  class Test{
      public synchronized void test(){
          
      }
  }
  //等价于
  class Test{
     public  void test(){
         synchronized(this){
             
         }
     }
  }
  ```

  ```java
  //锁在静态方法上
  class Test{
      public synchronized static void test(){
          
      }
  }
  //等价于
  class Test{
     public  void test(){
         synchronized(Test.class){
             
         }
     }
  }
  ```

### 常见的线程安全类

* String
* Integer
* StringBuffer
* Random
* Vector
* Hashtable
* java.util.concurrent包下的类
* 这里的线程安全指的是，多线程调用它们同一个实例的某个方法时，是线程安全的。
  * 它们的每个方法是原子的
  * 但注意它们的组合不是原子的

#### 不可变类线程安全性

* String、Integer等 都是不可变类，因为其内部的状态不可以改变，因此它们的方法都是线程安全的

### Monitor概念

#### Java对象头

以32为虚拟机为例

普通对象

![image-20220830173001299](并发编程(JUC).assets/image-20220830173001299.png)

* Klass Word：是一个指针，指向了当前对象从属的class文件找到当前对象的类型

Mark Work信息

![image-20220830173948086](并发编程(JUC).assets/image-20220830173948086.png)

![image-20220901100503649](并发编程(JUC).assets/image-20220901100503649.png)

数组对象

![image-20220830174141514](并发编程(JUC).assets/image-20220830174141514.png)

####  Monitor(锁)

Monitor被翻译成监视器或管程

每个Java对象都可以管理一个Monitor对象，如果使用synchronized给对象上锁（重量级）之后，该对象头中的Mark Word中就被设置指向Monitor对象指针

Monitor结构

![image-20220831152338871](并发编程(JUC).assets/image-20220831152338871.png)

* 刚开始Monitor中Owner为null

* 当Thread-2指向synchronized(obj)就会将Monitor的所有折Owner置为Thread-2，Monitor中只能有一个Owear

* 在Thread-2上锁的过程中，如果有Thread-3，Thread-4，Thread-5，也来执行synchronized(obj)，就会进入EntryList中进行BLOCKED

* 当Thread-2上执行完上锁的代码块后，释放锁，Owner为null，则会唤醒EntryList中等待的线程进行锁的竞争，竞争过程是非公平的。

  **注意：**

  * synchronized必须是进入同一个对象的monitor才有上述的效果
  * 不加synchronized的对象不会关联监视器，不遵从以上规则

### synchronized原理进阶

#### 轻量级锁

应用场景：如果一个对象虽然有多个线程访问，但是多线程访问的时间是错开（也就是没有竞争），那么可以使用轻量级锁来优化，如果有竞争则会升级为重量级锁

轻量级锁使用者是透明的，即语法仍然是synchronized

```java
 static final Object lock = new Object();


    public static void method1() {
        synchronized (lock){
            //代码
            method2();
        }
    }

    private static void method2() {
        synchronized (lock){
            //代码
        }
    }
```

加锁：

* 创建锁记录(Lock Record)对象，每个线程的栈帧都包含一个锁记录的结构，内部可以存储锁定对象的Mark Work

  ![image-20220831160225487](并发编程(JUC).assets/image-20220831160225487.png)

* 让锁记录中的Object reference指向对象，并尝试用cas替换Object中的Mark Work，并将Mark Work的值存入到所记录中

  ![image-20220831161307061](并发编程(JUC).assets/image-20220831161307061.png)

* 如果cas替换成功，对象头中存储了(锁记录地址和状态00)，表示有该线程给对象加锁

  ![image-20220831161353663](并发编程(JUC).assets/image-20220831161353663.png)

  * 失败情况：对象中的Mark Work的后两位状态码为00，表示改对象已经加锁，则会失败

  * 当同一个线程给同一个对像再次加锁(锁重入)，那么在线程中再添加一条Lock Record作为重入的记录，由于该线程已经替换对象中的Mark Work，所以重新添加的Lock Record保存Mark Work的内容为null，而Object reference指向对象

    ![image-20220831161505128](并发编程(JUC).assets/image-20220831161505128.png)

解锁：

* 当退出synchronized代码块（解锁时），如果取值为null，表示有重入，这时重置锁记录，表示重入减一

  ![image-20220831163519452](并发编程(JUC).assets/image-20220831163519452.png)

* 退出synchronized代码块（解锁时），如果取值不为null，这时使用cas将Mark Work的值恢复给对象

  * 成功，解锁成功
  * 失败，说明轻量级锁进行了锁膨胀或已经升级为重量级锁，进入重量锁解锁流程

#### 锁膨胀

如果在尝试加轻量级锁的过程中，CAS操作无法成功，这时一种情况就是其它线程为此对象加上了轻量级锁(有竞争)，这时需要进行锁膨胀，将轻量级锁变为重量级锁

* 当Thread-1进行轻量级加锁时，Thread-0已经对该线程加了轻量级锁

  ![image-20220831170251681](并发编程(JUC).assets/image-20220831170251681.png)

* 这时Thread-1加轻量级锁失败，进行锁膨胀流程

  * 即为Object对象申请Monitor锁，让Object的Mark Work指向重量级锁地址
  * 然后自己进入Monitor中的EntryList中BLOCKED

  ![image-20220831170652479](并发编程(JUC).assets/image-20220831170652479.png)

* 当Thread-0退出同步代码块解锁时，使用cas将Mark Work的值恢复给对象头，失败，这是会进入重量级解锁流程，集按照Monitor地址找到Monitor对象，将Owner置为null，唤醒EntryList中的BLOCKED线程

#### 自旋优化

重量级锁竞争的时候，还可以使用自旋来进行优化，如果当前线程自旋成功（即这个时候持锁线程已经退出了同步代码块，释放了锁），这时当线程就可以避免阻塞

自旋重试成功

![image-20220831171721202](并发编程(JUC).assets/image-20220831171721202.png)

自旋重试失败

![image-20220831171853122](并发编程(JUC).assets/image-20220831171853122.png)

#### 偏向锁

轻量级锁在没有竞争时(即只有自己这个线程)，每次重入仍然需要执行CAS操作。

在java6中引入了偏向锁来进行优化：只有第一次使用CAS将线程ID设置到对象的Mark Work头，之后发现这个线程ID时自己的就表示没有竞争，不用重新CAS，以后只要不发生竞争，这个对象就归该线程所有

![image-20220901094153700](并发编程(JUC).assets/image-20220901094153700.png)

#### 偏向状态

对象头的Mark的信息

![image-20220901094606501](并发编程(JUC).assets/image-20220901094606501.png)

![image-20220901100536277](并发编程(JUC).assets/image-20220901100536277.png)

一个对象创建时：

* 如果开启了偏向锁（默认开启）那么对象创建候，markword置为0x05即最后3位为101，这时它的thread，epoch，age都为0
* 偏向锁是默认是延迟的，不会再程序启动时立即生效，如果想避免延迟，可以加VM参数`-XX:BiasedLockingStartupDelay=0`来禁用延迟
* 如果没有开启偏向锁，那么对象创建候，markword置为0x01即最后3位为001，这时它的hashcode，age都为0，第一次用到hashcode时才会赋值

#### 撤销

1. 调用对象的hashcode
   * 调用了对象的hashcode，但是偏向的的对象markwork中存储的时线程id，如果调用hashcode会导致偏向锁被撤销
     * 轻量级锁会在锁记录中记录hashcode
     * 重量级锁会再Monitor中记录hashcode
   * 在调用hashcode候使用偏向锁，记得去掉`-XX:-UseBiasedLocking`
2. 其它线程使用对象
   * 当其他线程使用偏向锁对象时，会将偏向锁升级为轻量级锁
   * 调用wait/notify时候，会将偏向锁升级为重量级锁

#### 批量重偏向

如果对象虽然被多个线程访问，但没有竞争，这时偏向了线程T1的对象仍有机会偏向T2，重偏向会重置对象Thread ID

当撤销偏向锁阈值超过20次候，jvm会认为偏向有问题，于是会在给这些对象加锁时重新偏向至加锁线程

#### 批量撤销

当撤销偏向锁阈值超过 40 次后，jvm 会认为确实偏向错了，根本就不该偏向。于是整个类的所有对象 都会变为不可偏向的，新建的对象也是不可偏向的

> 参考资料 
>
> https://github.com/farmerjohngit/myblog/issues/12 
>
> https://www.cnblogs.com/LemonFive/p/11246086.html 
>
> https://www.cnblogs.com/LemonFive/p/11248248.html 
>
> [偏向锁论文]: (https://www.oracle.com/technetwork/java/biasedlocking-oopsla2006-wp-149958.pdf)

#### 锁消除

对于一个对象加锁和不加锁没有区别时，jvm就会在运行时自动把锁消除，以保证性能提高

`-XX:-EliminateLocks`:关闭锁消除

#### 锁粗化 

对相同对象多次加锁，导致线程发生多次重入，可以使用锁粗化方式来优化，这不同于之前讲的细分锁的粒度

### wait notify原理

![image-20220901143211777](并发编程(JUC).assets/image-20220901143211777.png)

* Owner线程发现条件不满足，调用wait方法，即可以进入WAITING状态
* BLOCKED和WAITING的线程都是处于阻塞状态，不占用CPU时间片
* BLOCKED线程会在Owenr线程释放时唤醒
* WAITING线程会在Owner线程调用notify活notifyAll时唤醒，但唤醒候并不意味立刻获得锁，仍然需要进入EntryList中重新竞争

#### API

* `obj.wait()`:让线程object监视器的线程到waitSet等待
* `obj.notify()`:在object上正在waitSet等待的线程中挑一个唤醒
* `obj.notifyAll`:让object上正在waitSet中的所有线程全部唤醒
* 注意：它们都是线程之间进行协作的手段，都属于 Object 对象的方法。必须获得此对象的锁，才能调用这几个方法
  * `wait()`:方法会释放对象的锁，进入waitSet等待区中，从而让其他线程获取对象锁，无限制的等待，知到notify为止
  * `wait(long n)`:有时限的等待，到n毫秒以后结束等待，获取提前被notify唤醒

#### wait notify的正确姿势

* `sleep(long n)`和`wait(long n)`的区别
  1. sleep时Thread方法，而wait时Object的方法
  2. sleep不需要强制和synchronized配合使用，但是wait需要和synchronized一起使用
  3. sleep在睡眠的时候，不会释放对象锁，但wait在等待时候会释放对象锁
  4. 它们状态都是TIMED_WAITING

### Park & Unpark

#### 基本使用

它们是LockSupport类中的方法

```java
//暂停当前线程
LockSupport.prak();
//恢复某个线程的运行
LockSupport.unpark(暂停线程对象)
```

先park，再unpark

#### 特点

与Object的wait & notify相比

* wait，notify和notifyAll必须配合对象的Monitor一起使用，而park、unpark不需要
* park和ubpark是以线程为单位来【阻塞】和【唤醒】线程，而notify只能随机唤醒一个等待线程，notifyAll时唤醒所有等待的线程，不【精确】
* park和unpark可以先unpark，而wait和notify不能先notify

#### 原理

每个线程都有自己的一个 Parker 对象，由三部分组成 _counter ， _cond 和 _mutex 

形象比喻：![image-20220901165719786](并发编程(JUC).assets/image-20220901165719786.png)

![image-20220901165734625](并发编程(JUC).assets/image-20220901165734625.png)

1. 当线程调用LockSupport.park()方法时
2. 检查_counter，当\_counter为0，这时，获得\_mutex互斥锁
3. 线程进入_cond,条件变量阻塞
4. 设置_counter=0

![image-20220901170039138](并发编程(JUC).assets/image-20220901170039138.png)

1. 当其他线程调用LockSupport.unpark(Thread-0)方法时，设置_counter=1
2. 唤醒_cond条件变量中的Thread-0
3. Thread-0恢复运行
4. 设置_counter=0

![image-20220901170556938](并发编程(JUC).assets/image-20220901170556938.png)

1. 当其他线程先执行了LockSupport.unpark(Thread-0)方法时，设置_counter=1
2. 当线程执行LockSupport.park()方法
3. 检查_counter,发现\_counter=1,这时线程就无需阻塞，继续运行
4. 设置_counter=0

### 线程状态转换

![image-20220901172107934](并发编程(JUC).assets/image-20220901172107934.png)

假设有线程`Thread t`

#### 情况1 `NEW-->RUNNABLE`

* 当调用`t.start()`方法时，由`NEW-->RUNNABLE`

#### 情况2 `RUNNABLE<-->WAITING`

t线程用`synchronized(obj)`获取了对象后

* 调用` obj.wait()` 方法时，t 线程从` RUNNABLE --> WAITING`
*  调用 `obj.notify() `， `obj.notifyAll() `，` t.interrupt() `时 
  * 竞争锁成功，t 线程从` WAITING --> RUNNABLE `
  * 竞争锁失败，t 线程从 `WAITING --> BLOCKED`

#### 情况 3 `RUNNABLE <--> WAITING`

* 当前线程调用 `t.join()` 方法时，当前线程从` RUNNABLE --> WAITING `
  * 注意是当前线程在t 线程对象的监视器上等待
*  t 线程运行结束，或调用了当前线程的 `interrupt()` 时，当前线程从 `WAITING --> RUNNABLE`

#### 情况 4 `RUNNABLE <--> WAITING`

* 当前线程调用 `LockSupport.park()` 方法会让当前线程从 `RUNNABLE --> WAITING `
* 调用` LockSupport.unpark(目标线程) `或调用了线程 的` interrupt() `，会让目标线程从 `WAITING --> RUNNABLE`

#### 情况 5 `RUNNABLE <--> TIMED_WAITING`

t 线程用 `synchronized(obj)`获取了对象锁后 

* 调用 `obj.wait(long n)` 方法时，t 线程从` RUNNABLE --> TIMED_WAITING `
* t 线程等待时间超过了 n 毫秒，或调用` obj.notify()` ，` obj.notifyAll() `， `t.interrupt() `时 
  * 竞争锁成功，t 线程从 `TIMED_WAITING --> RUNNABLE`
  *  竞争锁失败，t 线程从 `TIMED_WAITING --> BLOCKED`

#### 情况 6 `RUNNABLE <--> TIMED_WAITING`

* 当前线程调用` t.join(long n)` 方法时，当前线程从` RUNNABLE --> TIMED_WAITING `
  * 注意是当前线程在t 线程对象的监视器上等待
*  当前线程等待时间超过了 n 毫秒，或t 线程运行结束，或调用了当前线程的 `interrupt() `时，当前线程从 `TIMED_WAITING --> RUNNABLE`

#### 情况 7 `RUNNABLE <--> TIMED_WAITING`

* 当前线程调用 `Thread.sleep(long n)` ，当前线程从 `RUNNABLE --> TIMED_WAITING`
*  当前线程等待时间超过了 n 毫秒调用了线程 的 `interrupt() `，当前线程从 `TIMED_WAITING --> RUNNABLE`

#### 情况 8 `RUNNABLE <--> TIMED_WAITING`

* 当前线程调用 `LockSupport.parkNanos(long nanos)` 或 `LockSupport.parkUntil(long millis)` 时，当前线 程从 `RUNNABLE --> TIMED_WAITING` 
* 调用 `LockSupport.unpark(目标线程)` 或调用了线程 的 `interrupt() `，或是等待超时，会让目标线程从 `TIMED_WAITING--> RUNNABLE`

#### 情况 9 `RUNNABLE <--> BLOCKED`

* t 线程用 `synchronized(obj)` 获取了对象锁时如果竞争失败，从 `RUNNABLE --> BLOCKED `
* 持 obj 锁线程的同步代码块执行完毕，会唤醒该对象上所有 `BLOCKED `的线程重新竞争，如果其中 t 线程竞争 成功，从 `BLOCKED --> RUNNABLE `，其它失败的线程仍然 `BLOCKED`

#### 情况 10 `RUNNABLE <--> TERMINATED`

当前线程所有代码运行完毕，进入 `TERMINATED`

### 多把锁

不同资源或业务如果没有关联，但是使用了一把锁进行管理，则会导致一个线程等另一个毫无相关的线程释放锁，导致性能降低。

这时我们可以把不同资源之间使用不同对象锁进行上锁，这样就不会进行性能下降。

就是将锁粒度细分

* 好处：可以增强并发行
* 坏处：如果一个线程需要获取多把锁，就容易发生死锁

### 活跃性

#### 死锁

一个线程需要获取多把锁的情况下容易发生死锁

* t1线程获得A对象锁， t2线程获得B对象锁，接下来t1想获取B对象的锁,而t2想获取A对象的锁，这样就会倒是死锁
* 避免死锁要注意加锁顺序

#### 活锁

两个线程对一个变量进行操作，导致两个线程都无法结束

#### 饥饿

一个线程由于优先级太低，始终得不到`CPU`调度执行，也不能够结束，

### ReentrantLock

#### 简介

相对于synchronized 它具备一下特点

* 可中断
* 可以是指超时时间
* 可以是指为公平锁
* 支持多花个条件变量

与synchronized一样，都支持可重入

基本语法

```java
//声明锁
static RenntrantLock lock = new RenntrantLock();
//获取锁
lock.lock();
try{
    //临界区
}finally{
    //释放锁
    lock.unlock
}
```

#### 可重入

可重入是指同一个线程如果首次获得了这把锁，那么因为它是这把锁的拥有者，因此由权力再次回去这把锁，如果不可重入锁，那么第二次获得锁时，自己也会被挡住。

#### 可打断

当t1线程再等待的时候，另一个线程t2可以使用t1线程的`interrupt()`方法打断t1线程的等待。注意ReentrantLock中有不可打断的方法，当调用不可打断方法时，调用i线程的`interrupt()`方法也无法打断线程等待

#### 锁超时

* `tryLock()`:调用`tryLock()`方法判断线程是否获得了锁，返回值为`true`时证明获取到了锁，返回值为`false`时证明没有获取到锁；

* `tryLock(long timeout, TimeUnit unit)`:带有时间的锁锁超时，当等待了timeout时间后还没获取到锁，则超时，不再等待。
  * ` TimeUnit unit`:超时单位

#### 公平锁

ReentrantLock默认时不公平的

修改为公平锁

再创建的时候加构成参数：`ReentrantLock lock = new ReentrantLock(true);`

#### 添加变量(waitSet)

`synchronized`中也有条件变量，就是我们讲原理时那个`waitSet`休息室，当条件不满足时进入`waitSet`等待

`ReentrantLock`的条件变量比`synchronized`强大之处在于，它是支持多个条件变量的，这就好比

* `synchronized`是那些不满足条件的线程都在一间休息室等消息
* 而 ReentrantLock 支持多间休息室，有专门等烟的休息室、专门等早餐的休息室、唤醒时也是按休息室来唤 醒

创建休息室

```java
static ReentrantLock lock = new ReentrantLock();
static Condition condition1 = lock.newCondition();
static Condition condition2 = lock.newCondition();
```

使用：

* await前要获取锁，创建好Condition;
* await执行后，会释放锁，进入conditionObject中等待
* await的线程被唤醒(或打断，或超时)需重新竞争lock锁
* 竞争lock锁成功后，从await后继续执行
* `signal`:随机唤醒一个等待线程
* `signalAll`:唤醒所有的等待线程

## 共享模型之内存

JMM即Java Memory Model，它定义了主存，工作内存抽象概念，底层对应着CPU寄存器、缓存、硬件内存、CPU指令优化等

JMM体现在以下几个方面

* 原子性：保证指令不会受到线程上下文切换影响
* 可见性：保证指令不会受cpu缓存的影响
* 有序性：保证指令不会受cpu指令并行优化的影响

### 可见性

一个简单的例子

```java
static boolean run = true;
public static void main(String[] args) throws InterruptedException {
     Thread t = new Thread(()->{
         while(run){
         // ....
         }
         });
     t.start();
     sleep(1);
 	run = false; // 线程t不会如预想的停下来
}
```

代码分析

1. 初始状态，t线程刚开始从主内存读取了run的值到工作内存

   ![image-20220905105428988](并发编程(JUC).assets/image-20220905105428988.png)

2. 因为 t 线程要频繁从主内存中读取 run 的值，JIT 编译器会将 run 的值缓存至自己工作内存中的高速缓存中， 减少对主存中 run 的访问，提高效率

   ![image-20220905105456623](并发编程(JUC).assets/image-20220905105456623.png)

3.  1 秒之后，main 线程修改了 run 的值，并同步至主存，而 t 是从自己工作内存中的高速缓存中读取这个变量 的值，结果永远是旧值

   ![image-20220905105521209](并发编程(JUC).assets/image-20220905105521209.png)

**解决方法**

volatile（易变关键字）：它是用来修饰成员变量和静态成员变量，可以避免线程冲自己从工作缓存中查询变量的值，必须到主存中获取它的值，线程操作colatile变量都是直接操作主存

### 可见性VS原子性

![image-20220905110335903](并发编程(JUC).assets/image-20220905110335903.png)

注意 synchronized 语句块既可以保证代码块的原子性，也同时保证代码块内变量的可见性。但缺点是 synchronized 是属于重量级操作，性能相对更低

### 有序性

JVM会在不影响正确性的前提下，可以调整语句的执行顺序。

```JAVA
static int i;
static int j;
// 在某个线程内执行如下赋值操作
i = ...;
j = ...; 
//可以看到，至于是先执行 i 还是 先执行 j ，对最终的结果不会产生影响。所以，上面代码真正执行时，既可以是
i = ...;
j = ...;
//也可以是
j= ...;
i= ...;
```

这种特性称之为『指令重排』，多线程下『指令重排』会影响正确性。

#### 指令重排原理

##### 1.名词

**Clock Cycle Time**

主频的概念大家接触的比较多，而 CPU 的 Clock Cycle Time（时钟周期时间），等于主频的倒数，意思是 CPU 能 够识别的最小时间单位，比如说 4G 主频的 CPU 的 Clock Cycle Time 就是 0.25 ns，作为对比，我们墙上挂钟的 Cycle Time 是 1s

例如，运行一条加法指令一般需要一个时钟周期时间

**CPI**

有的指令需要更多的时钟周期时间，所以引出了 CPI （Cycles Per Instruction）指令平均时钟周期数

**IPC**

IPC（Instruction Per Clock Cycle） 即 CPI 的倒数，表示每个时钟周期能够运行的指令数

**CPU执行时间**

程序的 CPU 执行时间，即我们前面提到的 user + system 时间，可以用下面的公式来表示

```java
程序 CPU 执行时间 = 指令数 * CPI * Clock Cycle Time 
```

##### 2.指令重排序优化

事实上，现代处理器会设计为一个时钟周期完成一条执行时间最长的 CPU 指令。可以想到指令 还可以再划分成一个个更小的阶段，例如，每条指令都可以分为： 取指令 - 指令译码 - 执行指令 - 内存访问 - 数据 写回 这 5 个阶段

![image-20220905135243671](并发编程(JUC).assets/image-20220905135243671.png)

> 术语参考：
>
> * instruction fetch (IF)
> *  instruction decode (ID)
> *  execute (EX) 
> * memory access (MEM)
> *  register write back (WB)

在不改变程序结果的前提下，这些指令的各个阶段可以通过重排序和组合来实现指令级并行，这一技术在 80's 中 叶到 90's 中叶占据了计算架构的重要地位。

> 提示： 
>
> 分阶段，分工是提升效率的关键！

指令重排的前提是，重排指令不能影响结果，例如

```java
// 可以重排的例子
int a = 10; // 指令1
int b = 20; // 指令2
System.out.println( a + b );
// 不能重排的例子
int a = 10; // 指令1
int b = a - 5; // 指令2
```

> 参考： Scoreboarding and the Tomasulo algorithm (which is similar to scoreboarding but makes use of register renaming) are two of the most common techniques for implementing out-of-order execution and instruction-level parallelism.

##### 诡异的结果

```java
int num = 0;
boolean ready = false;
// 线程1 执行此方法
public void actor1(I_Result r) {
 if(ready) {
 r.r1 = num + num;
 } else {
 r.r1 = 1;
 }
}
// 线程2 执行此方法
public void actor2(I_Result r) {
 num = 2;
 ready = true;
}

```

![image-20220905135551816](并发编程(JUC).assets/image-20220905135551816.png)

##### 解决方法

volatile 修饰的变量，可以禁用指令重排

### Volatile原理

volatile的底层原理实现是内存屏障，Memory Barrier（Memory Fence)

* 对volatile变量的写指令后会加入写屏障
* 对volatile变量的读指令会加入读屏障

#### 1.保证可见性

* 写屏障（sfence）：保证再改屏障以前的，对共享变量的改动，都同步到主存当中

  ```java
  public void actor2(I_Result r) {
       num = 2;
       ready = true; // ready 是 volatile 赋值带写屏障
       // 写屏障
  }
  ```

* 读屏障（Ifence）：保证再改屏障之后，对共享变量的读取，加载的是主存中最新数据

  ```java
  public void actor1(I_Result r) {
       // 读屏障
       // ready 是 volatile 读取值带读屏障
       if(ready) {
       	r.r1 = num + num;
       } else {
       	r.r1 = 1;
       }
  }
  ```

  ![image-20220905143554529](并发编程(JUC).assets/image-20220905143554529.png)

#### 2.保证有序性

* 写屏障会确保指令重排序时，不会将写屏障之前的代码排在写屏障之后

  ```java
  public void actor2(I_Result r) {
       num = 2;
       ready = true; // ready 是 volatile 赋值带写屏障
       // 写屏障
      //num = 2;这行代码是写屏障之前的代码，不会被指令重排到写屏障之后
  }
  ```

* 读屏障会确保指令重排序时，不会将读屏障之后的代码排在读屏障之前

  ```java
  public void actor1(I_Result r) {
      // 本例子中不会将num读取出现在读屏障之前
       // 读屏障
       // ready 是 volatile 读取值带读屏障
       if(ready) {
       	r.r1 = num + num;
       } else {
       	r.r1 = 1;
       }
  }
  ```

  ![image-20220905144137048](并发编程(JUC).assets/image-20220905144137048.png)

还是那句话，不能解决指令交错： 

* 写屏障仅仅是保证之后的读能够读到最新的结果，但不能保证读跑到它前面去 
* 而有序性的保证也只是保证了本线程内相关代码不被重排序

![image-20220905151743290](并发编程(JUC).assets/image-20220905151743290.png)

#### happens-before

happens-before 规定了对共享变量的写操作对其它线程的读操作可见，它是可见性与有序性的一套规则总结，抛开以下 happens-before 规则，JMM 并不能保证一个线程对共享变量的写，对于其它线程对该共享变量的读可见

* 线程解锁 m 之前对变量的写，对于接下来对 m 加锁的其它线程对该变量的读可见

  ```java
  static int x;
  static Object m = new Object();
  new Thread(()->{
       synchronized(m) {
       x = 10;
       }
  	},"t1").start();
      new Thread(()->{
       synchronized(m) {
       System.out.println(x);
       }
      },"t2").start();
  ```

* 线程对 volatile 变量的写，对接下来其它线程对该变量的读可见

  ```java
  volatile static int x;
  
  new Thread(()->{
   	x = 10;
  },"t1").start();
  new Thread(()->{
  	 System.out.println(x);
  },"t2").start();
  
  ```

* 线程 start 前对变量的写，对该线程开始后对该变量的读可见

  ```java
  static int x;
  
  x = 10;
  
  new Thread(()->{
   System.out.println(x);
  },"t2").start();
  ```

* 线程结束前对变量的写，对其它线程得知它结束后的读可见（比如其它线程调用 t1.isAlive() 或 t1.join()等待 它结束）

  ```java
  static int x;
  
  Thread t1 = new Thread(()->{
   x = 10;
  },"t1");
  t1.start();
  t1.join();
  System.out.println(x);
  
  ```

* 线程 t1 打断 t2（interrupt）前对变量的写，对于其他线程得知 t2 被打断后对变量的读可见（通过 t2.interrupted 或 t2.isInterrupted）

  ```java
  static int x;
  public static void main(String[] args) {
   Thread t2 = new Thread(()->{
           while(true) {
           if(Thread.currentThread().isInterrupted()) {
           System.out.println(x);
           break;
           }
   	}
   },"t2");
   t2.start();
   new Thread(()->{
       sleep(1);
       x = 10;
       t2.interrupt();
   	},"t1").start();
   while(!t2.isInterrupted()) {
       Thread.yield();
   	}
   System.out.println(x);
  }
  
  ```

* 对变量默认值（0，false，null）的写，对其它线程对该变量的读可见

* 具有传递性，如果`x hb-> y` 并且 `y hb-> z` 那么有 `x hb-> z` ，配合 volatile 的防指令重排，有下面的例子

  ```java
  volatile static int x;
  static int y;
  new Thread(()->{
   y = 10;
   x = 20;
  },"t1").start();
  new Thread(()->{
   // x=20 对 t2 可见, 同时 y=10 也对 t2 可见
   System.out.println(x);
  },"t2").start();
  ```

## 共享模式之无锁

### CAS与Volatile

```java
package com.gyf.text;

import java.util.concurrent.atomic.AtomicInteger;

public class CASTest {


    public static void main(String[] args) throws InterruptedException {
        Desc desc = new Desc(100);
        Thread t1 = new Thread(() -> {
            desc.withdraw(10);
        });

        Thread t2 =new Thread(() -> {
            desc.withdraw(10);
        });
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(desc.getIn());
    }
}


class Desc{
    private AtomicInteger atomicInteger;

    public Desc(int i) {
        this.atomicInteger = new AtomicInteger(i);
    }

    public Integer getIn(){
        return atomicInteger.get();
    }

    public void withdraw(int i ){
        while (true){
            int prev = atomicInteger.get();
            int next = prev - i;
            if (atomicInteger.compareAndSet(prev,next)) {
                break;
            }
        }
    }
}
```

`AtomicInteger`的解决方法，内部并没有用锁来保护共享变量的线程安全。

其中的关键是`compareAndSet`，它的简称就是`CAS`（也有`Compare And Swap`的说法），它必须是原子操作

![image-20220905164400117](并发编程(JUC).assets/image-20220905164400117.png)

> 注意 
>
> 其实 CAS 的底层是 lock cmpxchg 指令（X86 架构），在单核 CPU 和多核 CPU 下都能够保证【比较-交 换】的原子性。
>
> 在多核状态下，某个核执行到带 lock 的指令时，CPU 会让总线锁住，当这个核把此指令执行完毕，再 开启总线。这个过程中不会被线程的调度机制所打断，保证了多个线程对内存操作的准确性，是原子的。

### volatile

获取共享变量时，为了保证该变量的可见性，需要使用 volatile 修饰。

它可以用来修饰成员变量和静态成员变量，他可以避免线程从自己的工作缓存中查找变量的值，必须到主存中获取 它的值，线程操作 volatile 变量都是直接操作主存。即一个线程对 volatile 变量的修改，对另一个线程可见。

> 注意
>
>  volatile 仅仅保证了共享变量的可见性，让其它线程能够看到最新值，但不能解决指令交错问题（不能保证原子性）

CAS 必须借助 volatile 才能读取到共享变量的最新值来实现【比较并交换】的效果

### CAS的特点

结合 CAS 和 volatile 可以实现无锁并发，适用于线程数少、多核 CPU 的场景下。

* CAS 是基于乐观锁的思想：最乐观的估计，不怕别的线程来修改共享变量，就算改了也没关系，我吃亏点再 重试呗。
* synchronized 是基于悲观锁的思想：最悲观的估计，得防着其它线程来修改共享变量，我上了锁你们都别想 改，我改完了解开锁，你们才有机会。
* CAS 体现的是无锁并发、无阻塞并发，请仔细体会这两句话的意思
  * 因为没有使用 synchronized，所以线程不会陷入阻塞，这是效率提升的因素之一
  * 但如果竞争激烈，可以想到重试必然频繁发生，反而效率会受影响

### 原子整数

JUC并发包提供了：

* AtomicBoolean
* AtomicInteger
* AtomicLong

以AtomicInteger为例

```java
AtomicInteger i = new AtomicInteger(0);
// 获取并自增（i = 0, 结果 i = 1, 返回 0），类似于 i++
System.out.println(i.getAndIncrement());
// 自增并获取（i = 1, 结果 i = 2, 返回 2），类似于 ++i
System.out.println(i.incrementAndGet());
// 自减并获取（i = 2, 结果 i = 1, 返回 1），类似于 --i
System.out.println(i.decrementAndGet());
// 获取并自减（i = 1, 结果 i = 0, 返回 1），类似于 i--
System.out.println(i.getAndDecrement());
// 获取并加值（i = 0, 结果 i = 5, 返回 0）
System.out.println(i.getAndAdd(5));
// 加值并获取（i = 5, 结果 i = 0, 返回 0）
System.out.println(i.addAndGet(-5));
// 获取并更新（i = 0, p 为 i 的当前值, 结果 i = -2, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
System.out.println(i.getAndUpdate(p -> p - 2));
// 更新并获取（i = -2, p 为 i 的当前值, 结果 i = 0, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
System.out.println(i.updateAndGet(p -> p + 2));
// 获取并计算（i = 0, p 为 i 的当前值, x 为参数1, 结果 i = 10, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
// getAndUpdate 如果在 lambda 中引用了外部的局部变量，要保证该局部变量是 final 的
// getAndAccumulate 可以通过 参数1 来引用外部的局部变量，但因为其不在 lambda 中因此不必是 final
System.out.println(i.getAndAccumulate(10, (p, x) -> p + x));
// 计算并获取（i = 10, p 为 i 的当前值, x 为参数1, 结果 i = 0, 返回 0）
// 其中函数中的操作能保证原子，但函数需要无副作用
System.out.println(i.accumulateAndGet(-10, (p, x) -> p + x));
```

```java
 AtomicInteger atomicInteger = new AtomicInteger(0);
        log.info("AtomicInteger中的get方法：{}",atomicInteger.get());
        log.info("AtomicInteger中的incrementAndGet方法：{}",atomicInteger.incrementAndGet());
        log.info("AtomicInteger中的getAndIncrement方法：{}",atomicInteger.getAndIncrement());
        log.info("AtomicInteger中的decrementAndGet方法：{}",atomicInteger.decrementAndGet());
        log.info("AtomicInteger中的getAndDecrement方法：{}", atomicInteger.getAndDecrement());
        log.info("AtomicInteger中的getAndAdd方法：{}",atomicInteger.getAndAdd(5));
        log.info("AtomicInteger中的addAndGet方法：{}",atomicInteger.addAndGet(5));
        log.info("AtomicInteger中的getAndUpdate方法：{}",atomicInteger.getAndUpdate(value->value/2));
        log.info("AtomicInteger中的updateAndGet方法：{}",atomicInteger.updateAndGet(value->value/2));
        log.info("AtomicInteger中的getAndAccumulate方法：{}",atomicInteger.getAndAccumulate(10,(p,x)->p*x));
        log.info("AtomicInteger中的accumulateAndGet方法：{}",atomicInteger.accumulateAndGet(10,(p,x)->p*x));
        log.info("AtomicInteger中的compareAndSet方法：{}",atomicInteger.compareAndSet(200,10));

        AtomicBoolean atomicBoolean = new AtomicBoolean(true);
        atomicBoolean.set(false);
        log.info("AtomicBoolean中的get方法：{}",atomicBoolean.get());
        log.info("AtomicBoolean中的compareAndSet方法：{}",atomicBoolean.compareAndSet(false,true));

        AtomicLong atomicLong = new AtomicLong(1);
        
        //和AtomicInteger相似
```

### 原子引用

原子引用类型

* AtomicReference 

* AtomicMarkableReference：判断是否修改过；

* AtomicStampedReference：带有版本好一个版本号

  ![AtomicMarkableReference的例子](并发编程(JUC).assets/image-20220906173153960.png)

```java
package com.gyf.text;

import java.util.concurrent.atomic.AtomicReference;

public class AtomicReferenceTest {


    public static void main(String[] args) {
        AtomicReference<Student> studentAtomicReference = new AtomicReference<>();
        Student student = studentAtomicReference.get();
        System.out.println(student);
        Student newStudent = new Student();
        newStudent.setName("张三");
        newStudent.setAge(12);
        newStudent.setAddress("北京");
        studentAtomicReference.compareAndSet(student, newStudent);
        System.out.println(studentAtomicReference.get());

    }
}



class Student{
    private String name;
    private int age;
    private String address;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
}
```

### 原子数组

* AtomicIntegerArray 
* AtomicLongArray 
* AtomicReferenceArray

### 字段更新器

* AtomicReferenceFieldUpdater // 域 字段 
* AtomicIntegerFieldUpdater 
* AtomicLongFieldUpdater

利用字段更新器，可以针对对象的某个域（Field）进行原子操作，只能配合 volatile 修饰的字段使用，否则会出现异常

```java
package com.gyf.text;

import java.util.concurrent.atomic.AtomicReferenceFieldUpdater;

public class AtomicFiledTest {


    public static void main(String[] args) {
        AtomicReferenceFieldUpdater<Teacher,String> atomicReferenceFieldUpdater = AtomicReferenceFieldUpdater.newUpdater(Teacher.class,String.class,"name");
       Teacher t = new Teacher();
        System.out.println(atomicReferenceFieldUpdater.get(t));
        atomicReferenceFieldUpdater.compareAndSet(t,null,"张三");
        System.out.println(atomicReferenceFieldUpdater.get(t));


    }
}
class Teacher{
   protected   volatile String name;
    private int age;
    private String address;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Teacher{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
}
```

> 1. 操作的字段不能是static类型;
> 2. 操作的字段不能是final类型的;
> 3. 字段必须是volatile修饰的，也就是数据本身是读一致的;
> 4. 属性必须对当前的Updater所在的区域是可见的，如果不是当前类内部进行原子更新器操作不能使用private，protected子类操作父类时修饰符必须是protect权限及以上，
> 5. 如果在同一个package下则必须是default权限及以上，也就是说无论何时都应该保证操作类与被操作类间的可见性;

### 原子累加器

后续补充，有点懵

### Unsafe

后续补充，有点懵

## 共享模型之不可变

如果一个对象在不能够修改其内部状态（属性），那么它就是线程安全的，因为不存在并发修改。

### final的使用

发现该类、类中所有属性都是final的

* 属性用final修改保证了改属性是只读的，不能修改
* 类用final修饰保证了该类中的方法不能被覆盖，防止子类无意间破坏不可变性

#### 保护性拷贝

String就是final保护的类，其中以substring为例子，其功能截取字串，为了不可变保证安全使用了保护性拷贝

```java
public String substring(int beginIndex) {
        if (beginIndex < 0) {
            throw new StringIndexOutOfBoundsException(beginIndex);
        }
        int subLen = value.length - beginIndex;
        if (subLen < 0) {
            throw new StringIndexOutOfBoundsException(subLen);
        }
        return (beginIndex == 0) ? this : new String(value, beginIndex, subLen);
    }
```

发现最后使用了`new String(value, beginIndex, subLen);`创建了一个新的字符串

```
public String(char value[], int offset, int count) {
        if (offset < 0) {
            throw new StringIndexOutOfBoundsException(offset);
        }
        if (count <= 0) {
            if (count < 0) {
                throw new StringIndexOutOfBoundsException(count);
            }
            if (offset <= value.length) {
                this.value = "".value;
                return;
            }
        }
        // Note: offset or count might be near -1>>>1.
        if (offset > value.length - count) {
            throw new StringIndexOutOfBoundsException(offset + count);
        }
        this.value = Arrays.copyOfRange(value, offset, offset+count);
    }
```

构造新字符串对象时，会生成新的 char[] value，对内容进行复制 。这种通过创建副本对象来避 免共享的手段称之为【保护性拷贝（defensive copy）】

### final原理

相较于volatile原理，final原理就笔记简单

```java
public class TestFinal {
 final int a = 20;
}
```

字节码

```java
0: aload_0
1: invokespecial #1 // Method java/lang/Object."<init>":()V
4: aload_0
5: bipush 20
7: putfield #2 // Field a:I
 <-- 写屏障
10: return
```

发现 final 变量的赋值也会通过 putfield 指令来完成，同样在这条指令之后也会加入写屏障，保证在其它线程读到 它的值时不会出现为 0 的情况

#### 获取final变量的原理

在字节码的角度，使用了final修饰的变量，在其他类中使用，不是引用其变量的位置，而是直接将变量的值复制到引用类中栈中。

![image-20220907103536873](并发编程(JUC).assets/image-20220907103536873.png)

## 共享模型之工具

### 线程池

#### 自定义线程池

```java
package cn.itcast.n8;

import lombok.extern.slf4j.Slf4j;
import org.springframework.core.log.LogDelegateFactory;

import java.util.ArrayDeque;
import java.util.Deque;
import java.util.HashSet;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

@Slf4j(topic = "c.TestPool")
public class TestPool {
    public static void main(String[] args) {
        ThreadPool threadPool = new ThreadPool(1,
                1000, TimeUnit.MILLISECONDS, 1, (queue, task)->{
            // 1. 死等
//            queue.put(task);
            // 2) 带超时等待
//            queue.offer(task, 1500, TimeUnit.MILLISECONDS);
            // 3) 让调用者放弃任务执行
//            log.debug("放弃{}", task);
            // 4) 让调用者抛出异常
//            throw new RuntimeException("任务执行失败 " + task);
            // 5) 让调用者自己执行任务
            task.run();
        });
        for (int i = 0; i < 4; i++) {
            int j = i;
            threadPool.execute(() -> {
                try {
                    Thread.sleep(1000L);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                log.debug("{}", j);
            });
        }
    }
}

@FunctionalInterface // 拒绝策略
interface RejectPolicy<T> {
    void reject(BlockingQueue<T> queue, T task);
}

@Slf4j(topic = "c.ThreadPool")
class ThreadPool {
    // 任务队列
    private BlockingQueue<Runnable> taskQueue;

    // 线程集合
    private HashSet<Worker> workers = new HashSet<>();

    // 核心线程数
    private int coreSize;

    // 获取任务时的超时时间
    private long timeout;

    private TimeUnit timeUnit;

    private RejectPolicy<Runnable> rejectPolicy;

    // 执行任务
    public void execute(Runnable task) {
        // 当任务数没有超过 coreSize 时，直接交给 worker 对象执行
        // 如果任务数超过 coreSize 时，加入任务队列暂存
        synchronized (workers) {
            if(workers.size() < coreSize) {
                Worker worker = new Worker(task);
                log.debug("新增 worker{}, {}", worker, task);
                workers.add(worker);
                worker.start();
            } else {
//                taskQueue.put(task);
                // 1) 死等
                // 2) 带超时等待
                // 3) 让调用者放弃任务执行
                // 4) 让调用者抛出异常
                // 5) 让调用者自己执行任务
                taskQueue.tryPut(rejectPolicy, task);
            }
        }
    }

    public ThreadPool(int coreSize, long timeout, TimeUnit timeUnit, int queueCapcity, RejectPolicy<Runnable> rejectPolicy) {
        this.coreSize = coreSize;
        this.timeout = timeout;
        this.timeUnit = timeUnit;
        this.taskQueue = new BlockingQueue<>(queueCapcity);
        this.rejectPolicy = rejectPolicy;
    }

    class Worker extends Thread{
        private Runnable task;

        public Worker(Runnable task) {
            this.task = task;
        }

        @Override
        public void run() {
            // 执行任务
            // 1) 当 task 不为空，执行任务
            // 2) 当 task 执行完毕，再接着从任务队列获取任务并执行
//            while(task != null || (task = taskQueue.take()) != null) {
            while(task != null || (task = taskQueue.poll(timeout, timeUnit)) != null) {
                try {
                    log.debug("正在执行...{}", task);
                    task.run();
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    task = null;
                }
            }
            synchronized (workers) {
                log.debug("worker 被移除{}", this);
                workers.remove(this);
            }
        }
    }
}
@Slf4j(topic = "c.BlockingQueue")
class BlockingQueue<T> {
    // 1. 任务队列
    private Deque<T> queue = new ArrayDeque<>();

    // 2. 锁
    private ReentrantLock lock = new ReentrantLock();

    // 3. 生产者条件变量
    private Condition fullWaitSet = lock.newCondition();

    // 4. 消费者条件变量
    private Condition emptyWaitSet = lock.newCondition();

    // 5. 容量
    private int capcity;

    public BlockingQueue(int capcity) {
        this.capcity = capcity;
    }

    // 带超时阻塞获取
    public T poll(long timeout, TimeUnit unit) {
        lock.lock();
        try {
            // 将 timeout 统一转换为 纳秒
            long nanos = unit.toNanos(timeout);
            while (queue.isEmpty()) {
                try {
                    // 返回值是剩余时间
                    if (nanos <= 0) {
                        return null;
                    }
                    nanos = emptyWaitSet.awaitNanos(nanos);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            T t = queue.removeFirst();
            fullWaitSet.signal();
            return t;
        } finally {
            lock.unlock();
        }
    }

    // 阻塞获取
    public T take() {
        lock.lock();
        try {
            while (queue.isEmpty()) {
                try {
                    emptyWaitSet.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            T t = queue.removeFirst();
            fullWaitSet.signal();
            return t;
        } finally {
            lock.unlock();
        }
    }

    // 阻塞添加
    public void put(T task) {
        lock.lock();
        try {
            while (queue.size() == capcity) {
                try {
                    log.debug("等待加入任务队列 {} ...", task);
                    fullWaitSet.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            log.debug("加入任务队列 {}", task);
            queue.addLast(task);
            emptyWaitSet.signal();
        } finally {
            lock.unlock();
        }
    }

    // 带超时时间阻塞添加
    public boolean offer(T task, long timeout, TimeUnit timeUnit) {
        lock.lock();
        try {
            long nanos = timeUnit.toNanos(timeout);
            while (queue.size() == capcity) {
                try {
                    if(nanos <= 0) {
                        return false;
                    }
                    log.debug("等待加入任务队列 {} ...", task);
                    nanos = fullWaitSet.awaitNanos(nanos);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            log.debug("加入任务队列 {}", task);
            queue.addLast(task);
            emptyWaitSet.signal();
            return true;
        } finally {
            lock.unlock();
        }
    }

    public int size() {
        lock.lock();
        try {
            return queue.size();
        } finally {
            lock.unlock();
        }
    }

    public void tryPut(RejectPolicy<T> rejectPolicy, T task) {
        lock.lock();
        try {
            // 判断队列是否满
            if(queue.size() == capcity) {
                rejectPolicy.reject(this, task);
            } else {  // 有空闲
                log.debug("加入任务队列 {}", task);
                queue.addLast(task);
                emptyWaitSet.signal();
            }
        } finally {
            lock.unlock();
        }
    }
}
```

#### ThreadPoolExecutor

类图

![image-20220907163341894](并发编程(JUC).assets/image-20220907163341894.png)

##### 线程池状态

ThreadPoolExecutor 使用 int 的高 3 位来表示线程池状态，低 29 位表示线程数量

![线程池状态](并发编程(JUC).assets/image-20220907163650452.png)

从数字上比较，TERMINATED > TIDYING > STOP > SHUTDOWN > RUNNING(因为是高3为，第一位是符号位，所以RUNNING是负数)

这些信息存储在一个原子变量 ctl 中，目的是将线程池状态与线程个数合二为一，这样就可以用一次 cas 原子操作 进行赋值

```java
// c 为旧值， ctlOf 返回结果为新值
ctl.compareAndSet(c, ctlOf(targetState, workerCountOf(c))));

// rs 为高 3 位代表线程池状态， wc 为低 29 位代表线程个数，ctl 是合并它们
private static int ctlOf(int rs, int wc) { return rs | wc; }
```

##### 构造方法

```java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```

* corePoolSize:核心线程数目(最多保留的线程数)
* maximumPoolSize:最大线程shumu
* keepAliveTime:生存时间-针对救急线程
* unit:时间单位-针对救急线程
* workQueue:阻塞队列
* threadFactory:线程工厂-可以位线程创建时起名字
* handler:拒绝策略

工作方式：

![image-20220907165245951](并发编程(JUC).assets/image-20220907165245951.png)

* 线程池中刚开始没有线程，当一个任务提交给线程池后，线程池会创建一个新线程来执行任务。
* 当线程数达到 corePoolSize 并没有线程空闲，这时再加入任务，新加的任务会被加入workQueue 队列排 队，直到有空闲的线程。
* 如果队列选择了有界队列，那么任务超过了队列大小时，会创建 maximumPoolSize - corePoolSize 数目的线 程来救急。
* 如果线程到达 maximumPoolSize 仍然有新任务这时会执行拒绝策略。拒绝策略 jdk 提供了 4 种实现，其它 著名框架也提供了实现
  * AbortPolicy 让调用者抛出 RejectedExecutionException 异常，这是默认策略
  * CallerRunsPolicy 让调用者运行任务
  * DiscardPolicy 放弃本次任务
  * DiscardOldestPolicy 放弃队列中最早的任务，本任务取而代之
  * Dubbo 的实现，在抛出 RejectedExecutionException 异常之前会记录日志，并 dump 线程栈信息，方 便定位问题
  * Netty 的实现，是创建一个新线程来执行任务
  * ActiveMQ 的实现，带超时等待（60s）尝试放入队列，类似我们之前自定义的拒绝策略
  * PinPoint 的实现，它使用了一个拒绝策略链，会逐一尝试策略链中每种拒绝策略
* 当高峰过去后，超过corePoolSize 的救急线程如果一段时间没有任务做，需要结束节省资源，这个时间由 keepAliveTime 和 unit 来控制

![image-20220907165511294](并发编程(JUC).assets/image-20220907165511294.png)

```java
//简单使用
package com.gyf.text;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;


@Slf4j(topic = "c.TestJavaThreadPool")
public class TestJavaThreadPool {


    public static void main(String[] args) {



        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(1,2,1000, TimeUnit.NANOSECONDS, new LinkedBlockingDeque<Runnable>(1)
        , new ThreadFactory(){
            AtomicInteger atomicInteger = new AtomicInteger(1);

            @Override
            public Thread newThread(Runnable r) {
                Thread t =new Thread(r,"线程pool_"+atomicInteger.getAndIncrement());
                return t;
            }
        });


        threadPoolExecutor.execute(()->{
            log.info("{}",1);
        });
        threadPoolExecutor.execute(()->{
            log.info("{}",2);
        });
        threadPoolExecutor.execute(()->{
            log.info("{}",3);
        });

        threadPoolExecutor.shutdown();
    }
}
```

根据这个构造方法，JDK Executors 类中提供了众多工厂方法来创建各种用途的线程池

##### newFixedThreadPool(固定大小线程池)

```java
  public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```

特点：

* 核心线程数 == 最大线程数（没有救急线程被创建），因此也无需超时时间
* 阻塞队列是无界的，可以放任意数量的任务

> 评价：适用于任务量已知，相对耗时的任务

##### newCachedThreadPool(带缓存线程池)

```java
public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

特点:

* 核心线程数是 0， 最大线程数是 Integer.MAX_VALUE，救急线程的空闲生存时间是 60s，意味着
  * 全部都是救急线程（60s 后可以回收）
  * 救急线程可以无限创建
* 队列采用了 SynchronousQueue 实现特点是，它没有容量，没有线程来取是放不进去的（一手交钱、一手交货）

```java
@Slf4j(topic = "c.TestSynchronousQueue")
public class TestSynchronousQueue {
    public static void main(String[] args) {
        SynchronousQueue<Integer> integers = new SynchronousQueue<>();
        new Thread(() -> {
            try {
                log.debug("putting {} ", 1);
                integers.put(1);
                log.debug("{} putted...", 1);

                log.debug("putting...{} ", 2);
                integers.put(2);
                log.debug("{} putted...", 2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"t1").start();

        sleep(1);

        new Thread(() -> {
            try {
                log.debug("taking {}", 1);
                integers.take();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"t2").start();

        sleep(1);

        new Thread(() -> {
            try {
                log.debug("taking {}", 2);
                integers.take();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"t3").start();
    }
}
```

> 评价:整个线程池表现为线程数会根据任务量不断增长，没有上限，当任务执行完毕，空闲 1分钟后释放线 程。 适合任务数比较密集，但每个任务执行时间较短的情况

##### newSingleThreadExecutor(单线程线程池)

```java
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```

使用场景：

希望多个任务排队执行。线程数固定为 1，任务数多于 1 时，会放入无界队列排队。任务执行完毕，这唯一的线程 也不会被释放。

区别：

* 自己创建一个单线程串行执行任务，如果任务执行失败而终止那么没有任何补救措施，而线程池还会新建一 个线程，保证池的正常工作
* Executors.newSingleThreadExecutor() 线程个数始终为1，不能修改
  * FinalizableDelegatedExecutorService 应用的是装饰器模式，只对外暴露了 ExecutorService 接口，因 此不能调用 ThreadPoolExecutor 中特有的方法
* Executors.newFixedThreadPool(1) 初始时为1，以后还可以修改
  * 对外暴露的是 ThreadPoolExecutor 对象，可以强转后调用 setCorePoolSize 等方法进行修改

##### 提交任务

```java
// 执行任务
void execute(Runnable command);
// 提交任务 task，用返回值 Future 获得任务执行结果
<T> Future<T> submit(Callable<T> task);
// 提交 tasks 中所有任务
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
 throws InterruptedException;
// 提交 tasks 中所有任务，带超时时间
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
 long timeout, TimeUnit unit)
 throws InterruptedException;
// 提交 tasks 中所有任务，哪个任务先成功执行完毕，返回此任务执行结果，其它任务取消
<T> T invokeAny(Collection<? extends Callable<T>> tasks)
 throws InterruptedException, ExecutionException;
// 提交 tasks 中所有任务，哪个任务先成功执行完毕，返回此任务执行结果，其它任务取消，带超时时间
<T> T invokeAny(Collection<? extends Callable<T>> tasks,
 long timeout, TimeUnit unit)
 throws InterruptedException, ExecutionException, TimeoutException;
```

例子

```java
package com.gyf.text;

import lombok.extern.slf4j.Slf4j;

import java.util.Arrays;
import java.util.List;
import java.util.concurrent.*;

@Slf4j(topic = "c.newFixedThreadPoolTest")
public class newFixedThreadPoolTest {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(3);


        Object o = executorService.invokeAny(Arrays.asList(() -> {
                    log.info("begin");
                    TimeUnit.SECONDS.sleep(1);
                    log.info("end");
                    return "1";
                },
                () -> {
                    log.info("begin");
                    TimeUnit.MICROSECONDS.sleep(2500);
                    log.info("end");
                    return "2";
                },
                () -> {
                    log.info("begin");
                    TimeUnit.MICROSECONDS.sleep(500);
                    log.info("end");
                    return "3";
                },
                () -> {
                    log.info("begin");
                    TimeUnit.SECONDS.sleep(2);
                    log.info("end");
                    return "4";
                }));
        log.info("{}",o);


    }

    private static void method2(ExecutorService executorService) throws InterruptedException {
        List<Future<String>> futures = executorService.invokeAll(Arrays.asList(
                () -> {
                    log.info("begin");
                    TimeUnit.SECONDS.sleep(1);
                    log.info("end");
                    return "1";
                },
                () -> {
                    log.info("begin");
                    TimeUnit.MICROSECONDS.sleep(2500);
                    log.info("end");
                    return "1";
                },
                () -> {
                    log.info("begin");
                    TimeUnit.MICROSECONDS.sleep(500);
                    log.info("end");
                    return "1";
                },
                () -> {
                    log.info("begin");
                    TimeUnit.SECONDS.sleep(2);
                    log.info("end");
                    return "1";
                }
        ));

        futures.forEach(f->{
            try {
                log.info("{}",f.get());
            } catch (InterruptedException  | ExecutionException e) {
                e.printStackTrace();
            }
        });
    }

    private static void method1(ExecutorService executorService) throws InterruptedException, ExecutionException {
        executorService.execute(()->{
            log.info("1");
        });

        Future<String> submit = executorService.submit(() -> {
            return "ok";
        });
        log.info("{}",submit.get());
    }

}
```

##### 关闭线程池

###### shutdown

```java
/*
线程池状态变为 SHUTDOWN
- 不会接收新任务
- 但已提交任务会执行完
- 此方法不会阻塞调用线程的执行
*/
void shutdown();
```

```java
public void shutdown() {
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try {
            checkShutdownAccess();
            // 修改线程池状态
            advanceRunState(SHUTDOWN);
            // 仅会打断空闲线程
            interruptIdleWorkers();
            onShutdown(); // hook for ScheduledThreadPoolExecutor
        } finally {
            mainLock.unlock();
        }
    // 尝试终结(没有运行的线程可以立刻终结，如果还有运行的线程也不会等)
        tryTerminate();
    }
```

###### shutdownNow

```java
/*
线程池状态变为 STOP
- 不会接收新任务
- 会将队列中的任务返回
- 并用 interrupt 的方式中断正在执行的任务
*/
List<Runnable> shutdownNow();
```

```java
public List<Runnable> shutdownNow() {
        List<Runnable> tasks;
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try {
            checkShutdownAccess();
            // 修改线程池状态
            advanceRunState(STOP);
            //打断所有线程
            interruptWorkers();
            // 获取队列中剩余任务
            tasks = drainQueue();
        } finally {
            mainLock.unlock();
        }
    // 尝试终结
        tryTerminate();
        return tasks;
    }
```

###### 其他方法

```java
// 不在 RUNNING 状态的线程池，此方法就返回 true
boolean isShutdown();
// 线程池状态是否是 TERMINATED
boolean isTerminated();
// 调用 shutdown 后，由于调用线程并不会等待所有任务运行结束，因此如果它想在线程池 TERMINATED 后做些事
情，可以利用此方法等待
boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;
```

#####  任务调度线程池

在『任务调度线程池』功能加入之前，可以使用 java.util.Timer 来实现定时功能，Timer 的优点在于简单易用，但 由于所有任务都是由同一个线程来调度，因此所有任务都是串行执行的，同一时间只能有一个任务在执行，前一个 任务的延迟或异常都将会影响到之后的任务。

```java
 public static void main(String[] args) {
Timer timer = new Timer();
        TimerTask task1 = new TimerTask() {
            @Override
            public void run() {
                log.debug("task 1");
                sleep(2);
            }
        };
        TimerTask task2 = new TimerTask() {
            @Override
            public void run() {
                log.debug("task 2");
            }
        };
        // 使用 timer 添加两个任务，希望它们都在 1s 后执行
        // 但由于 timer 内只有一个线程来顺序执行队列中的任务，因此『任务1』的延时，影响了『任务2』的执行
        timer.schedule(task1, 1000);
        timer.schedule(task2, 1000);
 }
```

使用 ScheduledExecutorService 改写：

```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);
// 添加两个任务，希望它们都在 1s 后执行
executor.schedule(() -> {
    System.out.println("任务1，执行时间：" + new Date());
    try { Thread.sleep(2000); } catch (InterruptedException e) { }
}, 1000, TimeUnit.MILLISECONDS);
executor.schedule(() -> {
    System.out.println("任务2，执行时间：" + new Date());
}, 1000, TimeUnit.MILLISECONDS);
```

scheduleAtFixedRate 例子（任务执行时间超过了间隔时间）：

```java
ScheduledExecutorService pool = Executors.newScheduledThreadPool(1);
log.debug("start...");
pool.scheduleAtFixedRate(() -> {
    log.debug("running...");
    sleep(2);
}, 1, 1, TimeUnit.SECONDS);
```

#####  正确处理执行任务异常

方法1：主动捉异常

```java
ExecutorService pool = Executors.newFixedThreadPool(1);
pool.submit(() -> {
    try {
        log.debug("task1");
        int i = 1 / 0;
    } catch (Exception e) {
        log.error("error:", e);
    }
});
```

方法2：使用 Future

```java
ExecutorService pool = Executors.newFixedThreadPool(1);
Future<Boolean> f = pool.submit(() -> {
    log.debug("task1");
    int i = 1 / 0;
    return true;
});
log.debug("result:{}", f.get());
```

#### Fork/Join

##### 概念

Fork/Join 是 JDK 1.7 加入的新的线程池实现，它体现的是一种分治思想，适用于能够进行任务拆分的 cpu 密集型运算 

所谓的任务拆分，是将一个大任务拆分为算法上相同的小任务，直至不能拆分可以直接求解。跟递归相关的一些计 算，如归并排序、斐波那契数列、都可以用分治思想进行求解 

Fork/Join 在分治的基础上加入了多线程，可以把每个任务的分解和合并交给不同的线程来完成，进一步提升了运算效率 

Fork/Join 默认会创建与 cpu 核心数大小相同的线程池

##### 使用

提交给 Fork/Join线程池的任务需要继承RecursiveTask(有返回值)或RecursiveAction(没有返回值)，例如下 面定义了一个对1~n 之间的整数求和的任务

```java
import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ForkJoinTest {
    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool(4);
        System.out.println(pool.invoke(new AddTask1(5)));
    }

}


@Slf4j(topic = "c.AddTask1")
class AddTask1 extends RecursiveTask<Integer> {
    int n;
    public AddTask1(int n) {
        this.n = n;
    }
    @Override
    public String toString() {
        return "{" + n + '}';
    }
    @Override
    protected Integer compute() {
        // 如果 n 已经为 1，可以求得结果了
        if (n == 1) {
            log.debug("join() {}", n);
            return n;
        }

        // 将任务进行拆分(fork)
        AddTask1 t1 = new AddTask1(n - 1);
        t1.fork();
        log.debug("fork() {} + {}", n, t1);

        // 合并(join)结果
        int result = n + t1.join();
        log.debug("join() {} + {} = {}", n, t1 result);
        return result;
    }
```

![image-20220908174744319](并发编程(JUC).assets/image-20220908174744319.png)

优化

```java
import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ForkJoinTest {
    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool(4);
        System.out.println(pool.invoke(new AddTask3(1,5)));
    }

}

@Slf4j(topic = "c.AddTask3")
class AddTask3 extends RecursiveTask<Integer> {

    int begin;
    int end;
    public AddTask3(int begin, int end) {
        this.begin = begin;
        this.end = end;
    }
    @Override
    public String toString() {
        return "{" + begin + "," + end + '}';
    }
    @Override
    protected Integer compute() {
        // 5, 5
        if (begin == end) {
            log.debug("join() {}", begin);
            return begin;
        }
        // 4, 5
        if (end - begin == 1) {
            log.debug("join() {} + {} = {}", begin, end, end + begin);
            return end + begin;
        }

        // 1 5
        int mid = (end + begin) / 2; // 3
        AddTask3 t1 = new AddTask3(begin, mid); // 1,3
        t1.fork();
        AddTask3 t2 = new AddTask3(mid + 1, end); // 4,5
        t2.fork();
        log.debug("fork() {} + {} = ?", t1, t2);
        int result = t1.join() + t2.join();
        log.debug("join() {} + {} = {}", t1, t2, result);
        return result;
    }
}
```

![image-20220908175039634](并发编程(JUC).assets/image-20220908175039634.png)

### JUC

#### AQS

```java
java.util.concurrent.locks public abstract class AbstractQueuedSynchronizer
extends AbstractOwnableSynchronizer
implements java.io.Serializable
提供一个框架来实现依赖于先进先出 (FIFO) 等待队列的阻塞锁和相关的同步器（信号量、事件等）。此类旨在为大多数依赖单个原子int值来表示状态的同步器提供有用的基础。子类必须定义更改此状态的受保护方法，并定义该状态在获取或释放此对象方面的含义。鉴于这些，此类中的其他方法执行所有排队和阻塞机制。子类可以维护其他状态字段，但只有使用getState 、 setState和compareAndSetState方法操作的原子更新的int值会在同步方面被跟踪。
子类应定义为非公共内部帮助类，用于实现其封闭类的同步属性。 AbstractQueuedSynchronizer类不实现任何同步接口。相反，它定义了诸如acquireInterruptibly之类的方法，具体锁和相关同步器可以适当地调用这些方法来实现它们的公共方法。
此类支持默认独占模式和共享模式中的一种或两种。以独占模式获取时，其他线程尝试获取时不会成功。多个线程的共享模式获取可能（但不一定）成功。此类不“理解”这些差异，除非在机械意义上，当共享模式获取成功时，下一个等待线程（如果存在）也必须确定它是否也可以获取。不同模式下等待的线程共享同一个FIFO队列。通常，实现子类只支持其中一种模式，但两者都可以发挥作用，例如在ReadWriteLock中。只支持独占或只支持共享模式的子类不需要定义支持未使用模式的方法。
该类定义了一个嵌套的AbstractQueuedSynchronizer.ConditionObject类，该类可以被支持独占模式的子类用作Condition实现方法isHeldExclusively报告同步是否相对于当前线程独占，使用当前getState值调用的方法release完全释放这个对象和acquire ，给定这个保存的状态值，最终将此对象恢复到其先前获取的状态。没有AbstractQueuedSynchronizer方法否则会创建这样的条件，因此如果无法满足此约束，请不要使用它。AbstractQueuedSynchronizer.ConditionObject的行为当然取决于其同步器实现的语义。
此类为内部队列提供检查、检测和监视方法，以及用于条件对象的类似方法。这些可以根据需要导出到类中，使用AbstractQueuedSynchronizer用于它们的同步机制。
此类的序列化仅存储底层原子整数维护状态，因此反序列化对象具有空线程队列。需要可序列化的典型子类将定义一个readObject方法，该方法在反序列化时将其恢复到已知的初始状态。
用法
要将此类用作同步器的基础，请在适用时重新定义以下方法，方法是使用getState 、 setState和/或compareAndSetState检查和/或修改同步状态：
tryAcquire
tryRelease
tryAcquireShared
tryReleaseShared
isHeldExclusively
默认情况下，这些方法中的每一个都会抛出UnsupportedOperationException 。这些方法的实现必须是内部线程安全的，并且通常应该是短的而不是阻塞的。定义这些方法是使用此类的唯一受支持的方法。所有其他方法都被声明为final方法，因为它们不能独立变化。
您可能还会发现从AbstractOwnableSynchronizer继承的方法对于跟踪拥有独占同步器的线程很有用。鼓励您使用它们——这使监视和诊断工具能够帮助用户确定哪些线程持有锁。
即使此类基于内部 FIFO 队列，它也不会自动强制执行 FIFO 获取策略。独占同步的核心形式为：
   Acquire:
       while (!tryAcquire(arg)) {
          enqueue thread if it is not already queued;
          possibly block current thread;
       }

   Release:
       if (tryRelease(arg))
          unblock the first queued thread;

（共享模式类似，但可能涉及级联信号。）
因为在入队之前调用了获取中的检查，所以新获取的线程可能会抢在其他被阻塞和排队的线程之前。但是，如果需要，您可以定义tryAcquire和/或tryAcquireShared以通过内部调用一个或多个检查方法来禁用插入，从而提供公平的 FIFO 获取顺序。特别是，大多数公平同步器可以定义tryAcquire以在hasQueuedPredecessors （一种专门为公平同步器使用的方法）返回true时返回false 。其他变化是可能的。
对于默认的 barging（也称为greedy 、 renouncement和convoy-avoidance ）策略，吞吐量和可扩展性通常最高。虽然这不能保证公平或无饥饿，但允许较早排队的线程在稍后排队的线程之前重新竞争，并且每次重新竞争都有成功对抗传入线程的无偏机会。此外，虽然获取不是通常意义上的“旋转”，但它们可能会在阻塞之前执行多次调用tryAcquire并穿插其他计算。当独占同步只是短暂地保持时，这提供了自旋的大部分好处，而没有大部分责任。如果需要，您可以通过预先调用获取具有“快速路径”检查的方法来增加这一点，可能会预先检查hasContended和/或hasQueuedThreads以仅在可能不会竞争同步器时才这样做。
此类通过将其使用范围专门用于可以依赖int状态、获取和释放参数以及内部 FIFO 等待队列的同步器，部分地为同步提供了高效且可扩展的基础。如果这还不够，您可以使用atomic类、您自己的自定义java.util.Queue类和LockSupport阻塞支持从较低级别构建同步器。
使用示例
这是一个不可重入互斥锁类，它使用值 0 表示解锁状态，使用值 1 表示锁定状态。虽然不可重入锁并不严格要求记录当前所有者线程，但无论如何，此类都会这样做以使使用情况更易于监控。它还支持条件并公开一种检测方法：

 class Mutex implements Lock, java.io.Serializable {

   // Our internal helper class
   private static class Sync extends AbstractQueuedSynchronizer {
     // Reports whether in locked state
     protected boolean isHeldExclusively() {
       return getState() == 1;
     }

     // Acquires the lock if state is zero
     public boolean tryAcquire(int acquires) {
       assert acquires == 1; // Otherwise unused
       if (compareAndSetState(0, 1)) {
         setExclusiveOwnerThread(Thread.currentThread());
         return true;
       }
       return false;
     }
    
     // Releases the lock by setting state to zero
     protected boolean tryRelease(int releases) {
       assert releases == 1; // Otherwise unused
       if (getState() == 0) throw new IllegalMonitorStateException();
       setExclusiveOwnerThread(null);
       setState(0);
       return true;
     }
    
     // Provides a Condition
     Condition newCondition() { return new ConditionObject(); }
    
     // Deserializes properly
     private void readObject(ObjectInputStream s)
         throws IOException, ClassNotFoundException {
       s.defaultReadObject();
       setState(0); // reset to unlocked state
     }

   }

   // The sync object does all the hard work. We just forward to it.
   private final Sync sync = new Sync();

   public void lock()                { sync.acquire(1); }
   public boolean tryLock()          { return sync.tryAcquire(1); }
   public void unlock()              { sync.release(1); }
   public Condition newCondition()   { return sync.newCondition(); }
   public boolean isLocked()         { return sync.isHeldExclusively(); }
   public boolean hasQueuedThreads() { return sync.hasQueuedThreads(); }
   public void lockInterruptibly() throws InterruptedException {
     sync.acquireInterruptibly(1);
   }
   public boolean tryLock(long timeout, TimeUnit unit)
       throws InterruptedException {
     return sync.tryAcquireNanos(1, unit.toNanos(timeout));
   }
 }
这是一个类似于CountDownLatch的锁存器类，只是它只需要一个signal即可触发。因为锁存器是非独占的，所以它使用shared的获取和释放方法。

 class BooleanLatch {

   private static class Sync extends AbstractQueuedSynchronizer {
     boolean isSignalled() { return getState() != 0; }

     protected int tryAcquireShared(int ignore) {
       return isSignalled() ? 1 : -1;
     }
    
     protected boolean tryReleaseShared(int ignore) {
       setState(1);
       return true;
     }

   }

   private final Sync sync = new Sync();
   public boolean isSignalled() { return sync.isSignalled(); }
   public void signal()         { sync.releaseShared(1); }
   public void await() throws InterruptedException {
     sync.acquireSharedInterruptibly(1);
   }
 }
自从：
1.5
  < 1.8 >
```

