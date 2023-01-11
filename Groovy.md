# Groovy

## 基础知识

### 运算符

三元运算符

```
条件？:值1:值2
```

```
displayName = user.name ? user.name : 'Anonymous'   
displayName = user.name ?: 'Anonymous'   
```

### 继承

**获取所有父类**

```java
public static List<Class> getSuperClasses(Object o) {
  List<Class> classList = new ArrayList<Class>();
  Class superclass = o.getClass().getSuperclass();
  while (superclass != null) {   
    classList.add(superclass);
    superclass = superclass.getSuperclass();
  }
  return classList;
}
```

### 枚举类

```java
values() 遍历每一个枚举类的对象
toString() 返回枚举类的名称

package biren.main.logging

import com.cloudbees.groovy.cps.NonCPS

/**
 * Enumeration for log levels
 */
enum LogLevel implements Serializable {

  ALL(0, 0),
  TRACE(2, 8),
  DEBUG(3, 12),
  INFO(4, 0),
  DEPRECATED(5, 93),
  WARN(6, 202),
  ERROR(7, 5),
  FATAL(8, 9),
  NONE(Integer.MAX_VALUE, 0)

  Integer level

  static COLOR_CODE_PREFIX = "1;38;5;"

  Integer color

  private static final long serialVersionUID = 1L

    LogLevel(Integer level, Integer color) {
    this.level = level
    this.color = color
  }

  @NonCPS
  static LogLevel fromInteger(Integer value) {
    for (lvl in values()) {
      if (lvl.getLevel() == value) return lvl
    }
    return INFO
  }

  @NonCPS
  static LogLevel fromString(String value) {
    for (lvl in values()) {
      if (lvl.toString().equalsIgnoreCase(value)) return lvl
    }
    return INFO
  }

  @NonCPS
  String getColorCode() {
    return COLOR_CODE_PREFIX + color.toString()
  }

  static void main(String[] args) {
    for (lvl in values()) {
      println(lvl.toString())
    }
  }
}

```



## 闭包

### 1. 语法

#### 1.1. 定义一个闭包

闭包是Groovy中非常重要的一个数据类型或者说一种概念。闭包是一种数据类型，它代表了一段可执行的代码

> 闭包的语法定义： { [closureParameters -> ] statements }

其中[closureParameters->]部分是一个可选的以逗号分隔的参数列表

statements可以为空，一行或者多行代码。

当参数列表确定时，`->`是必须的，他负责把参数和闭包的代码块分开，代码块可以由一行或者多行语句组成

一些闭包常用的定义形式：

```groovy
{ println "hello world" }                                      
{ println it }                                
{ name -> println name }                            
{ String x, int y ->                                
    println "hey ${x} the value is ${y}"
}
{ reader ->                                         
    def line = reader.readLine()
    line.trim()
}
```

#### 1.2. 闭包作为一个对象

闭包实质上是一个`groovy.lang.Closure`类的实例，虽然他是一个代码块，但是他可以为任何一个变量或者字段赋值，闭包中可以包含代码的逻辑，闭包中的最后一行语句，表示该闭包的返回值，不论该语句是否有`return`关键字

```groovy
//可以将闭包分配给一个变量，这个变量就是groovy.lang.Closure的一个实例
def listener = { e -> println "Clicked on $e.source" }      
assert listener instanceof Closure

//如果不使用def，可以将一个闭包分配给一个类型为groovy.lang.Closure的变量
Closure callback = { println 'Done!' }     

//可以通过使用groovy.lang.Closure的泛型来指定闭包的返回类型
Closure<Boolean> isTextFile = {
    File it -> it.name.endsWith('.txt')                     
}
```

#### 1.3. 调用闭包

闭包作为一个匿名的代码块，可以像方法那样被调用，如果定义了一个不需要参数的闭包：

```groovy
def code = { 123 }
```

闭包内的代码只有在闭包被调用时才会执行，调用可以像常规方法那样来完成

```groovy
assert code() == 123
```

或者，可以显式的调用`call`方法

```groovy
assert code.call() = 123
```

