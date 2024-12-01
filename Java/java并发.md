



# java并发

## 1. 进程与线程

**进程**

```
程序由指令和数据组成，但这些指令要运行，数据要读写，就必须将指令加载至 CPU，数据加载至内存。在
指令运行过程中还需要用到磁盘、网络等设备。进程就是用来加载指令、管理内存、管理 IO 的
当一个程序被运行，从磁盘加载这个程序的代码至内存，这时就开启了一个进程。
进程就可以视为程序的一个实例。大部分程序可以同时运行多个实例进程（例如记事本、画图、浏览器
等），也有的程序只能启动一个实例进程（例如网易云音乐、360 安全卫士等）
```

**线程**

```
一个进程之内可以分为一到多个线程。
一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给 CPU 执行
Java 中，线程作为最小调度单位，进程作为资源分配的最小单位。 在 windows 中进程是不活动的，只是作
为线程的容器
```

**二者对比**

```
进程基本上相互独立的，而线程存在于进程内，是进程的一个子集
进程拥有共享的资源，如内存空间等，供其内部的线程共享
进程间通信较为复杂
同一台计算机的进程通信称为 IPC（Inter-process communication）
不同计算机之间的进程通信，需要通过网络，并遵守共同的协议，例如 HTTP
线程通信相对简单，因为它们共享进程内的内存，一个例子是多个线程可以访问同一个共享变量
线程更轻量，线程上下文切换成本一般上要比进程上下文切换低
```

### **并发并行**

```
并发（concurrent）是同一时间应对（dealing with）多件事情的能力
并行（parallel）是同一时间动手做（doing）多件事情的能力
```

### 同步异步

```
以调用方角度来讲，如果
需要等待结果返回，才能继续运行就是同步
不需要等待结果返回，就能继续运行就是异步
```

```
1. 单核 cpu 下，多线程不能实际提高程序运行效率，只是为了能够在不同的任务之间切换，不同线程轮流使用
cpu ，不至于一个线程总占用 cpu，别的线程没法干活
2. 多核 cpu 可以并行跑多个线程，但能否提高程序运行效率还是要分情况的
有些任务，经过精心设计，将任务拆分，并行执行，当然可以提高程序的运行效率。但不是所有计算任
务都能拆分（参考后文的【阿姆达尔定律】）
也不是所有任务都需要拆分，任务的目的如果不同，谈拆分和效率没啥意义
3. IO 操作不占用 cpu，只是我们一般拷贝文件使用的是【阻塞 IO】，这时相当于线程虽然不用 cpu，但需要一
直等待 IO 结束，没能充分利用线程。所以才有后面的【非阻塞 IO】和【异步 IO】优化
```

### Java线程

方法一、继承 Thread类

```java
public class Create01 extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("子线程"+i);
        }
    }
}
Create01 c01=new Create01();
        c01.start();
        for (int i = 0; i < 100; i++) {
            System.out.println("主线程"+i);
        }
```

方法二、实现Runnable 接口

```java
public class Create02 implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("子线程"+i);
        }
    }
}
Create02 c02=new Create02();
        Thread thread=new Thread(c02);
        thread.start();
        for (int i = 0; i < 100; i++) {
            System.out.println("主线程"+i);
        }
```

原理之 Thread 与 Runnable 的关系

```
分析 Thread 的源码，理清它与 Runnable 的关系
小结
方法1 是把线程和任务合并在了一起，方法2 是把线程和任务分开了
用 Runnable 更容易与线程池等高级 API 配合
用 Runnable 让任务类脱离了 Thread 继承体系，更灵活
```

方法三，FutureTask 配合 Thread

```java
// 创建任务对象
FutureTask<Integer> task3 = new FutureTask<>(() -> {
log.debug("hello");
return 100;
});
// 参数1 是任务对象; 参数2 是线程名字，推荐
new Thread(task3, "t3").start();
// 主线程阻塞，同步等待 task 执行完毕的结果
Integer result = task3.get();
log.debug("结果是:{}", result);
```

### 查看进程线程的方法

**windows**

- 任务管理器可以查看进程和线程数，也可以用来杀死进程

- tasklist 查看进程

- taskkill 杀死进程

**linux**

-   ps -fe 查看所有进程

- ps -fT -p <PID> 查看某个进程（PID）的所有线程

- kill 杀死进程

- top 按大写 H 切换是否显示线程

- top -H -p <PID> 查看某个进程（PID）的所有线程

**Java**

-   jps 命令查看所有 Java 进程

-  jstack <PID> 查看某个 Java 进程（PID）的所有线程状态
-   jconsole 来查看某个 Java 进程中线程的运行情况（图形界面）

**线程上下文切换（Thread Context Switch）**

因为以下一些原因导致 cpu 不再执行当前的线程，转而执行另一个线程的代码

- 线程的 cpu 时间片用完
- 垃圾回收
- 有更高优先级的线程需要运行
- 线程自己调用了 sleep、yield、wait、join、park、synchronized、lock 等方法

当 Context Switch 发生时，需要由操作系统保存当前线程的状态，并恢复另一个线程的状态，Java 中对应的概念
就是程序计数器（Program Counter Register），它的作用是记住下一条 jvm 指令的执行地址，是线程私有的

- 状态包括程序计数器、虚拟机栈中每个栈帧的信息，如局部变量、操作数栈、返回地址等
- Context Switch 频繁发生会影响性能多路复用

### 线程状态

<img src="concurrent\线程状态.png" style="zoom: 50%;" />

```
线程休眠 Thread.sleep(1000)
线程放弃 Thread.yield()
加入 对象.join() 直到加入线程执行完毕
设置线程优先级 对象.setPriority
```

java六种状态

```
NEW 线程刚被创建，但是还没有调用 start() 方法
RUNNABLE 当调用了 start() 方法之后，注意，Java API 层面的 RUNNABLE 状态涵盖了 操作系统 层面的
【可运行状态】、【运行状态】和【阻塞状态】（由于 BIO 导致的线程阻塞，在 Java 里无法区分，仍然认为
是可运行）
BLOCKED ， WAITING ， TIMED_WAITING 都是 Java API 层面对【阻塞状态】的细分
TERMINATED 当线程代码运行结束
```

start和run方法

```
直接调用 run 是在主线程中执行了 run，没有启动新的线程
使用 start 是启动新的线程，通过新的线程间接执行 run 中的代码
```

sleep 与 yield

```
sleep
1. 调用 sleep 会让当前线程从 Running 进入 Timed Waiting 状态（阻塞）
2. 其它线程可以使用 interrupt 方法打断正在睡眠的线程，这时 sleep 方法会抛出 InterruptedException
3. 睡眠结束后的线程未必会立刻得到执行
4. 建议用 TimeUnit 的 sleep 代替 Thread 的 sleep 来获得更好的可读性
yield
1. 调用 yield 会让当前线程从 Running 进入 Runnable 就绪状态，然后调度执行其它线程
2. 具体的实现依赖于操作系统的任务调度器
```

线程优先级

- 线程优先级会提示（hint）调度器优先调度该线程，但它仅仅是一个提示，调度器可以忽略它
- 如果 cpu 比较忙，那么优先级高的线程会获得更多的时间片，但 cpu 闲时，优先级几乎没作用

join

```java
static int r = 0;
public static void main(String[] args) throws InterruptedException {
test1();
}
private static void test1() throws InterruptedException {
log.debug("开始");
Thread t1 = new Thread(() -> {
log.debug("开始");
sleep(1);
log.debug("结束");
r = 10;
});
t1.start();
log.debug("结果为:{}", r);
log.debug("结束");
}
```

```mermaid
graph TD
A[main] -->B(t1.start)
A-->D
    B -->|1s后| C(r=10)
    C -->|t1终止|D[t1.join]
```

多线程同步join

<img src="concurrent\join多线程同步.png" style="zoom:50%;" />

有时效的join

```
join(n) 最多等待n毫秒 线程提前结束，不需要等待那么长时间
```

#### interrupt 方法详解

```
打断 sleep，wait，join 的线程
这几个方法都会让线程进入阻塞状态
打断 sleep 的线程, 会清空打断状态 false
打断正常运行的线程, 不会清空打断状态 true
```

#### 两阶段终止模式

```mermaid
graph TD
    A[(while true)] --> B{有没有被打断?}
    B -- Yes --> C[后续处理]
    C --> D(结束循环)
    B -- No --> E[睡眠 2s]
    E -- Timeout --> F[执行监控记录]
    E -- Exception --> G[设置打断标记]
    F --> A
    G --> A
```

打断 park 线程

```
 park作用暂停当前线程，打断 park 线程, 不会清空打断状态
```

不推荐的方法

```
还有一些不推荐使用的方法，这些方法已过时，容易破坏同步代码块，造成线程死锁
```

```
方法名static   功能说明
stop()        停止线程运行
suspend()     挂起（暂停）线程运行
resume()      恢复线程运行
```

#### 守护线程

```
默认情况下，java中的线程需要等待所有线程运行完毕之后才结束，但是有一种特殊线程叫做守护线程，只要非守护线程运行结束了，即使守护线程没有执行完毕，也会强制结束
```

> 垃圾回收器线程就是一种守护线程
>
> Tomcat中的Acceptor和 Poller线程都是守护线程，所以 Tomcat接收到 shutdown命令之后，不会等待处理完当前请求

### 死锁

```
是指多个进程在运行过程中因争夺资源而造成的一种僵局
```

原因

```
a. 竞争资源

系统中的资源可以分为两类：
可剥夺资源，是指某进程在获得这类资源后，该资源可以再被其他进程或系统剥夺，CPU和主存均属于可剥夺性资源；
另一类资源是不可剥夺资源，当系统把这类资源分配给某进程后，再不能强行收回，只能在进程用完后自行释放，如磁带机、打印机等。
产生死锁中的竞争资源之一指的是竞争不可剥夺资源（例如：系统中只有一台打印机，可供进程P1使用，假定P1已占用了打印机，若P2继续要求打印机打印将阻塞）
产生死锁中的竞争资源另外一种资源指的是竞争临时资源（临时资源包括硬件中断、信号、消息、缓冲区内的消息等），通常消息通信顺序进行不当，则会产生死锁

b. 进程间推进顺序非法

若P1保持了资源R1,P2保持了资源R2，系统处于不安全状态，因为这两个进程再向前推进，便可能发生死锁
例如，当P1运行到P1：Request（R2）时，将因R2已被P2占用而阻塞；当P2运行到P2：Request（R1）时，也将因R1已被P1占用而阻塞，于是发生进程死锁
```

死锁产生的4个必要条件

```
1. 互斥条件：进程要求对所分配的资源进行排它性控制，即在一段时间内某资源仅为一进程所占用。
2. 请求和保持条件：当进程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件：进程已获得的资源在未使用完之前，不能剥夺，只能在使用完时由自己释放。
4. 环路等待条件：在发生死锁时，必然存在一个进程--资源的环形链。
```

预防死锁：

