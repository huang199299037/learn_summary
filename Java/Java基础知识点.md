#    Java基础知识点

## Comparable接口和Comparator接口的比较

1. Comparable和Comparator都是用来实现集合中元素的比较、排序的。
2. Comparable是在类内部定义的方法实现的排序，位于java.lang下。
3.  Comparator是在类外部实现的排序，位于java.util下。
4. 实现Comparable接口需要覆盖compareTo方法，实现Comparator接口需要覆盖compare方法。

**Comparable**

```java
public class User implements Comparable<User>{
    ...
    public int compareTo(User o) {
        if(this.age > o.getAge()) {
            return 1;
        }else if(this.age < o.getAge()) {
            return -1;
        }else{
            return 0;
        }
    }
}
Arrays.sort(users);
```

**Comparator**

```java
第一种方式
Collections.sort(list, new Comparator<Child>() {
            @Override
            public int compare(Child o1, Child o2) {
                return  o1.getAge() > o2.getAge() ? 1 : (o1.getAge() == o2.getAge() ? 0 : -1);
            }
        });
第二种方式
public class ChildComparator implements Comparator<Child> {
    ...
    @Override
    public int compare(Child o1, Child o2) {
        return o1.getAge() > o2.getAge() ? 1 : (o1.getAge() == o2.getAge() ? 0 : -1);
    }
}
Collections.sort(list, new ChildComparator());
```

**总结**

```
两种方法各有优劣，用Comparable 简单，只要实现Comparable接口的对象直接就成为一个可以比较的对象，但是需要修改源代码；用Comparator 的好处是不需要修改源代码，而是另外实现一个比较器，当某个自定义的对象需要作比较的时候，把比较器和对象一起传递过去就可以比大小了。并且在Comparator里面用户可以自己实现复杂的可以通用的逻辑，使其可以匹配一些比较简单的对象，那样就可以节省很多重复劳动了。总而言之Comparable是自已完成比较，Comparator是外部程序实现比较。
```

## 装箱拆箱

```Java
装箱就是自动将基本数据类型转换为包装器类型:
Integer total = 99 ——>Integer total = Integer.valueOf(99);
间接调用Integer.parseInt
public static Integer valueOf(String s, int radix) throws NumberFormatException {
        return Integer.valueOf(parseInt(s,radix));
}
拆箱就是自动将包装器类型转换为基本数据类型:
 int totalprim = total ——>int totalprim = total.intValue();
```

**Integer.valueOf**

```Java
public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```

它会首先判断i的大小：如果i小于-128或者大于等于128，就创建一个Integer对象,否则执行IntegerCache.cache[i + (-IntegerCache.low)]
IntegerCache.cache[i + (-IntegerCache.low)]它是一个静态的Integer数组对象，也就是说最终valueOf返回的都是一个Integer对象。装箱的过程会创建对应的对象，这个会消耗内存，所以装箱的过程会增加内存的消耗，影响性能。

```Java
public static void main(String[] args) {

        Integer i1 = 100;
        Integer i2 = 100;
        Integer i3 = 200;
        Integer i4 = 200;
        System.out.println(i1==i2);  //true
        System.out.println(i3==i4);  //false
    
        Double i1 = 100.0;
        Double i2 = 100.0;
        Double i3 = 200.0;
        Double i4 = 200.0;
        System.out.println(i1==i2); //false
        System.out.println(i3==i4); //false
    
    	Boolean i1 = false;
        Boolean i2 = false;
        Boolean i3 = true;
        Boolean i4 = true;
        System.out.println(i1==i2);//true
        System.out.println(i3==i4);//true
		
        Integer num1 = 400;  
        int num2 = 400;  
        System.out.println(num1 == num2); //true
    }
```

**equals**

```Java
Integer num1 = 100;  
int num2 = 100;  
System.out.println(num1.equals(num2));  //true

Integer num1 = 100;  
int num2 = 100;  
Long num3 = 200l;  
System.out.println(num1 + num2);  //200
System.out.println(num3 == (num1 + num2));  //true
System.out.println(num3.equals(num1 + num2));  //false
1、当一个基础数据类型与封装类进行==、+、-、*、/运算时，会将封装类进行拆箱，对基础数据类型进行运算。
2、对于num3.equals(num1 + num2)为false的原因很简单，我们还是根据代码实现来说明：
    它必须满足两个条件才为true：
	1、类型相同
	2、内容相同
public boolean equals(Object obj) {
        if (obj instanceof Long) {
            return value == ((Long)obj).longValue();
        }
        return false;
    }
public class Test {
    public static void main(String[] args){
                int  a = 33;
                sop(a);   
    }
    public static void sop(Object obj) {
        System.out.println(obj);
    }
}
在寻找最佳匹配方法时无法找到完全匹配的参数时
会进行自动提升转换或者其它相应转换
这里会发生：int自动装箱为Integer类
实际传入的是Integer对象
```

## String

**创建字符串形式**

​        首先形如声明为S ss是一个类S的引用变量ss（我们常常称之为句柄，后面JVM相关内容会讲到），而对象一般通过new创建。所以这里的ss仅仅是引用变量，并不是对象。

  **创建字符串的两种基本形式：**   

1. String s1=”1”;
2. String s2=new String(“1”);

<img src="java基础\str1.png" style="zoom: 80%;" />

从图中可以看出，s1使用””引号（也是平时所说的字面量）创建字符串，在编译期的时候就对常量池进行判断是否存在该字符串，如果存在则不创建直接返回对象的引用；如果不存在，则先在常量池中创建该字符串实例再返回实例的引用给s1。注意：编译期的常量池是静态常量池，以后和会讲。。。。

再来看看s2，s2使用关键词new创建字符串，JVM会首先检查字符串常量池，如果该字符串已经存在常量池中，那么不再在字符串常量池创建该字符串对象，而直接堆中复制该对象的副本，然后将堆中对象的地址赋值给引用s2，如果字符串不存在常量池中，就会实例化该字符串并且将其放到常量池中，然后在堆中复制该对象的副本，然后将堆中对象的地址赋值给引用s2。注意：此时是运行期，那么字符串常量池是在运行时常量池中的。。。。

**“+”连接形式创建字符串（更多可以查看API）：**

（1）String s1=”1”+”2”+”3”;

<img src="java基础\str2.png" style="zoom:80%;" />

使用包含常量的字符串连接创建是也是常量，编译期就能确定了，直接入字符串常量池，当然同样需要判断是否已经存在该字符串。

（2）String s2=”1”+”3”+new String(“1”)+”4”;

<img src="java基础\str3.png" style="zoom:80%;" />

当使用“+”连接字符串中含有变量时，也是在运行期才能确定的。首先连接操作最开始时如果都是字符串常量，编译后将尽可能多的字符串常量连接在一起，形成新的字符串常量参与后续的连接（可通过反编译工具jd-gui进行查看）。

```java
接下来的字符串连接是从左向右依次进行，对于不同的字符串，首先以最左边的字符串为参数创建StringBuilder对象（可变字符串对象），然后依次对右边进行append操作，最后将StringBuilder对象通过toString()方法转换成String对象（注意：中间的多个字符串常量不会自动拼接）。

    实际上的实现过程为：String s2=new StringBuilder(“13”).append(new String(“1”)).append(“4”).toString();

    当使用+进行多个字符串连接时，实际上是产生了一个StringBuilder对象和一个String对象
```

（3）String s3=new String(“1”)+new String(“1”);

<img src="java基础\str4.png" style="zoom:80%;" />

这个过程跟（2）类似。。。。。。

String.intern()解析

   String.intern()是一个Native方法，底层调用C++的 StringTable::intern 方法，源码注释：当调用 intern 方法时，如果常量池中已经该字符串，则返回池中的字符串；否则将此字符串添加到常量池中，并返回字符串的引用。

```java
public class StringTest {
public static void main(String[] args) {
// TODO 自动生成的方法存根
       String s3 = new String("1") + new String("1");
        System.out.println(s3 == s3.intern());
  }
}
```