example：

```groovy
def isOdd = { int i -> i%2 != 0 }                           
assert isOdd(3) == true                                     
assert isOdd.call(2) == false                               

def isEven = { it%2 == 0 }                                  
assert isEven(3) == false                                   
assert isEven.call(2) == true 

def code = { println("hello") }
def x=code()
println(x)

hello
null


def code = { 123 }
assert code() == 123
assert code.call() = 123
```

**和方法不一样的是，闭包始终有一个返回值**

### 2. 参数

#### 2.1. 正常参数

闭包的参数遵循与常规方法的参数相同的原则

- 可选的类型
- 名字
- 可选的默认值

参数用`,`分隔

```groovy
def closureWithOneArg = { str -> str.toUpperCase() }
assert closureWithOneArg('groovy') == 'GROOVY'

def closureWithOneArgAndExplicitType = { String str -> str.toUpperCase() }
assert closureWithOneArgAndExplicitType('groovy') == 'GROOVY'

def closureWithTwoArgs = { a,b -> a+b }
assert closureWithTwoArgs(1,2) == 3

def closureWithTwoArgsAndExplicitTypes = { int a, int b -> a+b }
assert closureWithTwoArgsAndExplicitTypes(1,2) == 3

def closureWithTwoArgsAndOptionalTypes = { a, int b -> a+b }
assert closureWithTwoArgsAndOptionalTypes(1,2) == 3

def closureWithTwoArgAndDefaultValue = { int a, int b=2 -> a+b }
assert closureWithTwoArgAndDefaultValue(1) == 3
```

闭包提供了询问自己参数个数的方法，无论在闭包内或者闭包外

```groovy
c= {x,y,z-> getMaximumNumberOfParameters() }  
assert c.getMaximumNumberOfParameters() == 3  
assert c(4,5,6) == 3  
```

#### 2.2. 隐式参数

当一个闭包没有明确定义一个参数列表（使用 - >）时，闭包总是定义一个隐式参数，并将其命名为`it`。

```groovy
def greeting = { "Hello, $it!" }
assert greeting('Patrick') == 'Hello, Patrick!'
```

如果想声明一个不接受参数的闭包，并且必须限制为没有参数的调用，那么必须声明一个明确的空参数列表：

```groovy
def magicNumber = { -> 42 }

// this call will fail because the closure doesn't accept any argument
magicNumber(11)
```

闭包的一些快捷写法，当闭包作为闭包或方法的最后一个参数。可以将闭包从参数圆括号中提取出来接在最后，如果闭包是唯一的一个参数，则闭包或方法参数所在的圆括号也可以省略。对于有多个闭包参数的，只要是在参数声明最后的，均可以按上述方式省略。见代码：

```groovy
def runTwice = { a, c -> c(c(a)) }  
assert runTwice( 5, {it * 3} ) == 45 //usual syntax  
assert runTwice( 5 ){it * 3} == 45  
    //when closure is last param, can put it after the param list  
  
def runTwiceAndConcat = { c -> c() + c() }  
assert runTwiceAndConcat( { 'plate' } ) == 'plateplate' //usual syntax  
assert runTwiceAndConcat(){ 'bowl' } == 'bowlbowl' //shortcut form  
assert runTwiceAndConcat{ 'mug' } == 'mugmug'  
    //can skip parens altogether if closure is only param  
  
def runTwoClosures = { a, c1, c2 -> c1(c2(a)) }  
    //when more than one closure as last params  
assert runTwoClosures( 5, {it*3}, {it*4} ) == 60 //usual syntax  
assert runTwoClosures( 5 ){it*3}{it*4} == 60 //shortcut form  
```

#### 2.3. 可变参数

闭包可以象任何其他方法一样声明可变参数

```groovy
def concat1 = { String... args -> args.join('') }           
assert concat1('abc','def') == 'abcdef'                     
def concat2 = { String[] args -> args.join('') }            
assert concat2('abc', 'def') == 'abcdef'

def multiConcat = { int n, String... args ->                
    args.join('')*n
}
assert multiConcat(2, 'abc','def') == 'abcdefabcdef'
```