- 资源一次性分配：一次性分配所有资源，这样就不会再有请求了：（破坏请求条件）
- 只要有一个资源得不到分配，也不给这个进程分配其他的资源：（破坏请保持条件）
- 可剥夺资源：即当某进程获得了部分资源，但得不到其它资源，则释放已占有的资源（破坏不可剥夺条件）
- 资源有序分配法：系统给每类资源赋予一个编号，每一个进程按编号递增的顺序请求资源，释放则相反（破坏环路等待条件）

避免死锁:

- 预防死锁的几种策略，会严重地损害系统性能。因此在避免死锁时，要施加较弱的限制，从而获得 较满意的系统性能。由于在避免死锁的策略中，允许进程动态地申请资源。因而，系统在进行资源分配之前预先计算资源分配的安全性。若此次分配不会导致系统进入不安全的状态，则将资源分配给进程；否则，进程等待。其中最具有代表性的避免死锁算法是银行家算法。
- 银行家算法：首先需要定义状态和安全状态的概念。系统的状态是当前给进程分配的资源情况。因此，状态包含两个向量Resource（系统中每种资源的总量）和Available（未分配给进程的每种资源的总量）及两个矩阵Claim（表示进程对资源的需求）和Allocation（表示当前分配给进程的资源）。安全状态是指至少有一个资源分配序列不会导致死锁。当进程请求一组资源时，假设同意该请求，从而改变了系统的状态，然后确定其结果是否还处于安全状态。如果是，同意这个请求；如果不是，阻塞该进程知道同意该请求后系统状态仍然是安全的。

银行家算法

![](concurrent\银行家算法.png)

检测死锁

1. 首先为每个进程和每个资源指定一个唯一的号码；
2. 然后建立资源分配表和进程等待表。

解除死锁:

当发现有进程死锁后，便应立即把它从死锁状态中解脱出来，常采用的方法有：

- 剥夺资源：从其它进程剥夺足够数量的资源给死锁进程，以解除死锁状态；
- 撤消进程：可以直接撤消死锁进程或撤消代价最小的进程，直至有足够的资源可用，死锁状态.消除为止；所谓代价是指优先级、运行代价、进程的重要性和价值等。

## 2.共享模型之管程

### synchronized

为了避免临界区的竞态条件发生，有多种手段可以达到目的。

- 阻塞式的解决方案：synchronized，Lock
- 非阻塞式的解决方案：原子变量

```
synchronized即俗称的【对象锁】，它采用互斥的方式让同一时刻至多只有一个线程能持有【对象锁】，其它线程再想获取这个【对象锁】时就会阻塞住。这样就能保证拥有锁的线程可以安全的执行临界区内的代码，不用担心线程上下文切换
注意
虽然 java 中互斥和同步都可以采用 synchronized 关键字来完成，但它们还是有区别的：
1. 互斥是保证临界区的竞态条件发生，同一时刻只能有一个线程执行临界区代码
2. 同步是由于线程执行的先后、顺序不同、需要一个线程等待其它线程运行到某个点
```

```
synchronized(对象) 中的对象，可以想象为一个房间（room），有唯一入口（门）房间只能一次进入一人
进行计算，线程 t1，t2 想象成两个人
当线程 t1 执行到 synchronized(room) 时就好比 t1 进入了这个房间，并锁住了门拿走了钥匙，在门内执行
count++ 代码
这时候如果 t2 也运行到了 synchronized(room) 时，它发现门被锁住了，只能在门外等待，发生了上下文切
换，阻塞住了
这中间即使 t1 的 cpu 时间片不幸用完，被踢出了门外（不要错误理解为锁住了对象就能一直执行下去哦），
这时门还是锁住的，t1 仍拿着钥匙，t2 线程还在阻塞状态进不来，只有下次轮到 t1 自己再次获得时间片时才
能开门进入
当 t1 执行完 synchronized{} 块内的代码，这时候才会从 obj 房间出来并解开门上的锁，唤醒 t2 线程把钥
匙给他。t2 线程这时才可以进入 obj 房间，锁住了门拿上钥匙，执行它的 count-- 代码
```

```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

@Slf4j(topic = "c.Test")
public class MutliDemo {
    static int count = 0;
    static final Object lock = new Object();

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(()->{
           for (int i = 0; i < 10000; i++) {
               synchronized (lock) {
                   count++;
               }
           }
        });
        Thread t2 = new Thread(()->{
            for (int i = 0; i < 10000; i++) {
                synchronized (lock){
                    count--;
                }
            }
        });
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        log.info("count:{}",count);
    }
}
```

方法上的 synchronized

```JAVA
class Test{
public synchronized void test() {
}
}
等价于
class Test{
public void test() {
synchronized(this) {
}
}
}
```

```java
class Test{
public synchronized static void test() {
}
}
等价于
class Test{
public static void test() {
synchronized(Test.class) {
}
}
}
```

### 线程安全分析

成员变量和静态变量是否线程安全？

- 如果它们没有共享，则线程安全
- 如果它们被共享了，根据它们的状态是否能够改变，又分两种情况
  - 如果只有读操作，则线程安全
  - 如果有读写操作，则这段代码是临界区，需要考虑线程安全

局部变量是否线程安全？

- 局部变量是线程安全的
- 但局部变量引用的对象则未必
  - 如果该对象没有逃离方法的作用访问，它是线程安全的	
  - 如果该对象逃离方法的作用范围，需要考虑线程安全

#### 线程安全类

- Sting
- Integer
- StringBuffer
- Random
- Vector
- Hashtable
- Java.util.concurrent包下面的类

这里所说的安全是指，多个线程调用同一个实例的某个方法，是线程安全的



- 每个方法是原子的
- 多个方法的组合不是原子的

#### 不可变类线程安全性

String、Integer等都是不可变类，内部状态不可更改，因此它们的方法都是线程安全的

### Monitor 概念

Java 对象头

普通对象

| Object Header (64 bits) |                      |
| ----------------------- | -------------------- |
| Mark Word (32 bits)     | Klass Word (32 bits) |

数组对象

| Object Header (96 bits) |                    |                      |
| ----------------------- | ------------------ | -------------------- |
| Mark Word(32bits)       | Klass Word(32bits) | array length(32bits) |

Mark Word结构

| Mark Word (32 bits)                          | State              |
| -------------------------------------------- | ------------------ |
| hashcode:  25    age:4   biased_lock: 0   01 | Normal             |
| thread:23 epoch:2 age:4 biased_lock:1 01     | Biased             |
| ptr_to_lock_record:30  00                    | Lightweight Locked |
| ptr_to_heavyweight_monitor:30 10             | Heavyweight Locked |
| 11                                           | Marked for GC      |

| Mark Word (64 bits)                                          | State              |
| ------------------------------------------------------------ | ------------------ |
| unused：25 hashcode:  31    unused：1 age:4   biased_lock: 0   01 | Normal             |
| thread:54 epoch:2  unused：1 age:4 biased_lock:1 01          | Biased             |
| ptr_to_lock_record:62  00                                    | Lightweight Locked |
| ptr_to_heavyweight_monitor:62 10                             | Heavyweight Locked |
| 11                                                           | Marked for GC      |

原理

每个 Java 对象都可以关联一个 Monitor 对象，如果使用 synchronized 给对象上锁（重量级）之后，该对象头的
Mark Word 中就被设置指向 Monitor 对象的指针

<img src="concurrent\monitor.png" style="zoom: 67%;" />

- 刚开始 Monitor 中 Owner 为 null
- 当 Thread-2 执行 synchronized(obj) 就会将 Monitor 的所有者 Owner 置为 Thread-2，Monitor中只能有一
  个 Owner·
- 在 Thread-2 上锁的过程中，如果 Thread-3，Thread-4，Thread-5 也来执行 synchronized(obj)，就会进入
  EntryList BLOCKED
- Thread-2 执行完同步代码块的内容，然后唤醒 EntryList 中等待的线程来竞争锁，竞争的时是非公平的
- 图中 WaitSet 中的 Thread-0，Thread-1 是之前获得过锁，但条件不满足进入 WAITING 状态的线程，后面讲
  wait-notify 时会分析

```
synchronized 必须是进入同一个对象的 monitor 才有上述的效果
不加 synchronized 的对象不会关联监视器，不遵从以上规则
```

### 轻量级锁

```
轻量级锁的使用场景：如果一个对象虽然有多线程要加锁，但加锁的时间是错开的（也就是没有竞争），那么可以
使用轻量级锁来优化。
轻量级锁对使用者是透明的，即语法仍然是 synchronized
假设有两个方法同步块，利用同一个对象加锁
```

```java
static final Object obj = new Object();
public static void method1() {
synchronized( obj ) {
// 同步块 A
method2();
}
}
public static void method2() {
synchronized( obj ) {
// 同步块 B
}
}
```

- 创建锁记录（Lock Record）对象，每个线程都的栈帧都会包含一个锁记录的结构，内部可以存储锁定对象的
  Mark Word

<img src="concurrent\轻量级锁1.png" style="zoom:67%;" />

- 让锁记录中 Object reference 指向锁对象，并尝试用 cas 替换 Object 的 Mark Word，将 Mark Word 的值存
  入锁记录

<img src="concurrent\轻量级锁2.png" style="zoom:67%;" />

- 如果 cas 替换成功，对象头中存储了锁记录地址和状态 00 ，表示由该线程给对象加锁，这时图示如下

<img src="concurrent\轻量级锁3.png" style="zoom:67%;" />

- 如果 cas 失败，有两种情况
  如果是其它线程已经持有了该 Object 的轻量级锁，这时表明有竞争，进入锁膨胀过程
  如果是自己执行了 synchronized 锁重入，那么再添加一条 Lock Record 作为重入的计数

<img src="concurrent\轻量级锁4.png" style="zoom:67%;" />

- 当退出 synchronized 代码块（解锁时）如果有取值为 null 的锁记录，表示有重入，这时重置锁记录，表示重
  入计数减一

<img src="concurrent\轻量级锁5.png" style="zoom:67%;" />

- 当退出 synchronized 代码块（解锁时）锁记录的值不为 null，这时使用 cas 将 Mark Word 的值恢复给对象
  头
  成功，则解锁成功
  失败，说明轻量级锁进行了锁膨胀或已经升级为重量级锁，进入重量级锁解锁流程

### 锁膨胀

```
如果在尝试加轻量级锁的过程中，CAS 操作无法成功，这时一种情况就是有其它线程为此对象加上了轻量级锁（有
竞争），这时需要进行锁膨胀，将轻量级锁变为重量级锁。
```

- 当 Thread-1 进行轻量级加锁时，Thread-0 已经对该对象加了轻量级锁

<img src="concurrent\锁膨胀1.png" style="zoom:67%;" />

- 这时 Thread-1 加轻量级锁失败，进入锁膨胀流程
  即为 Object 对象申请 Monitor 锁，让 Object 指向重量级锁地址
  然后自己进入 Monitor 的 EntryList BLOCKED

