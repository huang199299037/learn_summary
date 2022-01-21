# QT

## 信号和槽

**信号**

```
当对象改变其状态时，信号就由该对象发射(emit)出去，而且对象只负责发送信号，它不知道另一端是谁在接受这个信号
```

**槽**

```
用于接收信号，而且槽只是普通的对象成员函数，一个槽并不知道是否有任何信号与自己相连接
```

**QT宏**

### signals

```c++
# define signals Q_SIGNALS
# define Q_SIGNALS public QT_ANNOTATE_ACCESS_SPECIFIER(qt_signal)
# define QT_ANNOTATE_ACCESS_SPECIFIER(x)
====>
# define signals public
```

**slot**

```c++
# define slots Q_SLOTS
# define Q_SLOTS QT_ANNOTATE_ACCESS_SPECIFIER(qt_slot)
# define QT_ANNOTATE_ACCESS_SPECIFIER(x)
====>
# define slots
即空宏
```

**Q_OBJECT**

```c++
#define Q_OBJECT \
public: \
    QT_WARNING_PUSH \
    Q_OBJECT_NO_OVERRIDE_WARNING \
    static const QMetaObject staticMetaObject; \
    virtual const QMetaObject *metaObject() const; \
    virtual void *qt_metacast(const char *); \
    virtual int qt_metacall(QMetaObject::Call, int, void **); \
    QT_TR_FUNCTIONS \
private: \
    Q_OBJECT_NO_ATTRIBUTES_WARNING \
    Q_DECL_HIDDEN_STATIC_METACALL static void qt_static_metacall(QObject *, QMetaObject::Call, int, void **); \
    QT_WARNING_POP \
    struct QPrivateSignal {}; \
    QT_ANNOTATE_CLASS(qt_qobject, "")
```

====>

```
#define Q_OBJECT \
public: \
    static const QMetaObject staticMetaObject; \
    virtual const QMetaObject *metaObject() const; \
    virtual void *qt_metacast(const char *); \
    virtual int qt_metacall(QMetaObject::Call, int, void **); \
    QT_TR_FUNCTIONS \
private: \
    Q_DECL_HIDDEN_STATIC_METACALL static void qt_static_metacall(QObject *, QMetaObject::Call, int, void **); \
    struct QPrivateSignal {}; \

#  define QT_TR_FUNCTIONS \
    static inline QString tr(const char *s, const char *c = nullptr, int n = -1) \
        { return staticMetaObject.tr(s, c, n); } \
    QT_DEPRECATED static inline QString trUtf8(const char *s, const char *c = nullptr, int n = -1) \
        { return staticMetaObject.tr(s, c, n); }
```

**emit**

```
emit
# define emit
空宏
```

**SIGNAL**