### 3. 委托策略

#### 3.1. This  owner delegate

Groovy的闭包比Java的Lambda表达式功能更强大。原因就是Groovy闭包可以修改委托对象和委托策略。这样Groovy就可以实现非常优美的领域描述语言（DSL）了。Gradle就是一个鲜明的例子

Groovy闭包有三个相关的对象

- this 即闭包定义所在的类。
- owner 即闭包定义所在的对象或闭包。
- delegate 即闭包中引用的第三方对象。

##### 3.1.1 this

this代表闭包定义所在的类

在闭包中，调用`getThisObject`将返回闭包定义的闭包类，和显式的调用`this`一样

```groovy
class Enclosing {
    void run() {
        def whatIsThisObject = { getThisObject() }          
        assert whatIsThisObject() == this                   
        def whatIsThis = { this }                           
        assert whatIsThis() == this                         
    }
}
Enclosing en=new Enclosing()
en.run()

class EnclosedInInnerClass {
    class Inner {
        Closure cl = { this }                               
    }
    void run() {
        def inner = new Inner()
        assert inner.cl() == inner                          
    }
}
class NestedClosures {
    void run() {
        def close = {
            def cl = { this }                               
            cl()
        }
        assert nestedClosures() == this                     
    }
}
```

在Inner中调用`this` 返回的是Inner这个类，而不是外层的EnclosedInInnerClass

在NestedClosures类中，cl定义在闭包close中，调用this返回最近的外层类NestedClosures，而不是闭包close

可以像下面这样调用封闭类的方法

```groovy
class Person {
    String name
    int age
    String toString() { "$name is $age years old" }

    String dump() {
        def cl = {
            String msg = this.toString()               
            println msg
            msg //返回值
        }
        cl()
    }
}
def p = new Person(name:'Janice', age:74)
assert p.dump() == 'Janice is 74 years old'
```

##### 3.1.2 Owner

Owner代表闭包定义所在的对象或闭包

```groovy
class Enclosing {
    void run() {
        def whatIsOwnerMethod = { getOwner() }               
        assert whatIsOwnerMethod() == this                   
        def whatIsOwner = { owner }                          
        assert whatIsOwner() == this                         
    }
}
class EnclosedInInnerClass {
    class Inner {
        Closure cl = { owner }                               
    }
    void run() {
        def inner = new Inner()
        assert inner.cl() == inner                           
    }
}
class NestedClosures {
    void run() {
        def nestedClosures = {
            def cl = { owner }                               
            cl()
        }
        assert nestedClosures() == nestedClosures            
    }
}
```

##### 3.1.3 Delegate of a closure

可以使用`delegate`属性或者调用`getDelegate`方法来访问闭包中的`delegate`,`delegate`在Groovy中是一个很重要的概念，`delegate`需要我们手动指定

```groovy
class Enclosing {
    void run() {
        def cl = { getDelegate() }                          
        def cl2 = { delegate }                              
        assert cl() == cl2()                                
        assert cl() == this                                 
        def enclosed = {
            { -> delegate }.call()                          
        }
        assert enclosed() == enclosed                       
    }
}
```

闭包的`delegate`可以被改变成任何对象。 我们通过创建两个不是彼此的子类的类来说明这一点，但都定义了一个名为name的属性：

```groovy
class Person {
    String name
}
class Thing {
    String name
}

def p = new Person(name: 'Norman')
def t = new Thing(name: 'Teapot')
```

然后定义一个闭包通过`delegate`来调用name属性

```groovy
def upperCasedName = { delegate.name.toUpperCase() }
```

然后通过更改闭包的委托，可以看到目标对象将会改变

```groovy
upperCasedName.delegate = p
assert upperCasedName() == 'NORMAN'
upperCasedName.delegate = t
assert upperCasedName() == 'TEAPOT'
```

上述行为与在闭包的词法范围中定义目标变量并没有区别