<img src="concurrent\锁膨胀2.png" style="zoom:67%;" />

- 当 Thread-0 退出同步块解锁时，使用 cas 将 Mark Word 的值恢复给对象头，失败。这时会进入重量级解锁
  流程，即按照 Monitor 地址找到 Monitor 对象，设置 Owner 为 null，唤醒 EntryList 中 BLOCKED 线程

### 自旋优化

```
重量级锁竞争的时候，还可以使用自旋来进行优化，如果当前线程自旋成功（即这时候持锁线程已经退出了同步
块，释放了锁），这时当前线程就可以避免阻塞。
```

<img src="concurrent\自旋1.png" style="zoom:67%;" />

- 自旋会占用 CPU 时间，单核 CPU 自旋就是浪费，多核 CPU 自旋才能发挥优势。
- 在 Java 6 之后自旋锁是自适应的，比如对象刚刚的一次自旋操作成功过，那么认为这次自旋成功的可能性会
  高，就多自旋几次；反之，就少自旋甚至不自旋，总之，比较智能。
- Java 7 之后不能控制是否开启自旋功能

### 偏向锁

```
轻量级锁在没有竞争时（就自己这个线程），每次重入仍然需要执行 CAS 操作。
Java 6 中引入了偏向锁来做进一步优化：只有第一次使用 CAS 将线程 ID 设置到对象的 Mark Word 头，之后发现
这个线程 ID 是自己的就表示没有竞争，不用重新 CAS。以后只要不发生竞争，这个对象就归该线程所有
```

```java
static final Object obj = new Object();
public static void m1() {
synchronized( obj ) {
// 同步块 A
m2();
}
}
public static void m2() {
synchronized( obj ) {
// 同步块 B
m3();
}
}
public static void m3() {
synchronized( obj ) {
// 同步块 C
}
}
```

<img src="concurrent\偏向锁1.png" style="zoom:67%;" />

<img src="concurrent\偏向锁2.png" style="zoom:67%;" />

**偏向状态**

<img src="concurrent\偏向锁3.png" style="zoom:67%;" />

一个对象创建时：

- 如果开启了偏向锁（默认开启），那么对象创建后，markword 值为 0x05 即最后 3 位为 101，这时它的
  thread、epoch、age 都为 0
- 偏向锁是默认是延迟的，不会在程序启动时立即生效，如果想避免延迟，可以加 VM 参数 -
  XX:BiasedLockingStartupDelay=0 来禁用延迟
- 如果没有开启偏向锁，那么对象创建后，markword 值为 0x01 即最后 3 位为 001，这时它的 hashcode、
  age 都为 0，第一次用到 hashcode 时才会赋值

**撤销** 调用hashCode

```
正常状态对象一开始是没有 hashCode 的，第一次调用才生成
调用了对象的 hashCode，但偏向锁的对象 MarkWord 中存储的是线程 id，如果调用 hashCode 会导致偏向锁被
撤销
轻量级锁会在锁记录中记录 hashCode
重量级锁会在 Monitor 中记录 hashCode
```

**撤销** 其他线程使用偏向锁对象

```
当有其它线程使用偏向锁对象时，会将偏向锁升级为轻量级锁
```

**撤销** wait、notify

**批量重偏向**

```
如果对象虽然被多个线程访问，但没有竞争，这时偏向了线程 T1 的对象仍有机会重新偏向 T2，重偏向会重置对象
的 Thread ID
当撤销偏向锁阈值超过 20 次后，jvm 会这样觉得，我是不是偏向错了呢，于是会在给这些对象加锁时重新偏向至
加锁线程
```

**批量撤销**

```
当撤销偏向锁阈值超过 40 次后，jvm 会这样觉得，自己确实偏向错了，根本就不该偏向。于是整个类的所有对象
都会变为不可偏向的，新建的对象也是不可偏向的
```

```
锁消除优化
锁粗化
对相同对象多次加锁，导致线程发生多次重入，可以使用锁粗化方式来优化，这不同于之前讲的细分锁的粒度
```

### wait/notify

```java
obj.wait() 让进入 object 监视器的线程到 waitSet 等待
obj.notify() 在 object 上正在 waitSet 等待的线程中挑一个唤醒
obj.notifyAll() 让 object 上正在 waitSet 等待的线程全部唤醒

wait() 方法会释放对象的锁，进入 WaitSet 等待区，从而让其他线程就机会获取对象的锁。无限制等待，直到
notify 为止
wait(long n) 有时限的等待, 到 n 毫秒后结束等待，或是被 notify
```

wait和sleep区别

```
1) sleep 是 Thread 方法，而 wait 是 Object 的方法 2) sleep 不需要强制和 synchronized 配合使用，但 wait 需要
和 synchronized 一起用 3) sleep 在睡眠的同时，不会释放对象锁的，但 wait 在等待的时候会释放对象锁 4) 它们
状态 TIMED_WAITING
```

模版

```java
synchronized(lock) {
while(条件不成立) {
lock.wait();
}
// 干活
}
//另一个线程
synchronized(lock) {
lock.notifyAll();
}
```

### 同步模式之保护性暂停

```
有一个结果需要从一个线程传递到另一个线程，让他们关联同一个 GuardedObject
如果有结果不断从一个线程到另一个线程那么可以使用消息队列（见生产者/消费者）
JDK 中，join 的实现、Future 的实现，采用的就是此模式
因为要等待另一方的结果，因此归类到同步模式
```

#### 实现

<img src="concurrent\保护性暂停.png" style="zoom:67%;" />

```java
class GuardObject {
    private Object response;

    public Object get(long timeout) {
        synchronized (this) {
            long start = System.currentTimeMillis();
            long passedTime = 0;
            while (response == null) {
                long waitTime = timeout - passedTime;
                if (waitTime <= 0) {
                    break;
                }
                try {
                    this.wait(waitTime);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                passedTime = System.currentTimeMillis() - start;
            }
            return response;
        }
    }

    public void complete(Object response) {
        synchronized (this) {
            this.response = response;
            this.notifyAll();
        }
    }
}
```

#### 解耦

<img src="../images/protect_stop_2.png" alt="截屏2024-07-10 23.54.05" style="zoom:67%;" />

```java
列表遍历过程中不能进行remove
class People extends Thread{
    @Override
    public void run() {
        GuardObject guardObject = MailBoxs.createGuardObject();
        System.out.println("people start id "+ guardObject.getId());
        Object mail = guardObject.get(5000);
        System.out.println("people end id "+ guardObject.getId()+" mail is "+mail);

    }
}
class PostMan extends Thread {
    private int id;
    private String mail;
    public PostMan(int id, String mail) {
        this.id = id;
        this.mail = mail;
        System.out.println("postman start id "+ id);
    }
    @Override
    public void run() {
        GuardObject guardObject = MailBoxs.getGuardObject(id);
        System.out.println("send mail is "+id+" mail is "+mail);
        guardObject.complete(mail);
    }
}

class MailBoxs {
    private static int id=1;
    private static Map<Integer,GuardObject> map = new Hashtable<>();
    public static GuardObject createGuardObject() {
        GuardObject guardObject = new GuardObject(genID());
        System.out.println("gen id is "+guardObject.getId());
        map.put(guardObject.getId(),guardObject);
            return guardObject;
    }
    public static GuardObject getGuardObject(int id) {
        return map.get(id);
    }
    private static synchronized int genID() {
        return id++;
    }
    public static Set<Integer> getIds() {
        return map.keySet();
    }
}

class GuardObject {
    private int id;
    private Object response;

    // public GuardObject() {
    // }

        public GuardObject(int id) {
        this.id = id;
    }

    public int getId() {
        return id;
    }

    public Object get(long timeout) {
        synchronized (this) {
            long start = System.currentTimeMillis();
            long passedTime = 0;
            while (this.response == null) {
                long waitTime = timeout - passedTime;
                if (waitTime <= 0) {
                    break;
                }
                try {
                    this.wait(waitTime);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                passedTime = System.currentTimeMillis() - start;
            }
            return response;
        }
    }

    public void complete(Object response) {
        synchronized (this) {
            this.response = response;
            this.notifyAll();
        }
    }
}
```

### 异步模式之生产者消费者

要点

- 与保护性暂停不同，不需要产生的结果和消费的线程一一对应
- 消费队列可以用来平衡生产和消费的线程资源
- 生产者仅负责产生数据，不关心数据如何处理，而消费者专心处理结果数据
- 消息队列是有容量限制的，满时不会加入数据，空时不会消耗数据
- JDK 中的各种阻塞队列，就是采用这种模式

![截屏2024-07-15 23.20.13](../images/consumer.png)



### Park/Unpark

它们是 LockSupport 类中的方法

```java
// 暂停当前线程
LockSupport.park();
// 恢复某个线程的运行
LockSupport.unpark(暂停线程对象)
```

与 Object 的 wait & notify 相比

```
1. wait，notify 和 notifyAll 必须配合 Object Monitor 一起使用，而 park，unpark 不必
2. park & unpark 是以线程为单位来【阻塞】和【唤醒】线程，而 notify 只能随机唤醒一个等待线程，notifyAll
是唤醒所有等待线程，就不那么【精确】
3. park & unpark 可以先 unpark，而 wait & notify 不能先 notify
```

### 线程状态

<img src="../images/截屏2024-07-24 23.16.09.png" alt="截屏2024-07-24 23.16.09" style="zoom:67%;" />

假设有线程Theat t

#### 情况 1 NEW -》RUNNABLE

- 当调用t.start()方法是，由NEW -> RUNNABLE

#### 情况 2 RUNNABLE 《--》WAITING

t线程用 synchronized(obj)获取对象锁后

- 调用 obj.wait()方法时，线程从 RUNNABLE --》WAITING
- 调用 obj.notify，obj.notifyAll(),t.interrupt()时
  - 竞争锁成功，t线程从WAITING --》 RUNNING
  - 失败 t线程从 WAITING --》BLOCKED

#### 情况 3 RUNNABLE 《--》WAITING

- 当前线程调用t.join()方法时 RUNNABLE --》WAITING
- t 线程结束，或者调用interrupt() WAITING --》 RUNNING

#### 情况 4 RUNNABLE 《--》WAITING

- 调用 park 方法 RUNNABLE --》WAITING
- 调用 unpark 方法 或者调用interrupt() WAITING --》 RUNNING

#### 情况 5 RUNNABLE 《--》TIMED_WAITING

t线程用 synchronized(obj)获取对象锁后

- 调用 obj.wait(long n)方法时，线程从 RUNNABLE --》TIMED_WAITING
- 超过 n 毫秒或调用 obj.notify，obj.notifyAll(),t.interrupt()时
  - 竞争锁成功，t线程从TIMED_WAITING --》 RUNNING
  - 失败 t线程从 TIMED_WAITING --》BLOCKED

#### 情况 6 RUNNABLE 《--》TIMED_WAITING