JDK6的执行结果为：false
JDK7和JDK8的执行结果为：true

JDK6的内存模型如下：

<img src="java基础\str5.png" style="zoom:80%;" />

```
我们都知道JDK6中的常量池是放在永久代的，永久代和Java堆是两个完全分开的区域。而存在变量使用“+”连接而来的的对象存在Java堆中，且并未将对象存于常量池中，当调用 intern 方法时，如果常量池中已经存在该字符串，则返回池中的字符串；否则将此字符串添加到常量池中，并返回字符串的引用。所以结果为false。
```

JDK7JDK8的内存模型如下：

<img src="java基础\str6.png" style="zoom:80%;" />

```
JDK7中，字符串常量池已经被转移至Java堆中，开发人员也对intern 方法做了一些修改。因为字符串常量池和new的对象都存于Java堆中，为了优化性能和减少内存开销，当调用 intern 方法时，如果常量池中已经存在该字符串，则返回池中字符串；否则直接存储堆中的引用，也就是字符串常量池中存储的是指向堆里的对象。所以结果为true。
```

## 队列（Queue）用法

|          | **抛出异常** | **返回特殊值**     |
| -------- | ------------ | ------------------ |
| **插入** | add(e)       | offer(e) 返回false |
| **删除** | remove()     | poll() 返回null    |
| **检查** | element()    | peek() 返回null    |

Queue是java中实现的接口，它总共只有6个方法，我们一般只用其中3个就可以了。Queue的实现类有LinkedList和PriorityQueue。最常用的实现类是LinkedList。

**PriorityQueue**

```Java
public class Main {
    public static void main(String[] args) {
        Queue<User> q = new PriorityQueue<>(new UserComparator());
        // 添加3个元素到:
        q.offer(new User("Bob", "A1"));
        q.offer(new User("Alice", "A2"));
        q.offer(new User("Boss", "V1"));
        System.out.println(q.poll()); // Boss/V1
        System.out.println(q.poll()); // Bob/A1
        System.out.println(q.poll()); // Alice/A2
        System.out.println(q.poll()); // null,因为为空
    }
}

class UserComparator implements Comparator<User> {
    public int compare(User u1, User u2) {
        if (u1.number.charAt(0) == u2.number.charAt(0)) {
            // 如果两人的号都是A开头或者都是V开头,比较号的大小:
            return u1.number.compareTo(u2.number);
        }
        if (u1.number.charAt(0) == 'V') {
            // u1的号码是V开头,优先级高:
            return -1;
        } else {
            return 1;
        }
    }
}
```

**Deque**

|                    | Queue                  | Deque                           |
| ------------------ | ---------------------- | ------------------------------- |
| 添加元素到队尾     | add(E e) / offer(E e)  | addLast(E e) / offerLast(E e)   |
| 取队首元素并删除   | E remove() / E poll()  | E removeFirst() / E pollFirst() |
| 取队首元素但不删除 | E element() / E peek() | E getFirst() / E peekFirst()    |
| 添加元素到队首     | 无                     | addFirst(E e) / offerFirst(E e) |
| 取队尾元素并删除   | 无                     | E removeLast() / E pollLast()   |
| 取队尾元素但不删除 | 无                     | E getLast() / E peekLast()      |

注意到`Deque`接口实际上扩展自`Queue`：

 因此，`Queue`提供的`add()`/`offer()`方法在`Deque`中也可以使用，但是，使用`Deque`，最好不要调用`offer()`，而是调用`offerLast()`

**stack**

为什么Java的集合类没有单独的`Stack`接口呢？因为有个遗留类名字就叫`Stack`，出于兼容性考虑，所以没办法创建`Stack`接口，只能用`Deque`接口来“模拟”一个`Stack`了。

当我们把`Deque`作为`Stack`使用时，注意只调用`push()`/`pop()`/`peek()`方法，不要调用`addFirst()`/`removeFirst()`/`peekFirst()`方法，这样代码更加清晰。 

## 修饰符

|              | public | protected | 默认 | private |
| ------------ | ------ | --------- | ---- | ------- |
| 同一个类     | Yes    | Yes       | Yes  | Yes     |
| 同一个包     | Yes    | Yes       | Yes  | No      |
| 不同包子类   | Yes    | Yes       | No   | No      |
| 不同包非子类 | Yes    | No        | No   | No      |

## **HashCode计算方式**

Java中的hashcode是int类型

### 整数

```java
将本身作为当作hash值
public static int hashcode(int value){
    return value;
}
```

### 浮点数

```
将存储的二进制格式转为整数值
```

### Long和Double

```java
8个字节 需要高32位和低32位参与异或运算(先右移32位获得高32位)
public static int hashCode(double value) {
        long bits = doubleToLongBits(value);
        return (int)(bits ^ (bits >>> 32));
}
public static int hashCode(long value) {
        return (int)(value ^ (value >>> 32));
}
```

### 字符串

```java
jack 的哈希值可以表示为 j∗n^3+a∗n^2+c∗n^1+k∗n^0，等价于 [(j∗n+a)∗n+c]∗n+k
 在JDK 中，乘数 n为 31 ，为什么使用 31 ？
 31 是一个奇素数， JVM 会将 31 * i 优化成 (i << 5) – i
public static int hashCode(byte[] value) {
        int h = 0;
        int length = value.length >> 1;
        for (int i = 0; i < length; i++) {
            h = 31 * h + getChar(value, i);
        }
        return h;
}
```

## instanceOf 和 getClass()

```java
obj instanceOf Person (如果是person的子类也会返回true)
obj.getClass()!=Person.Class() 返回子类类型和Person类型
```

## 反射

<img src="java基础\Java代码的三个阶段.bmp" style="zoom:67%;" />

	* 框架：半成品软件。可以在框架的基础上进行软件开发，简化编码
	* 反射：将类的各个组成部分封装为其他对象，这就是反射机制
		* 好处：
			1. 可以在程序运行过程中，操作这些对象。
			2. 可以解耦，提高程序的可扩展性。


	* 获取Class对象的方式：
		1. Class.forName("全类名")：将字节码文件加载进内存，返回Class对象
			* 多用于配置文件，将类名定义在配置文件中。读取文件，加载类
		2. 类名.class：通过类名的属性class获取
			* 多用于参数的传递
		3. 对象.getClass()：getClass()方法在Object类中定义着。
			* 多用于对象的获取字节码的方式
	
		* 结论：
			同一个字节码文件(*.class)在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个。

```java
public class Person {
    private int age;
    private String name;
    
    public String a;
    protected String b;
    String c;
    private String d;

    public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public Person() {
    }

    public int getAge() {
        return age;
    }

    public void method(){
        System.out.println("public");
    }
    private void method(int num){
        System.out.println("private"+num);
    }
    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", a='" + a + '\'' +
                ", b='" + b + '\'' +
                ", c='" + c + '\'' +
                ", d='" + d + '\'' +
                '}';
    }
}
public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        // 方法一
        Class cls1=Class.forName("com.ecnu.test.reflection.Person");
        // 方法二
        Class cls2=Person.class;
        // 方法三
        Person person=new Person();
        Class cls3=person.getClass();
        System.out.println(cls1);
        System.out.println(cls2);
        System.out.println(cls3);
        System.out.println(cls1==cls2);
        System.out.println(cls2==cls3);
    }
}
```




```java
* Class对象功能：
	* 获取功能：
		1. 获取成员变量们
			* Field[] getFields() ：获取所有public修饰的成员变量
			* Field getField(String name)   获取指定名称的 public修饰的成员变量

			* Field[] getDeclaredFields()  获取所有的成员变量，不考虑修饰符
			* Field getDeclaredField(String name)  
		2. 获取构造方法们
			* Constructor<?>[] getConstructors()  
			* Constructor<T> getConstructor(类<?>... parameterTypes)  

			* Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)  
			* Constructor<?>[] getDeclaredConstructors()  
		3. 获取成员方法们：
			* Method[] getMethods()  
			* Method getMethod(String name, 类<?>... parameterTypes)  

			* Method[] getDeclaredMethods()  
			* Method getDeclaredMethod(String name, 类<?>... parameterTypes)  

		4. 获取全类名	
			* String getName()  
```