```groovy
def target = p
def upperCasedNameUsingVar = { target.name.toUpperCase() }
assert upperCasedNameUsingVar() == 'NORMAN'
```

##### 3.1.4 Delegation strategy（委托策略）

无论什么时候，在闭包中，访问某个属性时都没有明确地设置接收者对象，那么就会调用一个委托策略：

```groovy
class Person {
    String name
}
def p = new Person(name:'Igor')
def cl = { name.toUpperCase() }                 
cl.delegate = p

//name property be resolved transparently on the delegate object!
assert cl() == 'IGOR'  
```

调用name属性并没有执行是谁的name属性，然后把闭包cl的delegate设置为p

这里没有显式的给delegate设置一个接收者，但是能成功访问到name属性 因为相应的属性解析策略：

- Closure.OWNER_FIRST，默认策略，首先从owner上寻找属性或方法，找不到则在delegate上寻找。
- Closure.DELEGATE_FIRST，和上面相反。首先从delegate上寻找属性或者方法
- Closure.OWNER_ONLY，只在owner上寻找，delegate被忽略。
- Closure.DELEGATE_ONLY，和上面相反。只在delegate上寻找，owner被忽略。
- Closure.TO_SELF，高级选项，让开发者自定义策略。

```groovy
class Person {
    String name
    def pretty = { "My name is $name" }             
    String toString() {
        pretty()
    }
}
class Thing {
    String name                                     
}

def p = new Person(name: 'Sarah')
def t = new Thing(name: 'Teapot')

assert p.toString() == 'My name is Sarah'           
p.pretty.delegate = t                               
assert p.toString() == 'My name is Sarah'  
```

Person和Thing都定义了name属性，使用默认策略，首先会在Owner上寻找，所以即使我们把delegate换成t,输出结果还是一样的

下面改变一下委托策略

```groovy
p.pretty.resolveStrategy = Closure.DELEGATE_FIRST
assert p.toString() == 'My name is Teapot'
```

把委托策略改成Closure.DELEGATE_FIRST，则会首先去delegate寻找name属性，如果没有找到，再去owner上寻找，但是delegate有name的定义，所以输出结果为“My name is Teapot”

`delegate first` vs `delegate only` (`owner first`vs`owner only`)

```groovy
class Person {
    String name
    int age
    def fetchAge = { age }
}
class Thing {
    String name
}

def p = new Person(name:'Jessica', age:42)
def t = new Thing(name:'Printer')
def cl = p.fetchAge
cl.delegate = p
assert cl() == 42
cl.delegate = t
assert cl() == 42
cl.resolveStrategy = Closure.DELEGATE_ONLY
cl.delegate = p
assert cl() == 42
cl.delegate = t
try {
    cl()
    assert false
} catch (MissingPropertyException ex) {
    // "age" is not defined on the delegate
}
```

only时，在对应的对象上找不到时，不会接着去别处找，而是抛出一个异常

### 4.函数式编程



参考资料 https://www.jianshu.com/p/c02a456e1943?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation

## 导包

```
https://stackoverflow.com/questions/55402430/how-can-i-use-groovy-classes-in-other-packages-without-jar
https://www.javaroad.cn/questions/289412
The Groovy command line processor.
      -cp, -classpath, --classpath=<path>
                             Specify where to find the class files - must be
                               first argument
```

## 执行shell命令

普通文件中执行

```groovy
def process = "echo hello world".execute()
print "Output: " + process.text
print "Exit code: " + process.exitValue()
```

在pipeline中执行

```groovy
// Git committer email
GIT_COMMIT_EMAIL = sh (
    script: 'git --no-pager show -s --format=\'%ae\'',
    returnStdout: true
).trim()
echo "Git committer email: ${GIT_COMMIT_EMAIL}"

BUILD_FULL = sh (
    script: "git log -1 --pretty=%B | grep '\\[jenkins-full]'",
    returnStatus: true
) == 0
echo "Build full flag: ${BUILD_FULL}"
```

## Call方法