- 当前线程调用t.join(long n)方法时 RUNNABLE --》TIMED_WAITING
- 超过 n 毫秒或 t 线程结束，或者调用interrupt() WAITING --》 RUNNING

#### 情况 7 RUNNABLE 《--》TIMED_WAITING

- 调用 Thread.sleep(long n) 方法 RUNNABLE --》TIMED_WAITING
- 超时 TIMED_WAITING --》 RUNNING

#### 情况 8 RUNNABLE 《--》TIMED_WAITING

- 调用 parkNanos(long nanos) 或者 parkUntil(long millis)方法 RUNNABLE --》TIMED_WAITING
- 调用 unpark 方法 或者调用interrupt() 或者超市 TIMED_WAITING --》 RUNNING

#### 情况 9 RUNNABLE 《--》BLOCKED

- 获取对象锁失败 t线程从 WAITING --》BLOCKED
- 线程竞争成功 BLOCKED --》 RUNNABLE

#### 情况 10 RUNNABLE 《--》TERMINATED

所有线程运行完毕

### ReentrantLock

相对于 synchronized 它具备如下特点

```
可中断
可以设置超时时间
可以设置为公平锁
支持多个条件变量
```

与 synchronized 一样，都支持可重入

#### 基本语法

```java
// 获取锁
reentrantLock.lock();
try {
	// 临界区
} finally {
	// 释放锁
	reentrantLock.unlock();
}
```

#### 可重入

```
可重入是指同一个线程，如果首次获得这把锁，那么因为它是这把锁的拥有者，因此有权利再次获取这把锁，如果是不可重入锁，那么第二次获得锁时，自己也会被锁挡住
```

```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.ReentrantLock;


@Slf4j(topic = "c.Test")
public class ReentrantLockDemo {
    private static ReentrantLock lock = new ReentrantLock();
    public static void main(String[] args) {
        lock.lock();
        try {
            log.info("main function");
            m1();
        } finally {
            lock.unlock();
        }
    }

    private static void m1() {
        lock.lock();
        try {
            log.info("m1 function");
            m2();
        } finally {
            lock.unlock();
        }
    }

    private static void m2() {
        lock.lock();
        try {
            log.info("m2 function");
        } finally {
            lock.unlock();
        }
    }
}
```

#### 可打断

主动打断，避免死锁

```java
 private static void testInterrupt()  {
        Thread thread = new Thread(() -> {
            try {
                log.info("try get lock");
                lock.lockInterruptibly();
            } catch (Exception e) {
                log.error("get lock error", e);
                log.info("not get lock return");
                return;
            }
            try {
                log.info("get lock");
            } finally {
                lock.unlock();
            }
        },"t1");
        lock.lock();
        thread.start();
        log.info("main interupted thread");
        thread.interrupt();
        System.out.println("testInterrupt end");
    }
```

#### 锁超时

使用方法：tryLock() 不带参数，表示尝试获取锁，立马返回结果

```java
private static void testTryLock() {
        Thread thread = new Thread(() -> {
            if(!lock.tryLock()) {
                log.info("not get lock");
                return;
            }
            try {
                log.info("get lock");
            } finally {
                lock.unlock();
            }
        }, "thread");
        lock.lock();
        thread.start();
    }
```

tryLock() 带时间参数，表示等待时间，如果获取到了，继续执行，获取不到 return

```java
private static void testTryLockTime() throws InterruptedException {
        Thread thread = new Thread(() -> {
            try {
                if(!lock.tryLock(3, TimeUnit.SECONDS)) {
                    log.info("not get lock");
                    return;
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            try {
                log.info("get lock");
            } finally {
                lock.unlock();
            }
        }, "thread");
        log.info("get lock success");
        lock.lock();
        thread.start();
        Thread.sleep(1000);
        log.info("release lock success");
        lock.unlock();
    }
```

##### 哲学家就餐问题

synchronized,tryLock()

```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.locks.ReentrantLock;

@Slf4j(topic = "c.Main")
public class PhilosopherDemo {
    public static void main(String[] args) {
        Chopstick c1 = new Chopstick("1");
        Chopstick c2 = new Chopstick("2");
        Chopstick c3 = new Chopstick("3");
        Chopstick c4 = new Chopstick("4");
        Chopstick c5 = new Chopstick("5");
        new Philosopher("p1",c1,c2).start();
        new Philosopher("p2",c2,c3).start();
        new Philosopher("p3",c3,c4).start();
        new Philosopher("p4",c4,c5).start();
        new Philosopher("p5",c5,c1).start();
    }
}


@Slf4j(topic = "c.Philosopher")
class Philosopher extends Thread {
    Chopstick left;
    Chopstick right;

    public Philosopher(String name, Chopstick left, Chopstick right) {
        super(name);
        this.left = left;
        this.right = right;
    }

    // @Override
    // public void run() {
    //    while (true) {
    //        synchronized (left) {
    //            synchronized (right) {
    //                eat();
    //            }
    //        }
    //    }
    //}
    @Override
      public void run() {
          while (true) {
              if (left.tryLock()) {
                  try {
                      if (right.tryLock()) {
                          try {
                              eat();
                          } finally {
                              right.unlock();
                          }
                      }
                  } finally {
                      left.unlock();
                  }
              }
          }
      }

    private void eat() {
        log.info("eat.....");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class Chopstick extends ReentrantLock {
    String name;
    public Chopstick(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Chopstick{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

#### 锁公平

```java
    /**
     * Creates an instance of {@code ReentrantLock}.
     * This is equivalent to using {@code ReentrantLock(false)}.
     */
    public ReentrantLock() {
        sync = new NonfairSync();
    }

    /**
     * Creates an instance of {@code ReentrantLock} with the
     * given fairness policy.
     *
     * @param fair {@code true} if this lock should use a fair ordering policy
     */
    public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```

#### 条件变量

synchronized 中也有条件变量，就是我们讲原理时那个 waitSet 休息室，当条件不满足时进入 waitSet 等待

ReentrantLock 的条件变量比 synchronized 强大之处在于，它是支持多个条件变量的，这就好比

- synchronized 是那些不满足条件的线程都在一间休息室等消息

- 而 ReentrantLock 支持多间休息室，有专门等烟的休息室、专门等早餐的休息室、唤醒时也是按休息室来唤

  醒

使用要点：

- await 前需要获得锁
- await 执行后，会释放锁，进入 conditionObject 等待
- await 的线程被唤醒（或打断、或超时）取重新竞争 lock 锁
- 竞争 lock 锁成功后，从 await 后继续执行

```java
package com.ecnu.concurrent;


import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;



@Slf4j(topic = "c.Test")
public class WaitDemo {
    private static boolean hasA = false;
    private static boolean hasB = false;
    private static ReentrantLock ROOM = new ReentrantLock();
    private static Condition conditionA = ROOM.newCondition();
    private static Condition conditionB = ROOM.newCondition();
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            ROOM.lock();
            try {
                while (!hasA) {  // 防止虚假唤醒，当 conditionA 有多个线程比如 C 被唤醒，无法执行 A
                    log.info("not get A, wait");
                    try {
                        conditionA.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.info("get A, work A");
            } finally {
                ROOM.unlock();
            }
        },"A").start();
        new Thread(() -> {
            ROOM.lock();
            try {
                while (!hasA) {
                    log.info("not get B, wait");
                    try {
                        conditionB.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.info("get B, work B");
            } finally {
                ROOM.unlock();
            }
        },"B").start();
        TimeUnit.SECONDS.sleep(2);
        new Thread(() -> {
            ROOM.lock();
            try {
                hasA = true;
                conditionA.signal();
            } finally {
                ROOM.unlock();
            }
        },"sendA").start();
        TimeUnit.SECONDS.sleep(2);
        new Thread(() -> {
            ROOM.lock();
            try {
                hasB = true;
                conditionB.signal();
            } finally {
                ROOM.unlock();
            }
        },"sendB").start();
    }
}


12:13:38 [A] c.Test - not get A, wait
12:13:38 [B] c.Test - not get B, wait
12:13:40 [A] c.Test - get A, work A
12:13:42 [B] c.Test - get B, work B
```

### 同步模式之顺序控制

#### 固定顺序

##### wait-notify

先打印 2，后打印 1

```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

@Slf4j(topic = "c.Test")
public class OrderDemo {
    private static Object lock = new Object();
    private static boolean runTwoFirst = false;
    public static void main(String[] args) {
        Thread t1 = new Thread(()->{
            synchronized (lock){
                while(!runTwoFirst){
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.info("1");
            }
        },"A");
        Thread t2 = new Thread(()->{
            synchronized (lock){
                runTwoFirst = true;
                log.info("2");
                lock.notify();
            }
        },"B");
        t1.start();
        t2.start();

    }
}
```

##### park unpark

```java
private static void testParkUnpark(){
        Thread t1 = new Thread(()->{
           park();
           log.info("1");
        });
        Thread t2 = new Thread(()->{
           unpark(t1);
           log.info("2");
        });
        t1.start();
        t2.start();
    }
```

#### 交替输出

线程 1 输出a 五次，线程 2 输出 b 五次，线程3 输出 c 五次 abcabcabcabcabc

##### wait notify

```java
package com.ecnu.concurrent;

public class SequenceDemo {
    public static void main(String[] args) {
        WaitNotify waitNotify = new WaitNotify(1,5);
        new Thread(() ->{
            waitNotify.printNumber("a",1,2);
        }).start();
        new Thread(() ->{
            waitNotify.printNumber("b",2,3);
        }).start();
        new Thread(() ->{
            waitNotify.printNumber("c",3,1);
        }).start();
    }
}

class WaitNotify{
    private int flag;
    private int loopNumber;
    public WaitNotify(int flag, int loopNumber){
        this.flag = flag;
        this.loopNumber = loopNumber;
    }
    public void printNumber(String msg, int waitFlag, int nextFlag) {
        for (int i = 0; i < loopNumber; i++) {
            synchronized (this) {
                while (flag != waitFlag) {
                    try {
                        this.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.print(msg);
                flag = nextFlag;
                this.notifyAll();
            }
        }
    }
}

```

##### ReentrantLock

```java
package com.ecnu.concurrent;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class AwaitDemo {
    public static void main(String[] args) throws InterruptedException {
        AwaitSignal awaitSignal = new AwaitSignal(5);
        Condition a = awaitSignal.newCondition();
        Condition b = awaitSignal.newCondition();
        Condition c = awaitSignal.newCondition();
        new Thread(() -> {
            awaitSignal.printNumber("a",a,b);
        }).start();
        new Thread(() -> {
            awaitSignal.printNumber("b",b,c);
        }).start();
        new Thread(() -> {
            awaitSignal.printNumber("c",c,a);
        }).start();
        Thread.sleep(1000);
        awaitSignal.lock();
        try {
            a.signal();
        } finally {
            awaitSignal.unlock();
        }
    }
}
class AwaitSignal extends ReentrantLock {
    private int loopNumber;

    public AwaitSignal(int loopNumber) {
        this.loopNumber = loopNumber;
    }
    public void printNumber(String msg , Condition current, Condition nextCondition) {
        for (int i = 0; i < loopNumber; i++) {
            this.lock();
            try {
                try {
                    current.await();
                    System.out.print(msg);
                    nextCondition.signal();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            } finally {
                this.unlock();
            }
        }
    }
}
```

##### Park unpark

```java
package com.ecnu.concurrent;

import java.util.concurrent.locks.LockSupport;

public class ParkDemo {
    static Thread t1;
    static Thread t2;
    static Thread t3;
    public static void main(String[] args) {
        ParkUnpark parkUnpark = new ParkUnpark(5);
        t1=new Thread(()->{
            parkUnpark.printNumber("a",t2);
        });
        t2=new Thread(()->{
            parkUnpark.printNumber("b",t3);
        });
        t3=new Thread(()->{
            parkUnpark.printNumber("c",t1);
        });
        t1.start();
        t2.start();
        t3.start();
        LockSupport.unpark(t1);
    }
}

class ParkUnpark {
    private int loopNumber;
    public ParkUnpark(int loopNumber) {
        this.loopNumber = loopNumber;
    }
    public void printNumber(String msg, Thread nextthread) {
        for (int i = 0; i < loopNumber; i++) {
            LockSupport.park();
            System.out.print(msg);
            LockSupport.unpark(nextthread);
        }
    }
}

```



## 3. JMM

### 内存模型

JMM 即 Java Memory Model，它定义了主存、工作内存抽象概念，底层对应着 CPU 寄存器、缓存、硬件内存、
CPU 指令优化等。
JMM 体现在以下几个方面

- 原子性 - 保证指令不会受到线程上下文切换的影响
- 可见性 - 保证指令不会受 cpu 缓存的影响
- 有序性 - 保证指令不会受 cpu 指令并行优化的影响

#### 可见性

##### 退不出循环

main 线程对run 变量进行修改，对于 t 线程不可见，导致了 t 线程无法停止

```java
	package com.ecnu.concurrent;

public class VisableDemo {
    static boolean run = true;
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            while (run) {

            }
        });
        t.start();
        Thread.sleep(1000);
        run = false;
    }
}

```

> [!NOTE]
>
> ```
> System.out.println("a");
> ```
>
> 加了 print 打印之后也可以终止循环，原因在于 print 是被 sychronize 修饰

![截屏2024-07-28 00.14.39](../images/截屏2024-07-28 00.14.39.png)

<img src="../images/截屏2024-07-28 00.17.02.png" alt="截屏2024-07-28 00.17.02" style="zoom:67%;" />

<img src="../images/截屏2024-07-28 00.18.42.png" alt="截屏2024-07-28 00.18.42" style="zoom:67%;" />

##### 解决方案

volatile （易变关键字）

```
可以用来修饰成员变量和静态成员变量，可以避免线程从自己的工作缓存中查找变量的值，必须到主存中获取，线程操作 volatile变量都是操作主存， synchronize 也可以
```

#### 可见性和原子性

可见性保证在多个线程之中，一个线程对volatile 变量的修改，对另一个线程可见，不能保证原子性

##### 两阶段终止 volatile

```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

@Slf4j(topic = "c.TwoTest")
public class TwoPhaseTest {
    public static void main(String[] args) throws InterruptedException {
        log.debug("start main");
        // TwoPhaseTermination twoPhaseTermination = new TwoPhaseTermination();
        // twoPhaseTermination.start();
        // Thread.sleep(3500);
        // twoPhaseTermination.stop();

        TwoPhaseTerminationVolatile volatileTwoPhaseTermination = new TwoPhaseTerminationVolatile();
        volatileTwoPhaseTermination.start();
        Thread.sleep(3500);
        volatileTwoPhaseTermination.stop();
    }
}

@Slf4j(topic = "c.volatile")
class TwoPhaseTerminationVolatile {
    private volatile boolean stop = false;
    private Thread thread;