```java
* Field：成员变量
	* 操作：
		1. 设置值
			* void set(Object obj, Object value)  
		2. 获取值
			* Object get(Object obj) 

		3. 忽略访问权限修饰符的安全检查
			* setAccessible(true):暴力反射
```

```java
        Person person=new Person(18,"zhang");
        // 获取public的成员变量
        Field fieldA=personClass.getField("a");
        System.out.println(fieldA); 
       // 设置public的成员变量
        fieldA.set(person,"aaa");
        System.out.println(person);
		// 获取private的成员变量
        Field field=personClass.getDeclaredField("name");
        field.setAccessible(true);
        Object value=field.get(person);
        System.out.println(value);
		// 设置private的成员变量
        field.set(person,"li");
        System.out.println(person);
```

	* Constructor:构造方法
		* 创建对象：
			* T newInstance(Object... initargs)  
	
			* 如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法

```java
	   // 获得public构造器 构造对象
	   Constructor con=personClass.getConstructor();
        Object pe=con.newInstance();
        System.out.println(pe);

		// 获得private构造器 构造对象
        Constructor constructor=personClass.getDeclaredConstructor(int.class,String.class);
        constructor.setAccessible(true);
        Object p=constructor.newInstance(18,"lisi");
        System.out.println(p);
       
        //如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法
        Object per=personClass.newInstance();
        System.out.println(per);
```


	* Method：方法对象
		* 执行方法：
			* Object invoke(Object obj, Object... args)  
	
		* 获取方法名称：
			* String getName:获取方法名

```java
        // 获取public方法
        Method method = personClass.getMethod("method");
        Person person=new Person();
        method.invoke(person);
		// 获取private方法
        Method method1=personClass.getDeclaredMethod("method", int.class);
        method1.setAccessible(true);
        method1.invoke(person,3);
		// 获取方法名
        System.out.println(method1.getName());
```


	* 案例：
		* 需求：写一个"框架"，不能改变该类的任何代码的前提下，可以帮我们创建任意类的对象，并且执行其中任意方法
			* 实现：
				1. 配置文件
				2. 反射
			* 步骤：
				1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中
				2. 在程序中加载读取配置文件
				3. 使用反射技术来加载类文件进内存
				4. 创建对象
				5. 执行方法

```java
        // 创建Properties对象
        Properties properties=new Properties();
        // 获得类加载器
        ClassLoader classLoader = Main.class.getClassLoader();
        // 获得文件输入流
        InputStream resourceAsStream = classLoader.getResourceAsStream("pro.properties");

        // 加载配置文件
        properties.load(resourceAsStream);

        // 获得配置文件的数据
        String className = properties.getProperty("className");
        String methodName = properties.getProperty("methodName");

        //获得Class对象
        Class cls=Class.forName(className);

        // 创建对象
        Object obj=cls.newInstance();

        //获得Method对象
        Method method=cls.getMethod(methodName);
        method.invoke(obj);
```

## 泛型

```java
    private static void show1(){
        /*
        * 不使用泛型
        * 好处：集合不使用泛型，默认是Object，可以添加任意类型
        * 缺点：产生异常
        * */
        ArrayList list=new ArrayList();
        list.add("zs");
        list.add(4);
        Iterator it=list.iterator();
        while (it.hasNext()){
            Object obj=it.next();
            System.out.println(obj);
            String str=(String)obj;
            System.out.println(str.length());
        }

    }
    private static void show2(){
        /*
        * 使用泛型
        * 优点：1.把运行期间异常提升为编译期间异常
        *      2.避免了类型转换的麻烦
        * 缺点：泛型是什么类型，只能存储什么类型
        * */
        ArrayList<String> list=new ArrayList<>();
        list.add("zs");
        //list.add(4);//编译器错误
    }
```

### 泛型类

```java
package com.ecnu.test.generic;

/**
 * @author huang
 * @date 2020/11/26
 */

/*
  定义 class 类名 <泛型标识，泛型标识...>{
     private 泛型标识 变量名;
  }
  使用语法 类名<具体数据类型> 对象名=new 类名<具体数据类型>();
  java 1.7以后后面的具体数据类型可以省略
   类名<具体数据类型> 对象名=new 类名<>();
*
* */
public class Generic<T> {
    public static void main(String[] args) {
        Generic<String> strGeneric=new Generic<>("hello");
        System.out.println(strGeneric.getKey());
        Generic<Integer> intGeneric=new Generic<>(123);
        System.out.println(intGeneric.getKey());
        System.out.println(strGeneric.getClass()==intGeneric.getClass()); // true
    }
    private T key;

    public Generic(T key) {
        this.key = key;
    }

    public T getKey() {
        return key;
    }

    public void setKey(T key) {
        this.key = key;
    }

    @Override
    public String toString() {
        return "Generic{" +
                "key=" + key +
                '}';
    }
}

```

```
1. 如果没有指定具体数据类型，操作类型就是object类
2. 泛型参数只能是引用类型，不能是基本类型
3. 泛型逻辑上是多个不同的类型，实际上都是相同类型
```

**泛型类派生子类**

```java
public class Parent<E> {
    private E key;

    public E getKey() {
        return key;
    }

    @Override
    public String toString() {
        return "Parent{" +
                "key=" + key +
                '}';
    }

    public void setKey(E key) {
        this.key = key;
    }
}
public class ChildFirst<T,K,V> extends Parent<T> {
    @Override
    public T getKey() {
        return super.getKey();
    }
}
public class ChildSecond extends Parent<Integer>{
    @Override
    public Integer getKey() {
        return super.getKey();
    }
}
```

```java
/*
 * 1. 子类是泛型类，子类和父类的泛型类型要一致
 *  class ChildGeneric<T> extends Generic<T>
 * 2. 子类不是泛型类，父类要明确泛型的数据类型
 *  class ChildGeneric extends Generic<String>
 * */
```

### 泛型接口

```java
/*
* 定义  interface 接口名<泛型标识，泛型标识...>
* */
public interface GenericInterface<T> {
    T getKey();
}
public class Apple<T,V> implements GenericInterface<T> {

    private T key;
    private V value;

    public Apple(T key, V value) {
        this.key = key;
        this.value = value;
    }

    @Override
    public T getKey() {
        return key;
    }
}
public class Banana implements GenericInterface<String> {
    @Override
    public String getKey() {
        return "banana";
    }
}
```

```
/*
 * 1. 实现类是泛型类，实现类和接口泛型类型要一致
 * 2. 实现类不是泛型类，接口要明确泛型的数据类型
 * */
```

### 泛型方法

```
泛型类是在实例化类的时候指明泛型的具体类型
泛型方法是在调用方法时指明泛型的具体类型
```

```java
/*
* 泛型方法定义
*   修饰符 <泛型标识，泛型标识...> 返回值 方法名(形参列表){
*  }
*
* */
public class GenericMethod<T> {
    private T key;


    public GenericMethod(T key) {
        this.key = key;
    }
    // 成员方法，不是泛型方法
    public T getKey() {
        return key;
    }
    public void setKey(T key) {
        this.key = key;
    }

    // 与类中的T不同
    public <T> void method(T t){
        System.out.println(t);
    }
    public static <T,K,V> void method(T t,K k,V v){
        System.out.println(t+t.getClass().getSimpleName());
        System.out.println(k+k.getClass().getSimpleName());
        System.out.println(v+v.getClass().getSimpleName());
    }
    public <T> void methodA(T... t){
        for (T value :
                t) {
            System.out.println(value);
        }
    }

    public static void main(String[] args) {
        GenericMethod<Integer> generic=new GenericMethod<>(100);
        System.out.println(generic.getKey());
        generic.method("hello");
        GenericMethod.method("hell",123,true);
        generic.methodA(1,2,3);
    }

}
```

```
1. 泛型方法独立于类产生变化
2. 如果static要使用泛型能力，就必须成为泛型方法
```