```
在Groovy中我们给类可以增加call方法，然后隐式调用call方法。调用时，仅需输入括号和可选的参数,使用Groovy 写DSL尤其有用。同时可以增加多个带不同参数的call方法实现重载，在运行时根据参数决定。
```

```groovy
class User {
    String userName;
    String address;

    def call(final String userName){
        this.userName = userName
        this
    }

    def call(final Map data){
        userName = data.get("userName") ?: userName
        address  = data.get("address")  ?: address
        this
    }

    def call(final Closure runCode){
        runCode this
    }

    static void main(String [] args){
        User user = new User( userName:"tommy",address:"xuzhou")

        user "jack"
        assert user.userName == "jack" // call with string parameter

        Map data = [userName:"tommy",address:"shanghai"]

        user data  // call with map parameter

        assert "tommy"  == user.userName 
        assert "shanghai" == user.address  //true

        user{
            println it.address  // shanghai  call with Closure
        }

        //call with Closure
        def json =  user {
            new groovy.json.JsonBuilder(["user": ["name": it.userName, "address": it.address]]).toString()
        }

        assert json == '{"user":{"name":"tommy","address":"shanghai"}}'
    }
}
```

## 设计模式

### 工厂模式

参考资料：https://www.zhihu.com/question/42975862/answer/1239305317 https://zhuanlan.zhihu.com/p/239952896  https://blog.csdn.net/qq_45867375/article/details/124597130

问题：既然都有了构造函数，何必再折腾那么多事情呢

Factroy要解决的问题是：**希望能够创建一个对象，但创建过程比较复杂，希望对外隐藏这些细节**

> 创建过程不复杂，不需要使用factory模式

example

```java
例子1: 
	创建对象可能是一个pool里的，不是每次都凭空创建一个新的。而pool的大小等参数可以用另外的逻辑去控制。比如连接池对象，线程池对象就是个很好的例子。
例子2:
	对象代码的作者希望隐藏对象真实的的类型，而构造函数一定要真实的类名才能用。比如作者提供了
    abstract class Foo { 
     //...
    }
	而真实实现类是
    public class FooImplV1 extends Foo {
      // ...
    }
	但他不希望你知道FooImplV1的存在（没准下次就改成V2了），只希望你知道Foo，所以他必须提供某种类似于这样的方式让你用：
    Foo foo = FooCreator.create();
	// do something with foo ...
例子3:
	对象创建时会有很多参数来决定如何创建出这个对象。比如你有一个数据写在文件里，可能是xml也可能是json。这个文件的数据可以变成一个对象，大概就可以搞成。
     Foo foo = FooCreator.fromFile("/path/to/the/data-file.ext");
	再比如这个文件是描述一个可以显示在浏览器的UI的基础数据。而不同浏览器可以正确显示的需要的数据不太一样。这个“不一样”可以表达为：
     Foo foo = FooCreator.fromFile("/path/to/the/data-file.ext", BrowserType.CHROME);
例子4：
    简化一些常规的创建过程。上面可以看到根据配置去创建一个对象也很复杂。但可能95%的情况我们就创建某个特定类型的对象。这时可以弄个函数直接省略那些配置过程。纯粹就是为了方便
例子5: 
	创建一个对象有复杂的依赖关系，比如Foo对象的创建依赖A，A又依赖B，B又依赖C……。于是创建过程是一组对象的的创建和注入。手写太麻烦了。所以要把创建过程本身做很好地维护。对，Spring IoC就是这么干的。
例子6: 
	你知道怎么创建一个对象，但是无法把控创建的时机。你需要把“如何创建”的代码塞给“负责什么时候创建”的代码。后者在适当的时机，就回调创建的函数。
例子7: 
	避免在构造函数中抛出异常。"构造函数里不要抛出异常"这条原则很多人都知道。不在这里展开讨论。但问题是，业务要求必须在这里抛一个异常怎么办？就像上面的Foo要求从文件读出来数据并创建对象。但如果文件不存在或者磁盘有问题读不出来都会抛异常。因此用FooCreator.fromFile这个工厂来搞定异常这件事。

```

## 共享库

