# Java并发编程实战

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