### 类型通配符

```java
public class Box<T> {
    private T key;

    public T getKey() {
        return key;
    }

    public void setKey(T key) {
        this.key = key;
    }

    /*public static void showBox(Box<Number> box){
        Number number=box.getKey();
        System.out.println(number);
    }*/
    public static void showBox(Box<?> box){
        Object obj=box.getKey();
        System.out.println(obj);
    }

    public static void main(String[] args) {
        Box<Number> box=new Box<>();
        box.setKey(100);
        Box.showBox(box);

        Box<Integer> box2=new Box<>();
        box2.setKey(200);
        Box.showBox(box2);
    }
}

```

```
1. 类型通配符一般是使用"?"代替具体的类型实参
2. 类型通配符是类型实参，不是类型形参
```

**类型通配符上限**

```java
public class Animal {
}
public class Cat extends Animal{
}
public class MiniCat extends Cat{
}
public class Main {
    public static void main(String[] args) {
        ArrayList<Animal> animals=new ArrayList<>();
        ArrayList<Cat> cats=new ArrayList<>();
        ArrayList<MiniCat> miniCats=new ArrayList<>();
        show(animals); // 报错
        show(cats);
        show(miniCats);
    }

    public static void show(ArrayList<? extends Cat> list){
        for (Cat cat :
                list) {
            System.out.println(cat);
        }
    }
}
```

```
<? extends 实参类型>
要求泛型类型只能是实参类型，或者是其子类
```

**类型通配符下限**

```
<? super 实参类型>
要求泛型类型只能是实参类型，或者是其父类
```

### 类型擦除

<img src="java基础\无限制类型擦除.png" style="zoom: 50%;" />

<img src="java基础\有限制类型擦除.png" style="zoom: 50%;" />

<img src="java基础\桥接方法.png" style="zoom: 50%;" />

### 泛型和数组

```
1. 可以声明带泛型的数组引用，但是不能直接创建带泛型的数组对象
2. 可以通过java.lang.reflect.Array的newInstance(Class<T>,int)创建T[]数组
```

## 注解

```java
* 概念：说明程序的。给计算机看的
* 注释：用文字描述程序的。给程序员看的

* 定义：注解（Annotation），也叫元数据。一种代码级别的说明。它是JDK1.5及以后版本引入的一个特性，与类、接口、枚举是在同一个层次。它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明，注释。
* 概念描述：
	* JDK1.5之后的新特性
	* 说明程序的
	* 使用注解：@注解名称
```

	* 作用分类：
​		①编写文档：通过代码里标识的注解生成文档【生成文档doc文档】
​		②代码分析：通过代码里标识的注解对代码进行分析【使用反射】
​		③编译检查：通过代码里标识的注解让编译器能够实现基本的编译检查【Override】


```java
* JDK中预定义的一些注解
	* @Override	：检测被该注解标注的方法是否是继承自父类(接口)的
	* @Deprecated：该注解标注的内容，表示已过时
	* @SuppressWarnings：压制警告
		* 一般传递参数all  @SuppressWarnings("all")

* 自定义注解
	* 格式：
		元注解
		public @interface 注解名称{
			属性列表;
		}

	* 本质：注解本质上就是一个接口，该接口默认继承Annotation接口
		* public interface MyAnno extends java.lang.annotation.Annotation {}

	* 属性：接口中的抽象方法
		* 要求：
			1. 属性的返回值类型有下列取值
				* 基本数据类型
				* String
				* 枚举
				* 注解
				* 以上类型的数组

			2. 定义了属性，在使用时需要给属性赋值
				1. 如果定义属性时，使用default关键字给属性默认初始化值，则使用注解时，可以不进行属性的赋值。
				2. 如果只有一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值即可。
				3. 数组赋值时，值使用{}包裹。如果数组中只有一个值，则{}可以省略
	
	* 元注解：用于描述注解的注解
		* @Target：描述注解能够作用的位置
			* ElementType取值：
				* TYPE：可以作用于类上
				* METHOD：可以作用于方法上
				* FIELD：可以作用于成员变量上
		* @Retention：描述注解被保留的阶段
			* @Retention(RetentionPolicy.RUNTIME)：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
		* @Documented：描述注解是否被抽取到api文档中
		* @Inherited：描述注解是否被子类继承
```

```java
@Target({ElementType.TYPE,ElementType.METHOD,ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Anno {
   public abstract int age() default 18; 基本数据类型   
   public abstract String name(); String
   public abstract Em em(); 枚举
   public abstract MyAnno anno(); 注解
   public abstract int[] ages(); 他们的数组
}
```


```java
* 在程序使用(解析)注解：获取注解中定义的属性值
	1. 获取注解定义的位置的对象  （Class，Method,Field）
	2. 获取指定的注解
		* getAnnotation(Class)
		//其实就是在内存中生成了一个该注解接口的子类实现对象

	            public class ProImpl implements Pro{
	                public String className(){
	                    return "cn.itcast.annotation.Demo1";
	                }
	                public String methodName(){
	                    return "show";
	                }
	            }
	3. 调用注解中的抽象方法获取配置的属性值
```

注解的使用

```java
package cn.itcast.annotation;

import java.io.InputStream;
import java.lang.reflect.Method;
import java.util.Properties;

/**
 * 框架类
 */
@Pro(className = "cn.itcast.annotation.Demo1",methodName = "show")
public class ReflectTest {
    public static void main(String[] args) throws Exception {

        /*
            前提：不能改变该类的任何代码。可以创建任意类的对象，可以执行任意方法
         */


        //1.解析注解
        //1.1获取该类的字节码文件对象
        Class<ReflectTest> reflectTestClass = ReflectTest.class;
        //2.获取上边的注解对象
        //其实就是在内存中生成了一个该注解接口的子类实现对象
        /*

            public class ProImpl implements Pro{
                public String className(){
                    return "cn.itcast.annotation.Demo1";
                }
                public String methodName(){
                    return "show";
                }

            }
 */

        Pro an = reflectTestClass.getAnnotation(Pro.class);
        //3.调用注解对象中定义的抽象方法，获取返回值
        String className = an.className();
        String methodName = an.methodName();
        System.out.println(className);
        System.out.println(methodName);


        //3.加载该类进内存
        Class cls = Class.forName(className);
        //4.创建对象
        Object obj = cls.newInstance();
        //5.获取方法对象
        Method method = cls.getMethod(methodName);
        //6.执行方法
        method.invoke(obj);
    }
}

```


	* 案例：简单的测试框架
	* 小结：
		1. 以后大多数时候，我们会使用注解，而不是自定义注解
		2. 注解给谁用？
			1. 编译器
			2. 给解析程序用
		3. 注解不是程序的一部分，可以理解为注解就是一个标签
## 枚举

使用

```java
1. 类的对象只有有限个，确定的
2. 当定义一组常量时，建议使用枚举类
3. 如果枚举类只有一个对象，则可以作为单例模式的实现方式
public enum MyEnum {
}
// 反编译
public final class MyEnum extends java.lang.Enum<MyEnum> {
    public static MyEnum[] values();
    public static MyEnum valueOf(java.lang.String);
    static {};
}
```

自定义枚举类

```java
/*-------------------------
JDK1.5之前通过自行设计程序，来自定义枚举类
下面以季节为例自定义枚举类
--------------------------*/
package pack01;

public class Season {
    public static void main(String[] args) {

        FourSeasons spring = FourSeasons.SPRING;
        FourSeasons winter = FourSeasons.WINTER;

        System.out.println( spring.getName() );
        System.out.println( spring.toString() );
        System.out.println();
        System.out.println( winter.getName() );
        System.out.println( winter.toString() );
    }
}

// 定义表示季节的枚举类，共有4个内部对象
class FourSeasons {

    // 定义类的属性：私有的，终态的，在构造器中进行初始化
    private final String name;

    // 将构造器私有化，使构造器不能在类的外部被使用
    private FourSeasons(String name) {
        this.name = name;
    }

    // get方法返回属性值
    public String getName() {
        return name;
    }

    // 覆盖toString方法，设置默认打印值
    public String toString() {
        return "This is " + name;
    }

    // 在类的内部创建对象
    public static final FourSeasons SPRING = new FourSeasons("spring");
    public static final FourSeasons SUMMER = new FourSeasons("summer");
    public static final FourSeasons AUTUMN = new FourSeasons("autumn");
    public static final FourSeasons WINTER = new FourSeasons("winter");
}
```