参考资料https://www.jenkins.io/doc/book/pipeline/shared-libraries/ https://stackoverflow.com/questions/58029722/jenkins-shared-library-unable-to-import-a-package-no-such-property https://stackoverflow.com/questions/60862291/import-class-from-shared-library-in-jenkinsfile https://stackoverflow.com/questions/64193250/how-vars-in-jenkins-shared-libraries-work https://stackoverflow.com/questions/63948975/importing-class-from-git-unable-to-resolve-class/65942947#65942947

### 动态共享库（dynamic shared library）library

### 全局共享库（global shared library）@Library

不同点

```
1、沙盒限制不适用于全局共享库中的代码，因为此类库中的代码被视为受信任的（毕竟，它是由管理员全局配置的）。 动态共享库不是这种情况，因为它们是由用户定义的，因此是不可信任的。
2、来自全局共享库的类可以在 Jenkinsfile 中静态引用并作为 Jenkinsfile 编译的一部分得到解析，但这对于动态共享库是不可能的，因为这些库是在运行时定义的，并且在运行时不能交叉检查类 汇编。
```

```
1、库步骤返回可以分配给变量以供进一步使用的库的表示形式，例如 def myLib = library identifier: ...
2、可以使用类在库对象上的完全限定名称来引用类，例如 def myCls = myLib.com.mycom.mypkg.MyClass
3、您收到的不是实际的 Groovy 类，而是一个类似代理的对象，它只允许对底层类进行有限的操作。
4、您可以使用 new 函数实例化一个新实例，它的工作方式与 new 运算符非常相似。 您传递的参数与传递给构造函数的参数相同，例如 myCls.new(arg1, arg2)
5、您可以获得一个静态变量（虽然您不能设置它）或使用代理对象调用静态方法，就像它是真正的类一样。 如果您正在使用 new 实例化一个对象，那么您也可以通过该实例访问静态成员（正常的 Groovy 语义适用）。
6、在这两种类型的库中，var 文件使用相同的机制公开（也称为全局变量和自定义步骤），因此在用法上没有区别。 此外，var 文件在库的上下文中执行，因此在 var 文件中，您不需要特殊语法来访问类，无论它们是在全局共享库还是动态共享库中。
7、现在要回答 OP 中的真正问题，可以从动态共享库调用 CheckoutSCM。 如果定义为静态函数，则可以使用 myLib.com.company.DeploySteps.CheckoutSCM('repo-here') 调用，如果定义为非静态方法，则可以使用 myLib.com 调用 .company.DeploySteps.new(...).CheckoutSCM('repo-here')。 但是，在这两种情况下，DeploySteps 类将无法访问步骤 API，因此即使是简单的回显也不起作用。 一种传统的解决方法是提供 Jenkinsfile 的 this 实例作为参数（例如，CheckoutSCM(this, 'repo-here')），然后将其分配给函数内的 steps 参数（可以命名为任何名称）。 然后，您将调用步骤参数上的所有步骤调用，例如 steps.echo '...'。
```

>  不能在@Library中加入变量

> 非显示的类不需要导入

### 日志类

```
日志输出级别（由高到低）

NONE：最高级别用于关闭所有的日志记录

FATAL：严重错误，表示程序已经无法运行了。

ERROR：普通错误，程序还可以运行，大多数难以优雅处理的异常都属于 Error 范畴。

WARN：警告信息，预期的错误

DEPRECATED：调用废弃接口

INFO：有意义的事件信息，如程序启动，关闭事件，收到请求事件等；

DEBUG：调试信息，可记录详细的业务处理到哪一步了，以及当前的变量状态；

TRACE：非常具体的信息，只能用于开发调试使用，更详细的跟踪信息

ALL：输出所有信息
```