    public void start () {
        thread = new Thread(() -> {
            while (true) {
                if (stop) {
                    log.info("stop is true break");
                    break;
                }
                try {
                    Thread.sleep(1000);
                    log.info("monitor thread");
                } catch (InterruptedException ignored) {
                }

            }
        });
        thread.start();
    }
    public void stop() {
        stop = true;
        thread.interrupt();
    }
}
```

#### 同步模式之Balking

Balking（犹豫）模式用在一个线程发现另一个线程或者本线程已经做了某一件相同的事，那么本线程无需再做，直接结束返回

```java
 private volatile boolean starting = false;

    public void start () {
        synchronized (this) {
            if (starting) {
                return;
            }
            starting = true;
        }
      // 真正启动监控线程
    }
```

#### 有序性

volatile 可以保证共享变量的有序性，防止指令重排序

#### volatile （可见性和防止指令重排）

volatile的底层实现原理是内存屏障，Memory Barrier

- 写指令会加入写屏障
- 读指令会加入读屏障

#### double check lock

饿汉单例双重检验模式

```java
final  class Singleton {
    private Singleton() {
    }
    private static Singleton INSTANCE = null;
    public static Singleton getInstance() {
        if (INSTANCE == null) {
            synchronized (Singleton.class) {
                if (INSTANCE == null) {
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
```

> [!WARNING]
>
> t1 线程发生指令重排，先给 INSTANCE 赋值，还没有调用构造方法，t2 线程判断 INSTANCE 不为空，返回 INSTANCE 对象使用，但是 INSTANCE 对象还没有构造成功 （未初始化完毕的单例）

解决方法

```java
final  class Singleton {
    private Singleton() {
    }
    // private static Singleton INSTANCE = null;
    private static volatile Singleton INSTANCE = null;
    public static Singleton getInstance() {
        if (INSTANCE == null) {
            synchronized (Singleton.class) {
                if (INSTANCE == null) {
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
```

## 4.无锁并发

### CAS

配合 volatile

```java
 while (true) {
            int prev = atomicInteger.get();
            int next = prev + 1;
            if (atomicInteger.compareAndSet(prev, next)) {
                break;
            }
        }
```

### 原子整数

- AtomicBoolean
- AtomicInteger
- AtomicLong

```java
private static void testAtomicInteger() {
        AtomicInteger atomicInteger = new AtomicInteger(0);
        System.out.println(atomicInteger.getAndIncrement()); // i++
        System.out.println(atomicInteger.incrementAndGet()); // ++i
        System.out.println(atomicInteger.getAndAdd(5)); // 2
        System.out.println(atomicInteger.addAndGet(5)); // 12
        System.out.println(atomicInteger.get());
  
  			System.out.println(atomicInteger.updateAndGet(value -> value * 10)); // 120
        System.out.println(atomicInteger.getAndUpdate(value -> value * 10)); // 120
        System.out.println(atomicInteger.get()); // 1200
    }
```

其他操作实现

```java
public final int updateAndGet(IntUnaryOperator updateFunction) {
        int prev = get(), next = 0;
        for (boolean haveNext = false;;) {
            if (!haveNext)
                next = updateFunction.applyAsInt(prev);
            if (weakCompareAndSetVolatile(prev, next))
                return next;
            haveNext = (prev == (prev = get()));
        }
    }
```

### 原子引用

为什么需要原子引用类型? 需要保护其他引用类型数据

- AtomicReference
- AtomicMarkableReference
- AtomicStampedReference

#### ABA 问题

```java
private static void testABA() {
        String prev = atomicReference.get();
        new Thread(()->{
            System.out.println("A -> B is: "+ atomicReference.compareAndSet(atomicReference.get(), "B"));
        }).start();
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(()->{
            System.out.println("B -> A is: "+atomicReference.compareAndSet(atomicReference.get(), "A"));

        }).start();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Main A -> C is: "+atomicReference.compareAndSet(prev, "C"));

    }
```

#### 解决 ABA 问题

```java
private static void testSolveABA() {
        String prev = atomicStampedReference.getReference();
        int stamp = atomicStampedReference.getStamp();
        System.out.println("prev = " + prev + ", stamp = " + stamp);
        new Thread(()->{
            int stampOne  = atomicStampedReference.getStamp();
            System.out.println("stampOne = " + stampOne);
            System.out.println("A -> B is: "+ atomicStampedReference.compareAndSet(atomicStampedReference.getReference(), "B",stampOne,stampOne+1));
        }).start();
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(()->{
            int stampTwo = atomicStampedReference.getStamp();
            System.out.println("stampTwo = " + stampTwo);
            System.out.println("B -> A is: "+atomicStampedReference.compareAndSet(atomicStampedReference.getReference(), "B",stampTwo,stampTwo+1));

        }).start();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Main A -> C is: "+atomicStampedReference.compareAndSet(prev, "C",stamp,stamp+1));
    }
```

### 原子数组

- AtomicIntegerArray;
- AtomicLongArray;
- AtomicIntegerArray

```java
testAtomicArray(
                () -> new int[10],
                (array) -> array.length,
                (array, index) ->array[index]++,
                array -> System.out.println(Arrays.toString(array))
        );
        testAtomicArray(
                () -> new AtomicIntegerArray(10),
                (array) -> array.length(),
                (array, index) -> array.getAndIncrement(index),
                array -> System.out.println(array)
        );

/*
    * 参数一、提供数组、可以是线程不安全数组或者线程安全数组
    * 参数二、获取数组长度的方法
    * 参数三、自增方法，回传array，index
    * 参数四、打印数组的方法
    * */
    // Supplier 提供者 无中生有（）-> 结果
    // Function 函数，一个参数一个结果 （参数）-> 结果， BiFunction (参数 1，参数 2) -> 结果
    // Consumer 消费者 一个参数没结果 （参数）-> void， BiConsumer (参数 1，参数 2) -> void

    private static <T> void testAtomicArray(
            Supplier<T> arraySupplier,
            Function<T, Integer> lengthFun,
            BiConsumer<T, Integer> consumer,
            Consumer<T> printConsumer
    ) {
        List<Thread> threads = new ArrayList<>();
        T array = arraySupplier.get();
        int length = lengthFun.apply(array);
        for (int i = 0; i < length; i++) {
            // 每个线程对数组进行10000次操作
            threads.add(new Thread(() -> {
                for (int j = 0; j < 10000; j++) {
                    consumer.accept(array, j%length);
                }
            }));
        }
        threads.forEach(Thread::start);
        threads.forEach(t -> {
            try {
                t.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        printConsumer.accept(array);

    }

[8718, 8738, 8789, 8706, 8788, 8729, 8765, 8776, 8754, 8713]
[10000, 10000, 10000, 10000, 10000, 10000, 10000, 10000, 10000, 10000]
```

### 字段更新器

- AtomicReferenceFieldUpdater;
- AtomicIntegerFieldUpdater;
- AtomicLongFieldUpdater

利用字段更新器，可以针对对象的某个域（Field）进行原子操作，只能配合volatile修饰的字段使用，否则会出现异常

> [!WARNING]
>
> Exception in thread "main" java.lang.IllegalArgumentException: Must be volatile type

```java
private static void testFiled() {
        Student student = new Student();
        AtomicReferenceFieldUpdater updater = AtomicReferenceFieldUpdater.newUpdater(Student.class, String.class, "name");
        System.out.println(updater.compareAndSet(student, null, "tom"));
        System.out.println(student);
    }

class Student{
    volatile String name;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

### 原子累加

#### LongAdder源码

```java
// 累加单元数组, 懒惰初始化
transient volatile Cell[] cells;
// 基础值, 如果没有竞争, 则用 cas 累加这个域
transient volatile long base;
// 在 cells 创建或扩容时, 置为 1, 表示加锁
transient volatile int cellsBusy;
```

#### CAS 锁

```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.atomic.AtomicInteger;

@Slf4j(topic = "c.Test")
public class LongAdderDemo {
    public static void main(String[] args) {
        testCasLock();
    }

    private static void testCasLock() {
        CasLock casLock = new CasLock();
        new Thread(()->{
            log.info("begin");
            casLock.lock();
            try {
                log.info("lock...");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } finally {
                casLock.unlock();
            }
        }).start();
        new Thread(()->{
            log.info("begin");
            casLock.lock();
            try {
                log.info("lock...");
            } finally {
                casLock.unlock();
            }
        }).start();

    }
}

@Slf4j(topic = "c.Test")
class CasLock {
    // 0 未加锁 1表示加锁
    private AtomicInteger atomicInteger = new AtomicInteger(0);

    public void lock() {
        while (true) {
            if (atomicInteger.compareAndSet(0,1)) {
                break;
            }
        }
    }
    public void unlock() {
        atomicInteger.set(0);
        log.info("unlock");
    }
}
```

#### 原理之伪共享

其中 Cell即为累加单元

```java
// 防止缓存行伪共享
@jdk.internal.vm.annotation.Contended 
	static final class Cell {
        volatile long value;
        Cell(long x) { value = x; }
    		// 最重要的方法，用来 cas 累加，prev 代表旧值，next 表示新值
        final boolean cas(long cmp, long val) {
            return VALUE.weakCompareAndSetRelease(this, cmp, val);
        }
    // 省略不重要的代码
}
```

<img src="../images/截屏2024-07-29 23.11.19.png" alt="截屏2024-07-29 23.11.19" style="zoom:67%;" />

| 从 CPU | 大约需要的时钟周期              |
| ------ | ------------------------------- |
| 寄存器 | 1 cycle （4GHZ的 CPU约为0.2ns） |
| L1     | 3-4 cycle                       |
| L2     | 10-20 cycle                     |
| L3     | 40-45 cycle                     |
| 内存   | 120-240 cycle                   |

因为 CPU与内存的速度差异大，需要预先读取数据到缓存来提升效率

而缓存以缓存行为单位，每个缓存对应一块内存，一般是64 byte （8 个 long）

缓存的加入会造成副本的产生，即一份数据会缓存在不同核心的缓存行中

CPU 要保证数据一致性，如果某个 CPU 更改了数据，其他CPU 核心对应的整个缓存行必须失效

<img src="../images/截屏2024-07-29 23.38.17.png" alt="截屏2024-07-29 23.38.17" style="zoom:67%;" />

因为 Cell 是数组形式，在内存中连续存储，一个 Cell 对象是 24 个字节（16 个字节头+8 个字节的 value）因此缓存行可以存下两个缓存对象，问题来了：

- CPU Core-0要修改 Cell[0]
- CPU Core-1要修改 Cell[1]

无论谁修改成功，都会导致缓存行失效

@jdk.internal.vm.annotation.Contended 用来解决这个问题，原理是在对象后面加入 128 个字节，让 CPU读取到两个缓存行中

<img src="../images/截屏2024-07-29 23.52.48.png" alt="截屏2024-07-29 23.52.48" style="zoom:67%;" />

#### add

```java
    /**
     * Adds the given value.
     *
     * @param x the value to add
     */
    public void add(long x) {
        Cell[] cs; long b, v; int m; Cell c;
        if ((cs = cells) != null || !casBase(b = base, b + x)) {
            int index = getProbe();
            boolean uncontended = true;
            if (cs == null || (m = cs.length - 1) < 0 ||
                (c = cs[index & m]) == null ||
                !(uncontended = c.cas(v = c.value, v + x)))
                longAccumulate(x, null, uncontended, index);
        }
    }
```

<img src="../images/截屏2024-07-29 23.55.30.png" alt="截屏2024-07-29 23.55.30" style="zoom:67%;" />

#### longAccumulate

代码分析

```java
else if (cellsBusy == 0 && cells == cs && casCellsBusy()) {
                try {                           // Initialize table
                    if (cells == cs) {
                        Cell[] rs = new Cell[2];
                        rs[index & 1] = new Cell(x);
                        cells = rs;
                        break;
                    }
                } finally {
                    cellsBusy = 0;
                }
            }
            // Fall back on using base
            else if (casBase(v = base,
                             (fn == null) ? v + x : fn.applyAsLong(v, x)))
                break;
```

![截屏2024-07-30 00.05.59](../images/截屏2024-07-30 00.05.59.png)

```java
if ((c = cs[(n - 1) & index]) == null) {
                    if (cellsBusy == 0) {       // Try to attach new Cell
                        Cell r = new Cell(x);   // Optimistically create
                        if (cellsBusy == 0 && casCellsBusy()) {
                            try {               // Recheck under lock
                                Cell[] rs; int m, j;
                                if ((rs = cells) != null &&
                                    (m = rs.length) > 0 &&
                                    rs[j = (m - 1) & index] == null) {
                                    rs[j] = r;
                                    break;
                                }
                            } finally {
                                cellsBusy = 0;
                            }
                            continue;           // Slot is now non-empty
                        }
                    }
                    collide = false;
                }
```

![截屏2024-07-30 00.09.50](../images/截屏2024-07-30 00.09.50.png)

```java
else if (!wasUncontended)       // CAS already known to fail
                    wasUncontended = true;      // Continue after rehash
                else if (c.cas(v = c.value,
                               (fn == null) ? v + x : fn.applyAsLong(v, x)))
                    break;
                else if (n >= NCPU || cells != cs)
                    collide = false;            // At max size or stale
                else if (!collide)
                    collide = true;
                else if (cellsBusy == 0 && casCellsBusy()) {
                    try {
                        if (cells == cs)        // Expand table unless stale
                            cells = Arrays.copyOf(cs, n << 1);
                    } finally {
                        cellsBusy = 0;
                    }
                    collide = false;
                    continue;                   // Retry with expanded table
                }
                index = advanceProbe(index);
```

![截屏2024-07-30 00.11.51](../images/截屏2024-07-30 00.11.51.png)

#### sum

```java
    /**
     * Returns the current sum.  The returned value is <em>NOT</em> an
     * atomic snapshot; invocation in the absence of concurrent
     * updates returns an accurate result, but concurrent updates that
     * occur while the sum is being calculated might not be
     * incorporated.
     *
     * @return the sum
     */
    public long sum() {
        Cell[] cs = cells;
        long sum = base;
        if (cs != null) {
            for (Cell c : cs)
                if (c != null)
                    sum += c.value;
        }
        return sum;
    }
```

### Unsafe

Unsafe对象提供非常底层，操作内存，线程的方法，Unsafe 对象不能直接调用，只能通过反射

```java
package com.ecnu.concurrent;

import sun.misc.Unsafe;


import java.lang.reflect.Field;

public class UnsafeDemo {
    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        Field  unsafeField = Unsafe.class.getDeclaredField("theUnsafe");
        unsafeField.setAccessible(true);
        Unsafe unsafe = (Unsafe) unsafeField.get(null);
        System.out.println(unsafe);
    }
}

```

#### Unsafe Cas 操作

```java
package com.ecnu.concurrent;

import sun.misc.Unsafe;


import java.lang.reflect.Field;

public class UnsafeDemo {
    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
        Field  unsafeField = Unsafe.class.getDeclaredField("theUnsafe");
        unsafeField.setAccessible(true);
        Unsafe unsafe = (Unsafe) unsafeField.get(null);
        System.out.println(unsafe);

        // 获取偏移地址
        Long idOffset = unsafe.objectFieldOffset(Teacher.class.getDeclaredField("age"));
        Long nameOffset = unsafe.objectFieldOffset(Teacher.class.getDeclaredField("name"));

        Teacher teacher = new Teacher();

        // 执行 cas 操作
        unsafe.compareAndSwapInt(teacher, idOffset, 0, 1);
        unsafe.compareAndSwapObject(teacher,nameOffset,null, "tom");

        // 验证
        System.out.println(teacher);
    }
}

class Teacher {
    volatile int age;
    volatile String name;


    @Override
    public String toString() {
        return "Teacher{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}


sun.misc.Unsafe@85ede7b
Teacher{age=1, name='tom'}
```

Unsafe 模拟 AtomicInteger 实现

```java
package com.ecnu.concurrent.unsafedemo;

import sun.misc.Unsafe;

import java.lang.reflect.Field;

public class UnsafeAccessor {
    private static final Unsafe unsafe;
    static {
        try {
            Field field = Unsafe.class.getDeclaredField("theUnsafe");
            field.setAccessible(true);
            unsafe = (Unsafe) field.get(null);
        }  catch (NoSuchFieldException | IllegalAccessException e) {
            throw new RuntimeException(e);
        }
    }

    public static Unsafe getUnsafe() {
        return unsafe;
    }
}

package com.ecnu.concurrent.unsafedemo;

import sun.misc.Unsafe;

public class MyUnsafeDemo {
    public static void main(String[] args) {

    }
}

class MyAtomicInteger{
    private volatile int value;
    private static final long valueOffset;
    private static final Unsafe UNSAFE;
    static {
        UNSAFE = UnsafeAccessor.getUnsafe();
        try {
            valueOffset = UNSAFE.objectFieldOffset(MyAtomicInteger.class.getDeclaredField("value"));
        } catch (NoSuchFieldException e) {
            throw new RuntimeException(e);
        }
    }

    public MyAtomicInteger(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
    public void decrement(int amount) {
        while (true) {
            int prev = value;
            int next = prev - amount;
            if (UNSAFE.compareAndSwapInt(this, valueOffset, prev, next)) {
                break;
            }
        }
    }
}

```



## 5.共享模型之不可变

- 不可变类的使用
- 不可变类的设计
- 无状态类设计

Changed

```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

import java.text.ParseException;
import java.text.SimpleDateFormat;



@Slf4j(topic = "c.Sync")
public class UnChangeDemo {
    public static void main(String[] args) {
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                try {
                    log.info("{}", simpleDateFormat.parse("1951-04-21"));
                } catch (ParseException e) {
                    log.error("{}", e);
                }
            }).start();
        }

    }
}

```

Unchange

```java
private static void unChange() {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");

        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                TemporalAccessor temporalAccessor = dateTimeFormatter.parse("2020-01-01");
                log.info("{}",temporalAccessor);
            }).start();
        }
    }
```

### 不可变类的设计

String不可变类

```java
 @Stable
    private final byte[] value;

    /**
     * The identifier of the encoding used to encode the bytes in
     * {@code value}. The supported values in this implementation are
     *
     * LATIN1
     * UTF16
     *
     * @implNote This field is trusted by the VM, and is a subject to
     * constant folding if String instance is constant. Overwriting this
     * field after construction will cause problems.
     */
    private final byte coder;

    /** Cache the hash code for the string */
    private int hash; // Default to 0
```

#### final 的使用

发现该类、类中所有属性都是 final 的

- 属性用 final 修饰保证了该属性是只读的，不能修改
- 类用 final 修饰保证了该类中的方法不能被覆盖，防止子类无意间破坏不可变性

#### 保护性拷贝

传入引用对象之后，采用复制方式，创建一个副本，防止外部修改

### 享元模式

当需要重用数量有限的同一类对象时

#### 体现

##### 包装类

在 JDK 中，Boolean，Byte，Short，Integer，Long，Character等包装类提供了 valueOf 方法，例如 Long 的valueOf 会缓存-128-127之间的 Long 对象，这个范围内的对象会被重用，大于这个返回才会新建 Long 对象

```java
private static class LongCache {
        private LongCache() {}

        static final Long[] cache;
        static Long[] archivedCache;

        static {
            int size = -(-128) + 127 + 1;

            // Load and use the archived cache if it exists
            CDS.initializeFromArchive(LongCache.class);
            if (archivedCache == null || archivedCache.length != size) {
                Long[] c = new Long[size];
                long value = -128;
                for(int i = 0; i < size; i++) {
                    c[i] = new Long(value++);
                }
                archivedCache = c;
            }
            cache = archivedCache;
        }
    }
@IntrinsicCandidate
    public static Long valueOf(long l) {
        final int offset = 128;
        if (l >= -128 && l <= 127) { // will cache
            return LongCache.cache[(int)l + offset];
        }
        return new Long(l);
    }
```

> [!CAUTION]
>
> Byte，Short，Long 缓存的范围是-128-127
>
> Character范围是 0-127
>
> Integer -128-127，最小值不能变，最大值可以通过虚拟机参数控制 Djava.lang.Integer.IntegerCache.hing
>
> Boolean缓存了 TRUE 和 FALSE

#### 自定义连接池

```java
package com.ecnu.concurrent;


import lombok.extern.slf4j.Slf4j;

import java.sql.*;
import java.util.Map;
import java.util.Properties;
import java.util.Random;
import java.util.concurrent.Executor;
import java.util.concurrent.atomic.AtomicIntegerArray;

@Slf4j(topic = "c.PoolDemo")
public class ConnectPoolDemo {
    public static void main(String[] args) {
        Pool pool = new Pool(2);
        for (int i = 0; i < 5; i++) {
            new Thread(()->{
                Connection connection = pool.borrow();
                try {
                    Thread.sleep(new Random().nextInt(1000));
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                pool.free(connection);
            }).start();
        }
    }

}


@Slf4j(topic = "c.Pool")
class Pool {
    private int poolSize;
    private Connection[] connections;
    // 0表示空闲， 1表示繁忙
    private AtomicIntegerArray states;
    public Pool(int poolSize) {
        this.poolSize = poolSize;
        this.connections = new Connection[poolSize];
        this.states = new AtomicIntegerArray(new int[poolSize]);
        for (int i = 0; i < poolSize; i++) {
            connections[i] = new MockConnection("conn" + (i+1));
        }
    }

    public Connection borrow() {
        while (true) {
            for (int i = 0; i < poolSize; i++) {
                if (states.get(i) == 0) {
                    if (states.compareAndSet(i,0,1)) {
                        log.info("borrow connection {}", connections[i]);
                        return connections[i];
                    }
                }
            }
            synchronized (this) {
                try {
                    log.info("wait...");
                    this.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public void free(Connection connection) {
        for (int i = 0; i < poolSize; i++) {
            if (connections[i] == connection) {
                this.states.set(i,0);
                log.info("free {}", connection);
                synchronized (this) {
                    this.notifyAll();
                }
                return;
            }
        }
    }
}

class MockConnection implements Connection {
    private String name;
    public MockConnection(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "MockConnection{" +
                "name='" + name + '\'' +
                '}';
    }

    @Override
    public Statement createStatement() throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql) throws SQLException {
        return null;
    }

    @Override
    public CallableStatement prepareCall(String sql) throws SQLException {
        return null;
    }

    @Override
    public String nativeSQL(String sql) throws SQLException {
        return "";
    }

    @Override
    public void setAutoCommit(boolean autoCommit) throws SQLException {

    }

    @Override
    public boolean getAutoCommit() throws SQLException {
        return false;
    }

    @Override
    public void commit() throws SQLException {

    }

    @Override
    public void rollback() throws SQLException {

    }

    @Override
    public void close() throws SQLException {

    }

    @Override
    public boolean isClosed() throws SQLException {
        return false;
    }

    @Override
    public DatabaseMetaData getMetaData() throws SQLException {
        return null;
    }

    @Override
    public void setReadOnly(boolean readOnly) throws SQLException {

    }

    @Override
    public boolean isReadOnly() throws SQLException {
        return false;
    }

    @Override
    public void setCatalog(String catalog) throws SQLException {

    }

    @Override
    public String getCatalog() throws SQLException {
        return "";
    }

    @Override
    public void setTransactionIsolation(int level) throws SQLException {

    }

    @Override
    public int getTransactionIsolation() throws SQLException {
        return 0;
    }

    @Override
    public SQLWarning getWarnings() throws SQLException {
        return null;
    }

    @Override
    public void clearWarnings() throws SQLException {

    }

    @Override
    public Statement createStatement(int resultSetType, int resultSetConcurrency) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int resultSetType, int resultSetConcurrency) throws SQLException {
        return null;
    }

    @Override
    public CallableStatement prepareCall(String sql, int resultSetType, int resultSetConcurrency) throws SQLException {
        return null;
    }

    @Override
    public Map<String, Class<?>> getTypeMap() throws SQLException {
        return Map.of();
    }

    @Override
    public void setTypeMap(Map<String, Class<?>> map) throws SQLException {

    }

    @Override
    public void setHoldability(int holdability) throws SQLException {

    }

    @Override
    public int getHoldability() throws SQLException {
        return 0;
    }

    @Override
    public Savepoint setSavepoint() throws SQLException {
        return null;
    }

    @Override
    public Savepoint setSavepoint(String name) throws SQLException {
        return null;
    }

    @Override
    public void rollback(Savepoint savepoint) throws SQLException {

    }

    @Override
    public void releaseSavepoint(Savepoint savepoint) throws SQLException {

    }

    @Override
    public Statement createStatement(int resultSetType, int resultSetConcurrency, int resultSetHoldability) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int resultSetType, int resultSetConcurrency, int resultSetHoldability) throws SQLException {
        return null;
    }

    @Override
    public CallableStatement prepareCall(String sql, int resultSetType, int resultSetConcurrency, int resultSetHoldability) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int autoGeneratedKeys) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, int[] columnIndexes) throws SQLException {
        return null;
    }

    @Override
    public PreparedStatement prepareStatement(String sql, String[] columnNames) throws SQLException {
        return null;
    }

    @Override
    public Clob createClob() throws SQLException {
        return null;
    }

    @Override
    public Blob createBlob() throws SQLException {
        return null;
    }

    @Override
    public NClob createNClob() throws SQLException {
        return null;
    }

    @Override
    public SQLXML createSQLXML() throws SQLException {
        return null;
    }

    @Override
    public boolean isValid(int timeout) throws SQLException {
        return false;
    }

    @Override
    public void setClientInfo(String name, String value) throws SQLClientInfoException {

    }

    @Override
    public void setClientInfo(Properties properties) throws SQLClientInfoException {

    }

    @Override
    public String getClientInfo(String name) throws SQLException {
        return "";
    }

    @Override
    public Properties getClientInfo() throws SQLException {
        return null;
    }

    @Override
    public Array createArrayOf(String typeName, Object[] elements) throws SQLException {
        return null;
    }

    @Override
    public Struct createStruct(String typeName, Object[] attributes) throws SQLException {
        return null;
    }

    @Override
    public void setSchema(String schema) throws SQLException {

    }

    @Override
    public String getSchema() throws SQLException {
        return "";
    }

    @Override
    public void abort(Executor executor) throws SQLException {

    }

    @Override
    public void setNetworkTimeout(Executor executor, int milliseconds) throws SQLException {

    }

    @Override
    public int getNetworkTimeout() throws SQLException {
        return 0;
    }

    @Override
    public <T> T unwrap(Class<T> iface) throws SQLException {
        return null;
    }

    @Override
    public boolean isWrapperFor(Class<?> iface) throws SQLException {
        return false;
    }
}

```

### final 原理

<img src="../images/截屏2024-08-01 00.06.01.png" alt="截屏2024-08-01 00.06.01" style="zoom:67%;" />



## 6. JUC

### 自定义线程池

![截屏2024-08-06 00.13.25](../images/截屏2024-08-06 00.13.25.png)



```java
package com.ecnu.concurrent;

import lombok.extern.slf4j.Slf4j;

import java.util.ArrayDeque;
import java.util.Deque;
import java.util.HashMap;
import java.util.HashSet;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

@Slf4j(topic = "c.TheadPoolMain")
public class ThreadPoolDemo {
    public static void main(String[] args) {
        ThreadPool threadPool = new ThreadPool(TimeUnit.MILLISECONDS,1000,2,10);
        for (int i = 0; i < 5; i++) {
            int j = i;
            threadPool.execute(()->{
               log.info("{}",j);
            });
        }
    }
}


@Slf4j(topic = "c.TheadPool")
class ThreadPool {
    // 任务队列
    private BlockingQueue<Runnable> taskQueue;

    // 线程集合
    private HashSet<Worker> workers = new HashSet();

    // 核心线程数
    private int coreSize;

    // 获取任务超时时间
    private long timeout;

    private TimeUnit timeUnit;

    // 执行任务
    public void execute(Runnable task) {
        // 当任务数没有超过 coreSize时，直接交给 worker对象执行
        // 当任务数超过coreSize时，加入任务队列暂存
        synchronized (workers) {
            if (workers.size() < coreSize) {
                Worker worker = new Worker(task);
                log.info("新增 work {}, {}", worker,task);
                workers.add(worker);
                worker.start();
            } else {
                log.info("加入任务队列 {}", task);
                taskQueue.put(task);
                // 1、死等
                // 2、带超时等待
                // 3、让调用者放弃执行
                // 4、让调用者抛出异常
                // 5、让调用者自己执行任务
            }
        }

    }

    public ThreadPool(TimeUnit timeUnit, long timeout, int coreSize, int capacity) {
        this.timeUnit = timeUnit;
        this.timeout = timeout;
        this.coreSize = coreSize;
        this.taskQueue = new BlockingQueue<>(capacity);
    }

    class Worker extends Thread {
        private Runnable task;

        public Worker(Runnable task) {
            this.task = task;
        }


        @Override
        public void run() {
            // 当 task 不为空时执行任务
            // 当 task执行完毕时，接着从任务队列中获取任务并执行
            // while (task != null || (task = taskQueue.take()) != null) {
            while (task != null || (task = taskQueue.poll(timeout,timeUnit)) != null) {
                try {
                    log.info("正在执行...{}",task);
                    task.run();
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    task = null;
                }
            }
            synchronized (workers) {
                log.info("work被移出{}",this);
                workers.remove(this);
            }
        }
    }
}



@Slf4j(topic = "c.BlockQueue")
class BlockingQueue<T> {
    // 任务 队列
    private Deque<T> queue = new ArrayDeque<>();

    // 锁
    private ReentrantLock lock = new ReentrantLock();

    // 生产者条件变量
    private Condition fullWaitSet = lock.newCondition();

    // 消费者条件变量
    private Condition emptyWaitSet = lock.newCondition();

    // 容量
    private int capacity;

    public BlockingQueue(int capacity) {
        this.capacity = capacity;
    }

    // 带超时的阻塞获取
    public T poll(long timeout, TimeUnit unit) {
        lock.lock();
        try {
            // 将 timeout 统一转换成纳秒
            long nanos = unit.toNanos(timeout);
            while (queue.isEmpty()) {
                try {
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
    public void put(T t) {
        lock.lock();
        try {
            while (queue.size() == capacity) {
                try {
                    fullWaitSet.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            queue.addLast(t);
            emptyWaitSet.signal();
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
}
```

### ThreadPoolExecutor

![截屏2024-09-02 23.55.22](../images/截屏2024-09-02 23.55.22.png)

#### 线程池状态

ThreadPoolExecutor使用 int

### AQS

```
全称是 AbstractQueuedSynchronizer，是阻塞式锁和相关的同步器工具的框架
```

## Volatile

### 解决变量不可见问题

1. 加锁

```java
synchronized (t){
                if (t.isFlag()){
                    System.out.println("主线程");
                }
            }
```

执行步骤

```
a. 线程获得锁

b. 清空工作内存

c.从主内存拷贝共享变量最新的值到工作内存成为副本

d. 执行代码

e. 将修改后的副本值刷新到主内存中

f.线程释放锁
```

2. volatile关键字

```
　　Java语言提供了一种稍弱的同步机制，即volatile变量，用来确保将变量的更新操作通知到其他线程。当把变量声明为volatile类型后，编译器与运行时都会注意到这个变量是共享的，因此不会将该变量上的操作与其他内存操作一起重排序。volatile变量不会被缓存在寄存器或者对其他处理器不可见的地方，因此在读取volatile类型的变量时总会返回最新写入的值。
```

<img src="book_juc\volatile.png" style="zoom:67%;" />

当对非 volatile 变量进行读写的时候，每个线程先从内存拷贝变量到CPU缓存中。如果计算机有多个CPU，每个线程可能在不同的CPU上被处理，这意味着每个线程可以拷贝到不同的 CPU cache 中。

　　而声明变量是 volatile 的，JVM 保证了每次读变量都从内存中读，跳过 CPU cache (副本)这一步。

```
1 线程a从主内存读取共享变量到对应的工作内存

2 对共享变量进行更改

3 线程a将修改后的值刷新到主内存，

4 线程b从主内存读取最新值，放入到对应工作内存
```

### 特性

1.保证此变量对所有的线程的可见性，这里的“可见性”，如本文开头所述，当一个线程修改了这个变量的值，volatile 保证了新值能立即同步到主内存，以及每次使用前立即从主内存刷新。但普通变量做不到这点，普通变量的值在线程间传递均需要通过主内存来完成。不保证原子性

2.禁止指令重排序优化。有volatile修饰的变量，赋值后多执行了一个“load addl $0x0, (%esp)”操作，这个操作相当于一个**内存屏障**（指令重排序时不能把后面的指令重排序到内存屏障之前的位置），只有一个CPU访问内存时，并不需要内存屏障；（什么是指令重排序：是指CPU采用了允许将多条指令不按程序规定的顺序分开发送给各相应电路单元处理）。

重排序的好处

<img src="book_juc\重排序.png" style="zoom:50%;" />

重排序可能带来并发问题

### CAS

```
CAS全称是Compare and Swap，即比较并交换，是通过原子指令来实现多线程的同步功能，将获取存储在内存地址的原值和指定的内存地址进行比较，只有当他们相等时，交换指定的预期值和内存中的值，这个操作是原子操作，若不相等，则重新获取存储在内存地址的原值
```

```java
 public final int getAndAddInt(Object o, long offset, int delta) {
        int v;
        do {
            v = getIntVolatile(o, offset); // 拿到内存位置最新值
        } while (!weakCompareAndSetInt(o, offset, v, v + delta));// CAS修改成功才跳出循环
        return v;
    }
```

缺点

```
1. 循环时间长开销很大。
2. 只能保证一个变量的原子操作。
3. ABA问题。
```

1. ```
   CAS 通常是配合无限循环一起使用的，我们可以看到 getAndAddInt 方法执行时，如果 CAS 失败，会一直进行尝试。如果 CAS 长时间一直不成功，可能会给 CPU 带来很大的开销。
   ```

2. ```
   当对一个变量执行操作时，我们可以使用循环 CAS 的方式来保证原子操作，但是对多个变量操作时，CAS 目前无法直接保证操作的原子性。但是我们可以通过以下两种办法来解决：1）使用互斥锁来保证原子性；2）将多个变量封装成对象，通过 AtomicReference 来保证原子性。
   ```

3. ```
   CAS 的使用流程通常如下：1）首先从地址 V 读取值 A；2）根据 A 计算目标值 B；3）通过 CAS 以原子的方式将地址 V 中的值从 A 修改为 B。
   
   但是在第1步中读取的值是A，并且在第3步修改成功了，我们就能说它的值在第1步和第3步之间没有被其他线程改变过了吗？
   
   如果在这段期间它的值曾经被改成了B，后来又被改回为A，那CAS操作就会误认为它从来没有被改变过。这个漏洞称为CAS操作的“ABA”问题。Java并发包为了解决这个问题，提供了一个带有标记的原子引用类“AtomicStampedReference”，它可以通过控制变量值的版本来保证CAS的正确性。因此，在使用CAS前要考虑清楚“ABA”问题是否会影响程序并发的正确性，如果需要解决ABA问题，改用传统的互斥同步可能会比原子类更高效。
   ```

### 单例模式

9种写法

1. 饿汉单例1  静态常量 

```java
private static final Single01 instance=new Single01();
    private Single01(){
    }
    public static Single01 getInstance(){
        return instance;
    }
    public static void main(String[] args) {
        Single01 s1=Single01.getInstance();
        Single01 s2=Single01.getInstance();
        System.out.println(s1==s2);
    }
```

2. 饿汉单例2  静态代码块

```java
private static final Single02 instance;
    static {
        instance=new Single02();
    }
    private Single02(){
    }
    public static Single02 getInstance(){
        return instance;
    }
    public static void main(String[] args) {
        Single02 s1=Single02.getInstance();
        Single02 s2=Single02.getInstance();
        System.out.println(s1==s2);
    }
```

3. 懒汉单例 1 线程不安全

```java
private static Single03 instance=null;
    private Single03(){
    }
    public static Single03 getInstance(){
        if (instance==null){
            instance=new Single03();
        }
        return instance;
    }
    public static void main(String[] args) {
        Single03 s1= Single03.getInstance();
        Single03 s2= Single03.getInstance();
        System.out.println(s1==s2);
    }
```

4. 懒汉单例2 线程安全synchronized

```java
private static Single04 instance=null;
    private Single04(){
    }
    public synchronized static Single04 getInstance(){
        if (instance==null){
            instance=new Single04();
        }
        return instance;
    }
    public static void main(String[] args) {
        Single04 s1= Single04.getInstance();
        Single04 s2= Single04.getInstance();
        System.out.println(s1==s2);
    }
```

5. 懒汉单例3  线程不安全 单检查

```java
private static Single05 instance=null;
    private Single05(){
    }
    public static Single05 getInstance(){
        if (instance==null){
            synchronized (Single05.class){
                instance=new Single05();
            }
        }
        return instance;
    }
    public static void main(String[] args) {
        Single05 s1= Single05.getInstance();
        Single05 s2= Single05.getInstance();
        System.out.println(s1==s2);
    }
```

6. 懒汉单例4 线程不安全 双检查没有volatitle

```java
private static Single06 instance=null;
    private Single06(){
    }
    public static Single06 getInstance(){
        if (instance==null){
            synchronized (Single06.class){
                if (instance==null){
                    instance=new Single06();
                }
            }
        }
        return instance;
    }
    public static void main(String[] args) {
        Single06 s1= Single06.getInstance();
        Single06 s2= Single06.getInstance();
        System.out.println(s1==s2);
    }
```

7. 懒汉单例 线程安全 双检查+volatile

```java
private static volatile Single07 instance=null;
    private Single07(){
    }
    public static Single07 getInstance(){
        if (instance==null){
            synchronized (Single07.class){
                if (instance==null){
                    instance=new Single07();
                }
            }
        }
        return instance;
    }
    public static void main(String[] args) {
        Single07 s1= Single07.getInstance();
        Single07 s2= Single07.getInstance();
        System.out.println(s1==s2);
    }
```

<img src="book_juc\单例模式重排序.png" style="zoom: 50%;" />

8. 静态内部类

```java
private Single08(){
    }
    private static class Inner{
        private static final Single08 instance=new Single08();
    }
    public static Single08 getInstance(){
        return Inner.instance;
    }
    public static void main(String[] args) {
        Single08 s1= Single08.getInstance();
        Single08 s2= Single08.getInstance();
        System.out.println(s1==s2);
    }
```

9. 枚举实现

```java
public enum  EnumSingleton {
    INSTANCE;
    public EnumSingleton getInstance(){
        return INSTANCE;
    }
}
public class User {
    //私有化构造函数
    private User(){ }
 
    //定义一个静态枚举类
    static enum SingletonEnum{
        //创建一个枚举对象，该对象天生为单例
        INSTANCE;
        private User user;
        //私有化枚举的构造函数
        private SingletonEnum(){
            user=new User();
        }
        public User getInstnce(){
            return user;
        }
    }
 
    //对外暴露一个获取User对象的静态方法
    public static User getInstance(){
        return SingletonEnum.INSTANCE.getInstnce();
    }
}

public class Test {
    public static void main(String [] args){
        System.out.println(User.getInstance());
        System.out.println(User.getInstance());
        System.out.println(User.getInstance()==User.getInstance());
    }
}
结果为true
```