enum枚举类

```java
/*-------------------------
JDK1.5开始可以通过enum关键字来定义枚举类
使用enum关键字定义枚举类与自定义枚举类的程序编写不同之处：
....//将关键字class用关键字enum替换
....//必须在类体的一开始创建内部的对象，并遵循一定的书写规则
....//枚举类有两个常用的方法：
........//values()：将枚举类中的所有对象以数组的方式返回
........//valueOf(String arg0)：将类中的某一个对象的名字以字符串的形式作为参数传入，返回该对象
....//枚举类可以实现接口，覆盖接口中的抽象方法既可以写在枚举类的类体中，也可以写在对象后的花括号中
........//若写在了对象后的花括号中，则该方法不再是所有对象公共的。不同对象调用同一方法时，可以得到不同的效果。

下面以季节为例使用enum关键字定义枚举类
--------------------------*/
package pack02;

public class Season {
    public static void main(String[] args) {

        //测试枚举类中的values()方法
        FourSeasons[] seasons = FourSeasons.values();
        for( int i=0; i<seasons.length; ++i ) {
            System.out.println( seasons[i].getName() );
        }
        System.out.println();

        //测试枚举类中的valueOf()方法，创建春天的对象
        FourSeasons spring = FourSeasons.valueOf("SPRING");
        System.out.println( "valueOf(\"SPRING\"): " + spring.getName() + '\n' );

        //创建夏，秋，冬的对象
        FourSeasons summer = FourSeasons.SUMMER;
        FourSeasons autumn = FourSeasons.AUTUMN;
        FourSeasons winter = FourSeasons.WINTER;
        spring.printWords();
        summer.printWords();
        autumn.printWords();
        winter.printWords();
        //上面调用接口中的方法打印出来的结果，春天与其他三个季节是不同的，因为SPRING对象重新覆盖了接口中的方法
    }
}

//定义一个接口，让枚举类来实现该接口
interface Inter{
    void printWords();
}

// 定义表示季节的枚举类，共有4个内部对象
enum FourSeasons implements Inter { //使用enum关键字，并实现上述接口

    //必须在类体中的一开始创建对象，对象之间用逗号分开，并且要遵循一定的书写规则
    SPRING("spring"){
        public void printWords() {//在创建对象时可以单独重写接口中的方法，这时类体中的重写方法对于该对象将不再起作用
            System.out.println("This is spring");
        }
    },
    SUMMER("summer"),
    AUTUMN("autumn"),
    WINTER("winter");

    // 定义类的属性：私有的，终态的，在构造器中进行初始化
    private final String name;

    // 将构造器私有化，使构造器不能在类的外部被使用
    private FourSeasons(String name) {
        this.name = name;
    }

    // get方法返回属性值
    public String getName() {
        return name;
    }

    // 重写接口中的抽象方法
    public void printWords() {
        System.out.println("There are four different seasons.");
    }
}
```

## 内部类

```
1.成员内部类
2.局部内部类(匿名内部类)
```

成员内部类

```java
修饰符 class 外部类名称｛
	修饰符 class 内部类名称｛
	｝
｝
内用外，随意用 外用内，需要内部类对象
使用内部类
        1.间接方式：在外部类的方法中，使用内部类，然后Main调用外部类方法
         body.method();
        2.直接方式：外部类名称.内部类名称 对象名=new 外部类名称().new 内部类名称()
        Body.Heart heart=new Body().new Heart();
	    heart.beat();
```

```java
package com.ecnu.test.inner;

public class Body {
    public static void main(String[] args) {
        Body body=new Body();
        body.methodBody();

        Body.Heart heart=new Body().new Heart();
        heart.beat();
    }
    public class Heart{
        public void beat(){
            System.out.println("内部类方法");
            System.out.println("我叫："+name);
        }

    }
    private String name;

    public void methodBody(){
        Heart heart=new Heart();
        heart.beat();
        System.out.println("外部类方法");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```

重名解决

```java
public class Outer {
    public static void main(String[] args) {
        Outer.Inner in=new Outer().new Inner();
        in.method();
    }
    int num=10;
    public class Inner{
        int num=20;
        public void method(){
            int  num=30;
            System.out.println(num);
            System.out.println(this.num);
            System.out.println(Outer.this.num);
        }
    }
}
```

局部内部类

```java
一个类定义在方法中
修饰符 class 外部类名称｛
	修饰符 返回值类型 外部类方法名称(参数列表)｛
    	class 局部内部类名称｛
        ｝
	｝
｝
```

```java
public class Outer2 {

    /*public>protected>(default)>private
    定义一个类，权限修饰符规则：
    1.外部类：public/deafult
    2.成员内部类：都可以
    3.局部内部类：什么也不写*/
    public static void main(String[] args) {
        Outer2 outer2=new Outer2();
        outer2.method();
    }
    public void method(){
        class Inner{
            int num=10;
            public void innerMethod(){
                System.out.println(num);
            }
        }
        //局部内部类只能在方法内部使用
        Inner inn=new Inner();
        inn.innerMethod();
    }
}
```

局部内部类final问题

```java
/*
局部内部类需要访问所在方法的局部变量，那么这个变量必须是有效final
从 java8开始，只要局部变量事实不变那么final关键字可以省略
原因：
1.new 出来的对象存放在堆中
2.局部变量跟着方法走，存放在栈中
3.方法运行结束之后，立刻出栈，局部变量会立刻消失
4.但是new出来的对象会在堆中持续存在，直到垃圾回收消失，所以需要变量不变，拷贝一份即可
* */
public class Outer3 {
    public void method(){
        int num=10;
        class inner{
            public void innerMethod(){
                System.out.println(num);
            }
        }
    }
}

```

匿名内部类

```java
只用一次
匿名内部类定义格式
接口名称 对象=new 接口名称｛
｝；
1.new 表示创建对象的动作
2.接口内部就是匿名内部类需要实现的接口
3.｛...｝才是匿名内部类的对象
另外还要注意的问题
    1.匿名内部类 在创建对象的时候只能使用一次
    如果希望多次创建，而且类的内容一样的话，就必须使用单独定义的实现类
    2.匿名对象，在调用方法的时候只能调用一次，如果希望同一个对象，调用多次方法，那么必须给对象起个名字
    3.匿名内部类是省略【实现类/子类名称】但是匿名对象是省略对象名称
    匿名对象和匿名内部类不一样
```

```java
public class Outer4 {
    public static void main(String[] args) {
        Impl impl=new Impl() {
            @Override
            public void method1() {
            }

            @Override
            public void method2() {
            }
        };
        impl.method1();
        impl.method2();
        new Impl(){
            @Override
            public void method1() {
            }
            @Override
            public void method2() {

            }
        }.method1();
    }
}
```

## 迭代器

```java
通用的取出Collection集合的内容
package com.ecnu.test;

import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class iterator {
    public static void main(String[] args) {
        Collection<String> col=new ArrayList<>();
        col.add("zs");
        col.add("ls");
        //获取Iterator接口实现类
        Iterator<String> it=col.iterator();
        while (it.hasNext()){
            String str=it.next();
            System.out.println(str);
        }
    }
}
```

## 重载和重写

```
方法的重载和重写都是实现多态的方式，区别在于前者实现的是编译时的多态性，而后者实现的是运行时的多态性。重载发生在一个类中，同名的方法如果有不同的参数列表（参数类型不同、参数个数不同或者二者都不同）则视为重载；重写发生在子类与父类之间，重写要求子类被重写方法与父类被重写方法有相同的参数列表，有兼容的返回类型，比父类被重写方法更好访问，不能比父类被重写方法声明更多的异常（里氏代换原则）。重载对返回类型没有特殊的要求，不能根据返回类型进行区分。
```