```java
    public String testLog(){
	    //调试的时候知道进入了方法
        Logger.debug("enter getting content");
        String content=CacheManager.getCachedContent();

        if(content==null){
		   // 使用warn因为程序还可以继续执行，但是类似的警告太多可能说明缓存服务不可用了，值得引起注意
            Logger.warn("Got empty content from cache,need to perform database lookup");
            Connection conn=new ConnectionFactory.getConnection();
            if (conn==null){
			   // 详细的错误信息
                Logger.error("Can't get database connection,failed to return content");
            }else{
                try{
                    content=conn.query();
                }catch (IOException e){
                //记录错误堆栈信息
                    Logger.error("Failed to perform database lookup",e);
                } finally {
                    ConnectionFactory.releaseConnection(conn);
                    
                }
            }
        }
        // 调试的时候知道退出了方法
        Logger.debug("returning content: "+content);
        return content;
    }
	
	Logger.INFO("start pipeline")
    something
    testLog()
    Logger.INFO("endup pipeline")
```



##pipeline

详解流程参考资料：https://zhuanlan.zhihu.com/p/563981209

参考资料 https://blog.csdn.net/qq_34556414/article/details/117419824

> pipeline中同一个stage可以包含两个script

### 环境变量

**列出环境变量**

```groovy
1、${YOUR_JENKINS_HOST}/env-vars.html在Jenkins主服务器上打开页面，以获取HTML页面上列出的所有环境变量的列表。
2、可以通过执行printenvshell命令列出所有环境变量 ，使用printenv | sort命令组合来获取环境变量的排序列表可能很有用
pipeline {
  agent any
  stages {
    stage('Preparation') {
      steps {
        script{
            sh "printenv | sort"
        }
      }
    }
  }
}
```

**读取环境变量**

```
以在通过env对象的管道步骤中访问环境变量，例如，env.BUILD_NUMBER将返回当前的内部版本号
```

**设置环境变量**

```groovy
可以使用environment { }block 来声明性地设置环境变量，使用env.VARIABLE_NAME或命令来设置环境变量withEnv(["VARIABLE_NAME=value"]) {}。
 
pipeline {
    agent any
 
    environment {
        FOO = "bar"
    }
 
    stages {
        stage("Env Variables") {
            environment {
                NAME = "Alan"
            }
 
            steps {
                echo "FOO = ${env.FOO}"
                echo "NAME = ${env.NAME}"
 
                script {
                    env.TEST_VARIABLE = "some test value"
                }
 
                echo "TEST_VARIABLE = ${env.TEST_VARIABLE}"
 
                withEnv(["ANOTHER_ENV_VAR=here is some value"]) {
                    echo "ANOTHER_ENV_VAR = ${env.ANOTHER_ENV_VAR}"
                }
            }
        }
    }
}
FOO = bar
NAME = Alan
TEST_VARIABLE = some test value
ANOTHER_ENV_VAR = here is some value
```

**覆盖环境变量**

```
Jenkins Pipeline支持覆盖环境变量。注意一些规则。

1、该withEnv(["env=value]) { }块可以覆盖任何环境变量。
2、使用environment {}块设置的变量不能使用命令式env.VAR = "value"赋值覆盖。
3、命令式env.VAR = "value"分配只能覆盖使用命令式创建的环境变量。
```

```groovy
pipeline {
    agent any
 
    environment {
        FOO = "bar"
        NAME = "Joe"
    }
 
    stages {
        stage("Env Variables") {
            environment {
                NAME = "Alan" // overrides pipeline level NAME env variable
                BUILD_NUMBER = "2" // overrides the default BUILD_NUMBER
            }
 
            steps {
                echo "FOO = ${env.FOO}" // prints "FOO = bar"
                echo "NAME = ${env.NAME}" // prints "NAME = Alan"
                echo "BUILD_NUMBER =  ${env.BUILD_NUMBER}" // prints "BUILD_NUMBER = 2"
 
                script {
                    env.SOMETHING = "1" // creates env.SOMETHING variable
                }
            }
        }
 
        stage("Override Variables") {
            steps {
                script {
                    env.FOO = "IT DOES NOT WORK!" // it can't override env.FOO declared at the pipeline (or stage) level
                    env.SOMETHING = "2" // it can override env variable created imperatively
                }
 
                echo "FOO = ${env.FOO}" // prints "FOO = bar"
                echo "SOMETHING = ${env.SOMETHING}" // prints "SOMETHING = 2"
 
                withEnv(["FOO=foobar"]) { // it can override any env variable
                    echo "FOO = ${env.FOO}" // prints "FOO = foobar"
                }
 
                withEnv(["BUILD_NUMBER=1"]) {
                    echo "BUILD_NUMBER = ${env.BUILD_NUMBER}" // prints "BUILD_NUMBER = 1"
                }
            }
        }
    }
}
```