字符串化操作(#)

当用作字符串化操作时，`#`的主要作用是将宏参数不经扩展地转换成字符串常量。

```c++
#define F abc  
#define B def  
#define FB(arg) #arg  
#define FB1(arg) FB(arg)  
  
FB(F B)  
FB1(F B) 
```

```
"F B";  
"abc def"; 
FB(F B) --> #F B -->"F B"    
FB1(F B) --> FB1(abc def) --> FB(abc def) --> #abc def --> "abc def"  
```

```
# define SIGNAL(a)   "2"#a
```

**SLOT**

```
# define SLOT(a)     "1"#a
```

### QT元对象系统

1. QT的元对象系统负责用于对象间通信的信号槽机制、运行时类型信息和属性系统。
2. 每个QObject子类都有唯一的一个QMetaObject实例，保存了这个QObject子类的所有元信息
3. 可以通过metaObject() 获取QMetaObject对象指针

![](D:\document\learn_summary\cpp\images\qt.png)

把成员变量名用字符串给连接起来了

<img src="D:\document\learn_summary\cpp\images\qt_signal.png" alt="image-20211125151523425" style="zoom:67%;" />

### Qt信号与槽机制的实现原理

Qt的神奇之处就在于，它使用信号(signal)与槽(slot)的技术来取代了回调。
在继续之前，我们先看一眼最最常用的 connnect 函数

```c++
connect(btn, "2clicked()", this, "1onBtnClicked()")
```

可能你会觉得稍有点眼生，因为为了清楚起见，我没有直接使用大家很熟悉的SIGNAL和SLOT两个宏，宏定义如下：

```c++
# define SLOT(a)     "1"#a
# define SIGNAL(a)   "2"#a
```

程序运行时，connect借助两个字符串，即可将信号与槽的关联建立起来，那么，它是如果做到的呢？C++的经验可以告诉我们：

> 1.类中应该保存有信号和槽的字符串信息
> 2.字符串和信号槽函数要关联

而这，就是通过神奇的**元对象**系统所实现的（Qt的元对象系统预处理器叫做moc，对文件预处理之后生成一个moc_xxx.cpp文件，然后和其他文件一块编译即可）。

接下来，我们不妨尝试用纯C++来实现自己的元对象系统(我们需要有一个自己的预处理器，本文中用双手来代替了，预处理生成的文件是db_xxx.cpp)。继续之前，我们可以先看一下我们最终的类定义

```c++
class Object    
{    
    DB_OBJECT  
public:    
    Object();    
    virtual ~Object();    
    static void db_connect(Object *, const char *, Object *, const char *);    
    void testSignal();    
db_signals:    
    void sig1();    
public db_slots:    
    void slot1();    
friend class MetaObject;    
private:    
     ConnectionMap connections;    
};    
```

**引入元对象系统**

**首先定义自己的信号和槽**
● 为了和普通成员进行区别(以使得预处理器可以知道如何提取信息)，我们需要创造一些"关键字"

> db_signals
> db_slots

```c++
class Object
{
public:
    Object();
    virtual ~Object();
db_signals:
    void sig1();
public db_slots:
    void slot1();
};
```

● 通过自己的预处理器，将信息提取取来，放置到一个单独的文件中（比如db_object.cpp）:
● 规则很简单，将信号和槽的名字提取出来，放到字符串中。可以有多个信号或槽，按顺序"sig1/nsig2/n"

```c++
static const char sig_names[] = "sig1/n";
static const char slts_names[] = "slot1/n";
```

● 这些信号和槽的信息，如何才能与类建立关联，如何被访问呢？
我们可以定义一个类，来存放信息：

```cpp
struct MetaObject
{
    const char * sig_names;
    const char * slts_names;
};
```

这样一来，我们的预处理器可以生成这样的 db_object.cpp 文件：

```cpp
include "object.h"

static const char sig_names[] = "sig1/n";
static const char slts_names[] = "slot1/n";
MetaObject Object::meta = {sig_names, slts_names};
```

信息提取的问题解决了：可是，还有一个严重问题，我们定义的关键字 C++ 编译器不认识啊，怎么办？通过定义一下宏，问题解决了：

**建立信号槽链接**

我们的最终目的就是：当信号被触发的时候，能找到并触发相应的槽。所以有了信号和槽的信息，我们就可以建立信号和槽的连接了。我们通过 db_connect 将信号和槽的对应关系保存到一个 mutlimap 中：

```cpp
struct Connection
{
    Object * receiver;
    int method;
};

class Object
{
public:
...
    static void db_connect(Object*, const char*, Object*, const char*);
...
private:
    std::multimap<int, Connection> connections;
```



```cpp
void Object::db_connect(Object* sender, const char* sig, Object* receiver, const char* slt)
{
    int sig_idx = find_string(sender->meta.sig_names, sig);
    int slt_idx = find_string(receiver->meta.slts_names, slt);
    if (sig_idx == -1 || slt_idx == -1) {
        perror("signal or slot not found!");
    } else {
        Connection c = {receiver, slt_idx};
        sender->connections.insert(std::pair<int, Connection>(sig_idx, c));
    }
}
```

首先从元对象信息中查找信号和槽的名字是否存在，如果存在，则将信号的索引和接收者的信息存入信号发送者的的一个map中。如果信号或槽无效，就什么都不用做了。

我们这儿定义了一个find_string函数，就是个简单的字符串查找(此处就不列出了)。

**信号的激活**

连接信息有了，我们看看信号到底是怎么发出的。
 在 Qt 中，我们都知道用 emit 来发射信号：

```java
class Object
{
public:
    void testSignal()
...
};

void Object::testSignal()
{
    db_emit sig1();
}
```

C++编译器不认识 db_emit ？加一行就行了

```cpp
#define db_emit
```

从前面我的Object定义中可以看到，所谓的信号或槽，都只是普普通通的C++类的成员函数。既然是成员函数，就需要函数定义：

●槽函数：由于它包含我们需要的功能代码，我们都会想到在 object.cpp 文件中去定义它，不存在问题。
●信号函数：它的函数体不需要自己编写。那么它在哪儿呢？这就是本节的内容了
 信号函数由我们的"预处理器"来生成，也就是它要定义在我们的 db_object.cpp 文件中：

我们预处理源文件时，就知道它是第几个信号。所以根据它的索引去调用和它关联的槽即可。具体工作交给了MetaObject类：

```cpp
class Object;
struct MetaObject
{
    const char * sig_names;
    const char * slts_names;

    static void active(Object * sender, int idx);
};
```

这个函数该怎么写呢：思路很简单
●从前面的保存连接的map中，找出与该信号关联的对象和槽
●调用该对象这个槽

```c++
typedef std::multimap<int, Connection> ConnectionMap;
typedef std::multimap<int, Connection>::iterator ConnectionMapIt;

void MetaObject::active(Object* sender, int idx)
{
    ConnectionMapIt it;
    std::pair<ConnectionMapIt, ConnectionMapIt> ret;
    ret = sender->connections.equal_range(idx);
    for (it=ret.first; it!=ret.second; ++it) {
        Connection c = (*it).second;
        //c.receiver->metacall(c.method);
    }
}
```

### 槽的调用

这个最后一个关键问题了，槽函数如何根据一个索引值进行调用。
●直接调用槽函数我们都知道了，就一个普通函数
●可现在通过索引调用了，那么我们必须定义一个接口函数

```cpp
class Object
{
    void metacall(int idx);
...
```

该函数如何实现呢？这个又回到我们的元对象预处理过程中了，因为在预处理的过程，我们能将槽的索引和槽的调用关联起来。

所以，在预处理生成的文件(db_object.cpp)中，我们很容易生成其定义：

```cpp
void Object::metacall(int idx)
{
    switch (idx) {
        case 0:
            slot1();
            break;
        default:
            break;
    };
}
```

至此，我们已经实现的一个简化的自己的信号与槽的程序。下面我们总体上看看程序的所有代码：