## IO框架

### 流的概念

```
内存与存储设备之间传输数据的通道
```

```
一、方向
输入流：将<存储设备>中的内容读到<内存>中
输出流：将<内存>中的内容写到<存储设备>中
二、单位
字节流：以字节为单位，可以读写所有数据
字符流：以字符为单位，只能读写文本数据
三、功能
节点流：具有实际传输数据的读写功能
过滤流：在节点流的基础之上增强功能
```

### 字节流

```java
//InputStream 字节输入流
public int read(){}  //从输入流中读取下一个字节
public int read(byte[] b){} // 从输入流中读取一定数量的字节并存储在缓冲数组b中
public int read(byte[] b, int off, int len){} // 将输入流最多len个字节读入数组b中，偏移量off

// OutputStream 字节输出流
public void write(int n){}  // 将指定字节写入输出流
public void write(byte[] b){} //将b.length个字节从指定的byte数组中写入输出流
public void write(byte[] b, int off, int len){} // 将指定b数组从偏移量off开始len个字节写入输出流
```

**文件字节流**

```java
FileInputStream (File file) // 文件类
FileInputStream(FileDescriptor fdObj) // 文件描述符
FileInputStream(String name) //地址
```

文件输入流

```java
   public static void main(String[] args) throws IOException {
        FileInputStream fis=new FileInputStream("F:\\javaproject\\jvm\\src\\com\\ecnu\\classloader\\file\\a.txt");
        //1、读取单个字节
      /*  int temp=0;
        while ((temp=fis.read())!=-1)
            System.out.print((char)temp);*/
        //2、读取多个字节
        byte[] buf=new byte[1024];
        int count=0
        while ((count=fis.read(buf))!=-1){
            System.out.println(new String(buf,0,count));
        }
        fis.close();
    }
```

文件输出流

```java
public static void main(String[] args) throws IOException {
        FileOutputStream fos=new FileOutputStream(
                "F:\\javaproject\\jvm\\src\\com\\" +
                        "ecnu\\classloader\\file\\b.txt",true);  //true表示追加内容不覆盖
        fos.write(97);
        fos.write('b');
        String str="Hello";
        fos.write(str.getBytes());
        fos.close();
    }
```

字节缓冲流

缓冲流：BufferedInputStream/ BufferedOutputStream

- 提高IO效率，减少访问磁盘次数
- 数据存储在缓冲区中，flush是将缓冲区的内容写入文件中，也可以直接close

```java
public static void main(String[] args) throws IOException {
        FileInputStream fis=new FileInputStream("F:\\javaproject\\jvm\\" +
                "src\\com\\ecnu\\classloader\\file\\a.txt");
        BufferedInputStream bis=new BufferedInputStream(fis);
        int count=0;
        byte[] buf=new byte[1024]; //自己创建缓冲流大小，默认8K
        while ((count=bis.read(buf))!=-1){
            System.out.println(new String(buf,0,count));
        }
        bis.close();
    }
```

```java
// 1 创建BufferedInputStream
  FileOutputStream fos = new FileOutputStream("路径");
  BufferedOutputStream bis = new BufferedOutputStream(fos);
  // 2 写入文件
  for(int i = 0; i < 10; i ++){
    bos.write("hello".getBytes());// 写入8k缓冲区
    bos.flush(); // 刷新到硬盘
  }
  // 3 关闭
  bos.close();
```

### 对象流

ObjectOutputStream / ObjectInputStream

- 增强了缓冲区功能
- 增强了读写8种基本数据类型和字符串的功能
- 增强了读写对象的功能
  - `readObject()` 从流中读取一个对象
  - `writeObject(Object obj)` 向流中写入一个对象

使用流传输对象的过程称为序列化、反序列化

**序列化**

```java
// 使用objectoutputStream实现序列化
psvm(String[] args){
  // 1. 创建对象流
  FileOutputStream fos = new FileOutputStream("d:\\st.bin");
  ObjectOutputSream oos = new objectOutputSream(fos);
  // 2. 序列化（写入操作）
  Student zhangsan = new Student("zs", 20);
  oos.WriteObject(zhangsan);
  // 3. 关闭
  oos.close();
  sout("序列化完毕");
}
```

**反序列化**

```java
// 使用ObjectInputSteam实现反序列化（读取重构对象）
psvm(String[] args){
  // 1. 创建对象流
  FileInputStream fis = new FileInputStream("d:\\stu.bin");
  ObjectInputStream ois = new ObjectInputStream(fis);
  // 2. 读取文件（反序列化）
  Student s = (Student)ois.readObject();
  // 3. 关闭
  ois.close();
  sout("执行完毕");
  sout(s.toString());  
}
```

1. 某个类要想序列化必须实现Serializable接口
2. 序列化类中对象属性要求实现Serializable接口
3. 序列化版本号ID，保证序列化的类和反序列化的类是同一个类
4. 使用transient修饰属性，这个属性就不能序列化
5. 静态属性不能序列化
6. 序列化多个对象，可以借助集合来实现

### 文件流

输入流

```java
// 1. 创建FileReader 文件字符输入流
FileReader fr = new FileReader("..");
// 2. 读取
// 2.1 单个字符读取
int data = 0;
while((data = fr.read()) != -1){
  sout((char)data);// 读取一个字符
}
char[] buf = new char[2];// 字符缓冲区读取
int count = 0;
while((count = fr.read(buf) != -1)){
  sout(new String(buf, 0, count));
}
// 3. 关闭
fr.close();
```

输出流

```java
// 1. 创建FileWriter对象
FileWriter fw = new FileWriter("..");
// 2. 写入
for(int i = 0; i < 10; i ++){
  fw.write("写入的内容");
  fw.flush();
}
// 3. 关闭
fw.close();
sout("执行完毕");
```

**缓冲流**

```java
psvm(String[] args) throws Exception{
  // 创建缓冲流
  FileReader fr = new FileReader("..");
	BufferedReader br = new BufferedReader(fr);
  // 读取
  // 1. 第一种方式
  char[] buf = new char[1024];
  int count = 0;
  while((count = br.read(buf)) != -1){
    sout(new String(buf, 0, count));
  }
  // 2. 第二种方式 一行一行读取
  String line = null;
  while((line = br.readLine()) != null){
    sout(line);
  }
  
	// 关闭
  br.close();
}
```

```java
psvm(String[] args){
  // 1. 创建BufferedWriter对象
  FileWriter fw = new FileWriter("..");
  BufferedWriter bw = new BufferedWriter(fw);
  // 2. 写入
  for(int i = 0; i < 10; i ++){
    bw.write("写入的内容");
    bw.newLine(); // 写入一个换行符
    bw.flush();
  }
  // 3. 关闭
  bw.close(); // 此时会自动关闭fw
}
```

### 打印流

封装了`print() / println()` 方法 支持写入后换行

支持数据原样打印

```java
psvm(String[] args){
  // 1 创建打印流
  PrintWriter pw = new PrintWriter("..");
  // 2 打印
  pw.println(12);
  pw.println(true);
  pw.println(3.14);
  pw.println('a');
  // 3 关闭
  pw.close();
}
```

### 转换流

桥转换流 `InputStreamReader / OutputStreamWriter`

可将字节流转换为字符流

可设置字符的编码方式

```java
psvm(String[] args) throws Exception{
  // 1 创建InputStreamReader对象
  FileInputStream fis = new FisInputStream("..");
  InputStreamReader isr = new InputStreamReader(fis, "utf-8");
  // 2 读取文件
  int data = 0;
  while((data = isr.read()) != -1){
    sout((char)data);
  }
  // 3 关闭
  isr.close();
}
```