**布尔值存储在环境变量中**

```
存储为环境变量的每个值都将转换为String。当存储布尔false值时，它将转换为"false"。如果要在布尔表达式中正确使用该值，则需要调用"false".toBoolean()method
"false".toBoolean()method转换时忽略大小写
```

```groovy
pipeline {
    agent any
 
    environment {
        IS_BOOLEAN = false
    }
 
    stages {
        stage("Env Variables") {
            steps {
                script {
                    if (env.IS_BOOLEAN) {
                        echo "You can see this message, because \"false\" String evaluates to Boolean.TRUE value"
                    }
 
                    if (env.IS_BOOLEAN.toBoolean() == false) {
                        echo "You can see this message, because \"false\".toBoolean() returns Boolean.FALSE value"
                    }
                }
            }
        }
    }
}
```

**使用sh捕获环境变量**

```
还可以将shell命令的输出捕获为环境变量。需要使用sh(script: 'cmd', returnStdout:true)格式来强制sh步骤返回输出，以便可以捕获它并将其存储在变量中
```

```groovy
pipeline {
    agent any
 
    environment {
        LS = "${sh(script:'ls -lah', returnStdout: true)}"
    }
 
    stages {
        stage("Env Variables") {
            steps {
                echo "LS = ${env.LS}"
            }
        }
    }
```

**流水线全局变量参考**

内置变量

```
BUILD_NUMBER          //构建号
BUILD_ID              //构建号
BUILD_DISPLAY_NAME    //构建显示名称
JOB_NAME              //项目名称
              
EXECUTOR_NUMBER       //执行器数量
NODE_NAME             //构建节点名称
WORKSPACE             //工作目录
JENKINS_HOME          //Jenkins home
JENKINS_URL           //Jenkins地址
BUILD_URL             //构建地址
JOB_URL               //项目地址
```

currentbuild变量

```
result  currentResult   //构建结果
displayName      //构建名称  #111
description      //构建描述
duration         //持续时间
```

流水线中变量定义引用

```
 变量的类型：两种类型的变量。
1.Jenkins系统内置变量 （全局变量）
2.Pipeline中定义变量（全局/局部变量）

Jenkins系统内置变量：是Jenkins系统在安装部署后预先定义好的变量。这些变量可以通过Jenkins流水线语法页面看到具体有哪些。这些变量都是全局的可以使用"${env.变量名}引用。

Pipeline中的变量：

def name = "devops"
String name = "devops"

如果你在Jenkins图形界面设置了参数化构建，那么这些参数也都变成了Jenkins全局变量，可以使用与Jenkins内置变量相同的引用方式。

如果在某个stage定义的变量默认是局部变量，在后续的stage中可能语法引用，所以如果需要引用最好定义为全局变量。

全局变量的定义方式：
env.name = "devops"
引用方式： "${env.name}"
```

### steps

参考资料 https://plugins.jenkins.io/workflow-basic-steps/ https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/ https://www.jenkins.io/doc/book/pipeline/syntax/

```
Pipeline: Basic Steps 插件
```

父类

```groovy
(Jenkinsfile)WorkflowScript@47dc6d52 (pipelineTest) pipelineTest@465781ca
class org.jenkinsci.plugins.workflow.cps.CpsScript
class com.cloudbees.groovy.cps.SerializableScript
class groovy.lang.Script
class groovy.lang.GroovyObjectSupport
class java.lang.Object

println(this)
Class superclass = this.getClass().getSuperclass();
while (superclass != null) {
    println(superclass)
    superclass = superclass.getSuperclass();
}
```

## br_ci_lib