```java
psvm(String[] args) throws Exception{
  // 1 创建OutputStreamReader对象
  FileOutputStream fos = new FisOutputStream("..");
  OutputStreamWRITER osw = new OutputStreamReader(fos, "utf-8");
  // 2 写入
  for(int i = 0; i < 10; i ++){
    osw.write("写入内容");
    osw.flush();
  }
  // 3 关闭
  osw.close();
}
```

### File

概念：代表物理盘符中的一个文件或者文件夹

```java
/*
File类的使用
1. 分隔符
2. 文件操作
3. 文件夹操作
*/
public class Demo{
  psvm(String[] args){
    separator();
  }
  // 1. 分隔符
  public static void separator(){
    sout("路径分隔符" + File.pathSeparator);
    sout("名称分隔符" + File.separator);
  }
  // 2. 文件操作
  public static void fileOpen(){
    // 1. 创建文件
    if(!file.exists()){ // 是否存在
    	File file = new File("...");
    	boolean b = file.creatNewFile();
    }
    
    // 2. 删除文件
    // 2.1 直接删除
    file.delete(); // 成功true
    // 2.2 使用jvm退出时删除
    file.deleteOnExit();
    
    // 3. 获取文件信息
    sout("获取绝对路径" + file.getAbsolutePaht());
    sout("获取路径" + file.getPath());
    sout("获取文件名称" + file.getName());
    sout("获取夫目录" + file.getParent());
    sout("获取文件长度" + file.length());
    sout("文件创建时间" + new Date(file.lashModified()).toLocalString());
    
    // 4. 判断
    sout("是否可写" + file.canWrite());
    sout("是否是文件" + file.isFile());
    sout("是否隐藏" + file.isHidden());
  }
  
  
  // 文件夹操作
  public static void directoryOpe() throws Exception{
    // 1. 创建文件夹
    File dir = new File("...");
    sout(dir.toString());
    if(!dir.exists()){
      //dir.mkdir(); // 只能创建单级目录
      dir.mkdirs(); // 创建多级目录
    }
    
    // 2. 删除文件夹
    // 2.1 直接删除
    dir.delete(); // 只能删除最底层空目录
    // 2.2 使用jvm删除
    dir.deleteOnExit();
    
    // 3. 获取文件夹信息
 		sout("获取绝对路径" + dir.getAbsolutePaht());
    sout("获取路径" + dir.getPath());
    sout("获取文件名称" + dir.getName());
    sout("获取夫目录" + dir.getParent());
    sout("获取文件长度" + dir.length());
    sout("文件夹创建时间" + new Date(dir.lashModified()).toLocalString());
    
    // 4. 判断
    sout("是否是文件夹" + dir.isFile());
    sout("是否隐藏" + dir.isHidden());
    
    // 5. 遍历文件夹
    File dir2 = new File("...");
    String[] files = dir2.list();
    for(String string : files){
      sout(string);
    }
    
    // FileFilter接口的使用
    
    File[] files2 = dir2.listFiles(new FileFilter(){
      
      @Override
      public boolean accept(File pathname){
        if(pathname.getName().endsWith(".jpg")){
          return true;
        }
        return false;
      }
    });
    for(File file : files2){
      sout(file.getName());
    }
    
  }
}
```

递归删除文件

```java
public static void deleteDir(File dir){
  File[] files = dir.listFiles();
  if(files != null && files.length > 0){
    for(File file : files){
      if(file.idDirectory()){
        deleteDir(file); // 递归
      }else{
        // 删除文件
        sout(file.getAbsolutePath() + "删除" + file.delete());
      }
    }
  }
}
```

### Properties

```java
public static void main(String[] args) {
        Properties properties=new Properties();
        properties.setProperty("username","zhangsan");
        properties.setProperty("age","20");
        System.out.println(properties.toString());
        Set<String> proName=properties.stringPropertyNames();
        for (String pro:
             proName) {
            System.out.println(pro+"===="+properties.getProperty(pro));
        }
    }
```

## 异常

```
Throwable是错误和异常的父类
```

编译器异常

```java
 public static void main(String[] args) throws IOException {

        BufferedReader br =new BufferedReader(new InputStreamReader(
                System.in
        ));
        br.readLine();
        
    }
```

运行时异常

```java
int[] a=new int[]{1,2,3}
System.out.print(a[4])
```

异常产生原因

![](java基础\异常原因.png)

throw

```
作用：
	可以使用throw关键字在指定方法中抛出异常
使用格式
	throw new xxxException("原因阐述")
1. throw关键字必须写在方法内部
2. throw关键字后边的new对象必须是Exception或其子类
3. 必须处理异常
	1.throw后边是运行时异常或其子类，我们可以不处理，交给jvm
	2. 编译异常必须处理，要么throws要么try catch
```

throws

```
编译期间异常 会把异常抛出给方法调用者，最用交给jvm处理，中断处理
如果抛出异常有父子关系，直接抛出父类即可
```

try catch

```
自己处理异常
多个异常，多个catch
如果try中产生异常，执行catch逻辑，之后执行try-catch之后代码 不会执行try中代码
如果没有，不执行catch逻辑，先执行try中逻辑，之后执行try-catch之后代码
catch (Exception e) {
            e.printStackTrace();  //此方法最详细
        }
```

finally

```
不能单独使用 必须配合try
无论出现异常都会执行
finally中如果有return永远返回finally中的return语句
```

```
异常有子父类关系，子类必须写在上面
```

![](java基础\多异常父子关系.png)

<img src="java基础\子类父类异常01.png" style="zoom:67%;" />

<img src="java基础\子父类异常02.png" style="zoom:67%;" />

java中异常抛出后代码还会继续执行吗

```java
//代码1
public static void test() throws Exception  {
 
    throw new Exception("参数越界"); 
    System.out.println("异常后"); //编译错误，「无法访问的语句」
}
```

```java
//代码2
try{
    throw new Exception("参数越界"); 
}catch(Exception e) {
    e.printStackTrace();
}
System.out.println("异常后");//可以执行
```

```java
//代码3
if(true) {
    throw new Exception("参数越界"); 
}
System.out.println("异常后"); //抛出异常，不会执行
```

1. 若一段代码前有异常抛出，并且这个异常没有被捕获，这段代码将产生编译时错误「无法访问的语句」。如代码1
2. 若一段代码前有异常抛出，并且这个异常被try...catch所捕获，若此时catch语句中没有抛出新的异常，则这段代码能够被执行，否则，同第1条。如代码2
3. 若在一个条件语句中抛出异常，则程序能被编译，但后面的语句不会被执行。如代码3

## final

```
1 可以修饰一个类
2 可以修饰一个方法
3 可以修饰一个局部变量
4 可以修饰一个成员变量
```

修饰类

```java
当前类不能有子类
public final class FinalDemo {
}
```

修饰方法

```java
子类不能覆盖重写
abstract和final不能同时使用 因为矛盾 abstract必须重写，但是final不允许重写
public final void method(){
}
```

修饰局部变量

```java
不允许改变变量值
final int num=20;
// 错误
num=30;
num=20
对于基本类型 值不变
对于引用类型 地址值不变
```

修饰成员变量

```java
对于成员变量，如果用final修饰，变量也是不可变的
由于成员变量具有默认值，所以final必须手动赋值，不会再给默认值
1 直接赋值
2 构造函数赋值
二者选择其一
final String name="zs";
public FinalDemo(String name){
        this.name=name;
}
```

## static

```
一旦使用static关键字  那么这样的内容就不属于对象自己，而是属于类，凡是本类的对象，都共享一份
```

成员变量

```java
生成计数器
int id;
    private static int idCounter=0;

    public Father() {
        this.id=++idCounter;
    }
Father father1=new Father();
        System.out.println(father1.id);
        Father father2=new Father();
        System.out.println(father2.id);
        System.out.println(father1.id);
```

成员方法

```
没有static关键字，必须通过对象名调用
用了static关键字，既可以通过类名调用，也可以通过对象名调用
对于本类当中的静态方法，可以省略类名称
```

注意事项

```java
1. 静态不能直接访问非静态
现有静态内容，后有的非静态内容
2. 静态方法不能使用this
this代表当前对象，通过谁调用的方法，谁就是当前对象
    int num=200;

    static int number;
    public static void methodA(){
        System.out.println("静态方法");
        //System.out.println(num); 报错
    }
    public void methodFather(){
        System.out.println(num);
        System.out.println(number);
    }
```

内存图

![](java基础\static1.png)

静态代码块

```java
当第一次用到本类时，静态代码块执行唯一的一次
静态内容总是优于非静态，所以静态代码块比构造方法先执行
    public class Test {
    static {
        System.out.println("静态代码块");
    }

    public Test() {
        System.out.println("构造方法");
    }

    public static void main(String[] args) {
        Test tes1=new Test();
        Test test2=new Test();
    }
}
静态代码块
构造方法
构造方法

典型用途
    一次性对静态成员变量进行赋值
```

## 继承

```
共性抽取
```

成员变量

```java
在父子类继承关系中，如果成员变量重名，则创建子类对象时，访问有两种方式

1 直接通过子类对象访问成员变量
	等号左边是谁，就优先使用谁，没有则向上找
2 间接通过成员方法访问成员变量
	该方法属于谁，就优先用谁，没有则向上找
int num=200; father
    public void methodFather(){
        System.out.println(num);
    }
int num=100;

    public void methodSon(){
        System.out.println(num);
    }
    
System.out.println(father.num); // 200
System.out.println(son.num); // 100
son.methodFather(); // 200
```

三种重名

```java
局部变量       直接接局部变量名
本类成员变量   this.成员变量名
父类成员变量   super.成员变量名
public void method(){
        int num=50;
        System.out.println(num);
        System.out.println(this.num);
        System.out.println(super.num);
    }
```

成员方法

```java
重名成员方法
	创建的对象是谁，优先用谁，如果没有则向上找
重写注意事项
1. 必须保证与父类的方法名称相同，参数列表页相同
2. 子类返回值必须，小于等于父类方法的返回值范围
	父类返回Sting 子类返回Object 报错
3. 子类方法的权限必须大于等于 父类方法的修饰符
    父类是protected 子类必须大于等于protected
```

构造方法

```java
1. 子类构造方法默认隐含“super()”调用，所以先调用父类构造方法，然后调用子类的构造方法
2. 子类构造方法可以通过super关键字来调用父类重载的方法
3. super的父类构造调用，必须是子类构造方法的第一句，且不能多次构造
public Fu() {
        System.out.println("父类构造方法");
    }
 public Zi() {

        //super(); 默认赠送
        System.out.println("子类构造方法");
    }

    public static void main(String[] args) {
        Zi zi=new Zi();
    }
```

super

```
1. 在子类成员方法中访问父类成员变量
2. 在子类成员方法中访问父类成员方法
3.在子类构造方法中访问父类构造方法
```

this

```java
1. 在本类成员方法中，访问奔雷成员变量
2. 在本类成员，调用本类的另一个成员方法
3. 在本类的构造方法中，访问本类的另一个构造方法
A. this调用必须是第一个，也是唯一一个
B. super和this两种构造调用，不能同时使用
public class Zi extends Fu{
    public Zi() {

        //super();
        this(0);
        System.out.println("子类构造方法");
    }
    public Zi(int num){
        this(1,2);
    }
    public Zi(int a,int b){

    }
    public void methodA(){
        
    }
    public void methodB(){
        methodA();
    }

    public static void main(String[] args) {
        Zi zi=new Zi();
    }
}
```

```
不能同时使用，this和super不能同时出现在一个构造函数里面，因为this必然会调用其它的构造函数，其它的构造函数必然也会有super语句的存在，所以在同一个构造函数里面有相同的语句，就失去了语句的意义，编译器也不会通过。
```

图解

![](java基础\super_this.png)

```
子类中有super.method()方法
```

继承特点

![](java基础\继承特点.png)

## 抽象类

```java
父类方法不确定如何实现，那么这就是一个抽象方法
抽象方法 加上abstract 去掉大括号 直接分号结束
抽象类 抽象方法所在类必须是抽象类，在class之后加上abstract
public abstract class Animal {
    public abstract void eat();
    public void method(){ }
}
```

使用

```java
1. 不能直接创建new抽象类对象
2. 必须要用一个子类继承抽象父类
3. 子类必须覆盖重写父类抽象方法
4. 创建子类对象使用
public class Cat extends Animal{
    @Override
    public void eat() {
    }
}
```

注意事项

```java
1. 抽象类不能创建对象
2. 可以有构造方法，提供子类创建对象时，初始化父类成员使用的
3. 抽象类不一定有抽象方法 有抽象方法一定是抽象类
4. 子类必须重写所有父类的抽象方法，除非子类也是抽象类
public abstract class Animal {
    public abstract void eat();
    public abstract void sleep();
}
public abstract class Cat extends Animal{
    @Override
    public void eat() {
        System.out.println("喵喵喵");
    }
}
public class WildCat extends Cat{
    @Override
    public void sleep() {
        System.out.println("呼呼呼");
    }

    public static void main(String[] args) {
        WildCat wildCat=new WildCat();
        wildCat.eat();
        wildCat.sleep();
    }
}
```

## 接口

```java
公共的规范标准
java7 包含内容
1. 常量 
2. 抽象方法
java8 包含
3. 默认方法
    public default    
    默认继承 子类也可覆盖重写
4. 静态方法
    通过接口名称调用静态方法 不能通过实现类调用，因为实现类可以继承多个接口，会产生重名问题
java9包含
5. 私有方法
    需要抽取一个共有方法，用来解决默认方法之间重复代码的问题
    但是这个共有方法不应该让实现类使用，应该私有化
    public interface USB {
     void eat();
     void sleep();
     public default void methodA(){
         /*System.out.println("111");
         System.out.println("222");
         System.out.println("333");*/
         methodCommon();
         System.out.println("AAA");
     }
     public default void methodB(){
         /*System.out.println("111");
         System.out.println("222");
         System.out.println("333");*/
         methodCommon();
         System.out.println("BBB");
     }
     private void methodCommon(){  //修饰符不需要加default
         System.out.println("111");
         System.out.println("222");
         System.out.println("333");
     }
}
5.1 普通私有方法 解决默认方法之间重复代码 
5.2 静态私有方法 解决静态方法之间重复代码
基本格式
抽象方法 public abstract 返回值类型 方法名称(参数列表)
```

成员变量

```
接口中也可以定义成员变量
但是必须使用 public static final三个关键字修饰 默认不写
从效果上看，这其实就是接口中的常量 
1. 常量必须赋值
```

```
1. 接口不能有静态代码块
2. 没有构造方法
3. 实现多个接口 存在重复抽象方法，只需要实现一次即可
4. 实现多个接口，存在重复的默认方法，必须覆盖重写
5. 一个类父类和接口默认方法重名，优先使用父类的方法
```

## 多态

```
父类引用指向子类对象
父类名称 对象名=new 自类名称 左父右子
```

```
成员变量 编译看左 运行看左
成员方法 编译看左，运行看右
```

好处

![](java基础\多态.png)

转型

![](java基础\转型.png)

## 正则表达式

```java
java内置正则表达式引擎 java.util.regex
```

```java
用户输入的是 19xx年
规则  1 9 0-9 0-9
相应的正则表达式 1 9 \d \d 
String str="1909";
boolean isTrue=str.matches("19\\d\\d");
System.out.println(isTrue); // true
```

匹配规则

![](java基础\正则表达式规则.png)

![](java基础\正则表达式规则2.png)

分组提取字符串

```
() 可以分组提取字符串 不用括号只能显示是否匹配成功
```

![](java基础\match.png)

![](java基础\match2.png)

![](java基础\group.png)

复杂正则表达式

![](java基础\复杂正则表达式.png)

贪婪匹配

![](java基础\贪婪匹配.png)

用？实现非贪婪匹配

![](java基础\非贪婪匹配.png)

搜索替换

![](java基础\搜索替换.png)

![](java基础\搜索字符串.png)

<img src="java基础\空格替换.png" style="zoom:67%;" />