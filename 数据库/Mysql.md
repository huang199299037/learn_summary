# Mysql初级

## ubuntu安装mysql

参考资料 https://blog.csdn.net/weixin_46990523/article/details/127442304

https://segmentfault.com/a/1190000039203507

```
卸载：
https://blog.csdn.net/weixin_46272577/article/details/124564640
dpkg --list | grep mysql
sudo apt-get --purge remove mysql*

sudo apt install mysql-server

MySQL服务管理
sudo service mysql status # 查看服务状态
sudo service mysql start # 启动服务
sudo service mysql stop # 停止服务
sudo service mysql restart # 重启服务
112357Aa.
```

### 登陆

**方法一：默认账户登录**

查看密码使用`sudo cat /etc/mysql/debian.cnf`这条查看

**方法二：直接进入mysql**
命令：`sudo mysql`

#### 本地 root 用户

到了关键的一步，其实现在你的数据库中就有一个叫做 `host` 字段为 `localhost` 的 `root` 的用户我们需要做如下几件事情：

- 修改初始 root 用户的密码（修为我们自己的密码）

> 不需要授予访问权限等操作，因为默认已经有了

**重置密码**

重置 root 账户密码

```pgsql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```

**刷新权限**

```abnf
FLUSH PRIVILEGES;
```

#### 远程root 用户

如果需要远程登陆：

- 创建一个 `host` 字段为 `%` 的 root 用户（创建用户的同时设置密码）
- 授权所有数据库的访问权限
- 刷新权限列表

> 有些 uu 就会很奇怪为什么要创建两个 `root` 用户呢？这个和 `mysql` 的用户管理方式有关系：`localhost` 表示本机登录；`%` 表示远程登陆。
> 如果 `root` 用户只有 `%` ，那就只能除了本机外的其他计算机才能登陆 mysql server，如果用户只有 `localhost`，那只有本机可以登录，远程计算机不能登录 mysql server
> 那么 mysql 为什么要这么设计呢？可能是为了安全吧！这样我们可以为 root 设置两个不同的密码，`localhost` 环境下设置一个很简单的密码；`%` 环境下就可以极其复杂，诸如：`MnRmsrdm9wjkT5XC9Y2F5b4IouAPZBfx` （注意 mysql 的密码有长度限制，好像是 32 个字符长度）

**新建一个 host 为 % 的 root用户，密码随意**

```sql
create user 'root'@'%' identified by 'yourpassword';
```

**授权**

```plaintext
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
```

**刷新权限**

```abnf
FLUSH PRIVILEGES;
```

通过如下的方式查看我们的用户信息情况

```
mysql> use mysql
mysql> select host,user,authentication_string from user;
+-----------+------------------+------------------------------------------------------------------------+
| host      | user             | authentication_string                                                  |
+-----------+------------------+------------------------------------------------------------------------+
| %         | root             | *96E7A848AB10957950D4E01EE8D60E361205A073                              |
| localhost | debian-sys-maint | $A$005$)h&}?mq<1rx*2^ut5na8v15kXP0XBBiK63RFLJBF2vHY0DYnmVHNA/PoHA |
| localhost | mysql.infoschema | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.session    | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.sys        | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | root             | *96E7A848AB10957950D4E01EE8D60E361205A073                              |
+-----------+------------------+------------------------------------------------------------------------+
6 rows in set (0.00 sec)
```

##### 远程连接

光设置需要登陆用户的 host 为 % 是不够的，因为 mysql 的配置文件中禁止了远程登录，需要去修改一下配置文件。

先关停mysql服务

```arduino
sudo systemctl stop mysql
```

编辑mysql配置文件

```awk
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

注销掉

```1c
#bind-address           = 127.0.0.1
```

在开启mysql服务即可`sudo service mysql start`

==**注意**==

腾讯云服务器限制了端口，需要在防火墙-》添加规则-》端口3306

### mysql执行流程

- 启动`MySQL`服务器程序。
- 启动`MySQL`客户端程序并连接到服务器程序。
- 在客户端程序中输入一些命令语句作为请求发送到服务器程序，服务器程序收到这些请求后，会根据请求的内容来操作具体的数据并向客户端返回操作结果。

### DBeaver显示系统数据库不全

https://blog.csdn.net/xj19940904/article/details/116464350

```
解决：
1、右击连接名
2、点击Connection view
3、选中显示系统对象
```

### 连接数据库

```
mysql -h主机名  -u用户名 -p密码
```

各个参数的意义如下：

| 参数名 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| `-h`   | 表示启动服务器程序的计算机的域名或者IP地址，如果服务器程序就运行在本机的话，可以省略这个参数，也可以填`localhost`或者`127.0.0.1`。也可以写作 `--host=主机`的形式。 |
| `-u`   | 表示用户名，我们刚刚安装完，作为超级管理员的我们的用户名是`root`。也可以写作 `--user=用户名`的形式。 |
| `-p`   | 表示密码。也可以写作 `--password=密码`的形式。               |

### MySQL语句使用注意事项

**命令结束符号。**

在书写完一个命令之后需要以下边这几个符号之一结尾：

- `;`
- `\g`
- `\G`

```
mysql> SELECT NOW();
+---------------------+
| now()               |
+---------------------+
| 2023-03-05 01:14:31 |
+---------------------+
1 row in set (0.00 sec)

结果中1 row in set (0.00 sec)的意思是结果只有1行数据，用时0.00秒。使用\g可以起到一样的效果：
mysql> select now()\g
+---------------------+
| now()               |
+---------------------+
| 2023-03-05 01:16:18 |
+---------------------+
1 row in set (0.00 sec)
```

`\G`有一点特殊，它并不以表格的形式返回查询结果，而是以`垂直`的形式将每个列都展示在单独的一行中：

```
mysql> select now()\G
*************************** 1. row ***************************
now(): 2023-03-05 01:17:37
1 row in set (0.00 sec)
```

**命令可以随意换行。**

并不是按了回车键就提交命令了，只要按回车键的时候输入的语句里没有`;`、`\g`或者`\G`这些语句结束符号，该语句就算是没结束。比如上边查询当前时间的命令还可以这么写

```
mysql> select
    -> now()
    -> ;
+---------------------+
| now()               |
+---------------------+
| 2023-03-05 01:18:49 |
+---------------------+
1 row in set (0.00 sec)
```

**可以一次提交多个命令**

我们可以在一条语句里写多个命令(命令之间用上面说的结束符分隔)，比如这样：

```
mysql> select now();select now();select now();
+---------------------+
| now()               |
+---------------------+
| 2023-03-05 01:20:56 |
+---------------------+
1 row in set (0.00 sec)

+---------------------+
| now()               |
+---------------------+
| 2023-03-05 01:20:56 |
+---------------------+
1 row in set (0.00 sec)

+---------------------+
| now()               |
+---------------------+
| 2023-03-05 01:20:56 |
+---------------------+
1 row in set (0.00 sec)
```

连着输入了3个查询当前时间的命令，只要没按回车键，就不会提交命令

>  后边我们还会介绍把命令都写在文件里，然后再批量执行文件中的命令，那个感觉更爽！

**使用`\c`放弃本次操作。**

如果你想放弃本次编写的命令，可以在输入的命令后边加上`\c`，比如这样：

```
mysql> SELECT NOW()\c
```

大小写问题。

`MySQL`默认对命令的大小写并没有限制，也就是说我们这样查询当前时间也是可以的：

不过按照习俗，这些命令、函数什么的都是要大写的，而一些名称类的东西，比如数据库名，表名、列名啥的都是要小写的，更多具体的书写规范等我们遇着再详细介绍。



**字符串的表示。**

在命令里有时会使用到字符串，我们可以使用单引号`''`或者双引号`""`把字符串内容引起来，比如这样：

```
mysql> SELECT 'aaa';
+-----+
| aaa |
+-----+
| aaa |
+-----+
1 row in set (0.00 sec)
```

这个语句只是简单的把字符串`'aaa'`又输出来了而已。但是一定要在字符串内容上加上引号，不然的话`MySQL`服务器会把它当作列名，比如这样就会返回一个错误：

```
mysql> SELECT aaa;
ERROR 1054 (42S22): Unknown column 'aaa' in 'field list'
```

但是`MySQL`中有一种叫`ANSI_QUOTES`的模式，如果开启了这种模式，双引号就有其他特殊的用途了，至于是什么用途对于小白的你并不重要，你也不需要理解什么是个`ANSI_QUOTES`模式，重要的建议你最好使用单引号来表示字符串～

当一条命令从客户端发送给了MySQL服务器之后，服务器处理完后就会给客户端发送回来处理结果，然后显示到界面上。然后你就可以接着输入下一条命令了。

## MYSQL数据类型

### 数值类型

#### 整数类型

很显然，使用的字节数越多，意味着能表示的数值范围就越大，但是也就越耗费存储空间。根据表示一个数占用字节数的不同，`MySQL`把整数划分成如下所示的类型：

| 类型                     | 占用的存储空间（单位：字节） | 无符号数取值范围 | 有符号数取值范围 | 含义           |
| ------------------------ | ---------------------------- | ---------------- | ---------------- | -------------- |
| `TINYINT`                | 1                            | 0 ~ 2⁸-1         | -2⁷ ~ 2⁷-1       | 非常小的整数   |
| `SMALLINT`               | 2                            | 0 ~ 2¹⁶-1        | -2¹⁵ ~ 2¹⁵-1     | 小的整数       |
| `MEDIUMINT`              | 3                            | 0 ~ 2²⁴-1        | -2²³ ~ 2²³-1     | 中等大小的整数 |
| `INT`（别名：`INTEGER`） | 4                            | 0 ~ 2³²-1        | -2³¹ ~ 2³¹-1     | 标准的整数     |
| `BIGINT`                 | 8                            | 0 ~ 2⁶⁴-1        | -2⁶³ ~ 2⁶³-1     | 大整数         |

#### 浮点数类型

#### 用二进制表示十进制小数

浮点数是用来表示小数的，我们平时用的十进制小数也可以被转换成二进制后被计算机存储。比如`9.875`，这个小数可以被表示成这样：

```ini
9.875 = 8 + 1 + 0.5 + 0.25 + 0.125 = 1 × 2³ + 1 × 2⁰ + 1 × 2⁻¹ + 1 × 2⁻² + 1 × 2⁻³ 
```

也就是说，如果十进制小数`9.875`转换成二进制小数的话就是：`1001.111`。为了在计算机里存储这种二进制小数，我们统一把它们表示成`a × 2ⁿ`的科学计数法的形式，其中1≤`|a|`＜2，比如`1001.111`可以被表示成`1.001111 × 2³`，我们把小数点之后的`001111`称为`尾数`，把`2³`中的`3`称为`指数`，然后只需要在计算机中的比特位中表示出`尾数`和`指数`就行了。另外，小数也有正负之分，我们还需要单独的部分来表示小数的正负号。综上所述，表示一个浮点数需要下边几个部分：

- 符号部分，占用1个比特位即可。
- 指数部分，视具体浮点数格式而定。
- 尾数部分，视具体浮点数格式而定。

很显然，我们表示一个浮点数使用的字节数越多，表示`尾数`和`指数`的范围就越大，也就是说可以表示的小数范围就越大，设计`MySQL`的大叔根据表示一个小数需要的不同字节数定义了如下的两种浮点数类型：

| 类型     | 占用的存储空间（单位：字节） | 绝对值最小非0值          | 绝对值最大非0值          | 含义         |
| -------- | ---------------------------- | ------------------------ | ------------------------ | ------------ |
| `FLOAT`  | 4                            | ±1.175494351E-38         | ±3.402823466E+38         | 单精度浮点数 |
| `DOUBLE` | 8                            | ±2.2250738585072014E-308 | ±1.7976931348623157E+308 | 双精度浮点数 |



以单精度浮点数类型`FLOAT`类型为例，它占用的4个字节的各个组成部分如下图所示：

![image-20230305013953001](..\images\float_mysql.png)

另外需要注意的是，虽然有的十进制小数，比如`1.875`可以被很容易的转换成二进制数`1.111`，但是更多的小数是无法直接转换成二进制的，比如说`0.3`，它转换成的二进制小数就是一个无限小数，但是我们现在只能用4个字节或者8个字节来表示这个小数，所以只能进行一些舍入来近似的表示，所以我们说计算机的浮点数表示有时是不精确的。

#### 设置最大位数和小数位数

在定义浮点数类型时，还可以在`FLOAT`或者`DOUBLE`后边跟上两个参数，就像这样：

```
FLOAT(M, D)
DOUBLE(M, D)
```

对于我们用户而言，使用的都是十进制小数。如果我们事先知道表中的某个列要存储的小数在一定范围内，我们可以使用`FLOAT(M, D)`或者`DOUBLE(M, D)`来限制可以存储到本列中的小数范围。其中：

- `M`表示该小数最多需要的十进制有效数字个数。

  注意是`有效数字`个数，比方说对于小数`-2.3`来说有效数字个数就是2，对于小数`0.9`来说有效数字个数就是`1`。

- `D`表示该小数的小数点后的十进制数字个数。

  这个好理解，小数点后有几个十进制数字，`D`的值就是什么。

举个例子看一下，设置了`M`和`D`的单精度浮点数的取值范围的变化：

| 类型          | 取值范围         |
| ------------- | ---------------- |
| `FLOAT(4, 1)` | -999.9~999.9     |
| `FLOAT(5, 1)` | -9999.9~9999.9   |
| `FLOAT(6, 1)` | -99999.9~99999.9 |
| `FLOAT(4, 0)` | -9999~9999       |
| `FLOAT(4, 1)` | -999.9~999.9     |
| `FLOAT(4, 2)` | -99.99~99.99     |

可以看到，在D相同的情况下，M越大，该类型的取值范围越大；在M相同的情况下，D越大，该类型的取值范围越小。当然，`M`和`D`的取值也不是无限大的，`M`的取值范围是`1~255`，`D`的取值范围是`0~30`，而且`D`的值必须不大于`M`。`M`和`D`都是可选的，如果我们省略了它们，那它们的值按照机器支持的最大值来存储。

#### 定点数类型

正因为用浮点数表示小数可能会有不精确的情况，在一些情况下我们必须保证小数是精确的，所以设计`MySQL`的大叔们提出一种称之为`定点数`的数据类型，它也是存储小数的一种方式：

```
DECIMAL(M, D)
```

此处的`M`和`D`的含义与浮点数中的含义一样。`M`和`D`对取值范围的影响我们之前在唠叨浮点数的时候已经介绍过了，但是我们又说单精度浮点数类型`FLOAT(M, D)`占用的字节数一直都是4字节，双精度浮点数`DOUBLE(M, D)`占用的字节数一直都是8字节，它们占用的存储空间大小并不随着M和D的值的变动而变动，为啥到了这个所谓的定点数类型`DECIMAL(M, D)`中，它占用的存储空间大小就和`M`、`D`的取值有关了呢？哈哈，回答这个问题还得且听我细细道来。

我们说定点数是一种精确的小数，为了达到精确的目的我们就不能把它转换成二进制小数之后再存储(因为有很多十进制小数转为二进制小数后需要进行舍入操作，导致二进制小数表示的数值是不精确的)。其实转念一想，所谓的小数只是把两个十进制整数用小数点分割开来而已，我们只要把小数点左右的两个十进制整数给存储起来，那不就是精确的了么。比方说对于十进制小数`2.38`来说，我们可以把这个小数的小数点左右的两个整数，也就是`2`和`38`分别保存起来，那么不就相当于保存了一个精确的小数么，这波操作是不是很6。

当然事情并没有这么简单，对于给定`M`、`D`值的`DECIMAL(M, D)`类型，比如`DEMCIMAL(16, 4)`来说：

- 首先确定小数点左边的整数最多需要存储的十进制位数是12位，小数点右边的整数需要存储的十进制位数是4位，如图所示：

![image-20230306003120811](..\images\mysql_data_fixed_number.png)

- 从图中可以看出，如果不足9个十进制位，也会被划分成一组。

![image-20230306003213209](..\images\partinon_number.png)

- 针对每个组中的十进制数字，将其转换为二进制数字进行存储，根据组中包含的十进制数字位数不同，所需的存储空间大小也不同，具体见下表：

  | 组中包含的十进制位数 | 占用存储空间大小（单位：字节） |
  | -------------------- | ------------------------------ |
  | 1或2                 | 1                              |
  | 3或4                 | 2                              |
  | 5或6                 | 3                              |
  | 7或8或9              | 4                              |

  所以`DECIMAL(16, 4)`共需要占用`8`个字节的存储空间大小，这8个字节由下边3个部分组成：

  - 第1组包含3个十进制位，需要使用2个字节存储。
  - 第2组包含9个十进制位，需要使用4个字节存储。
  - 第3组包含4个十进制位，需要使用2个字节存储。

- 将转换完成的比特位序列的最高位设置为1。

这些步骤看的有一丢丢懵逼吧，别着急，举个例子就都清楚了。比方说我们使用定点数类型`DECIMAL(16, 4)`来存储十进制小数`1234567890.1234`，这个小数会被划分成3个部分：

```yaml
1 234567890 1234
```

也就是：

- 第1组中包含整数`1`。
- 第2组中包含整数`234567890`。
- 第3组中包含整数`1234`。

然后将每一组中的十进制数字转换成对应的二进制数字：

- 第1组占用2个字节，整数`1`对应的二进制数就是（字节之间实际上没有空格，只不过为了大家理解上的方便我们加了一个空格）：

  ```
  00000000 00000001
  ```

  二进制看起来太难受，我们还是转换成对应的十六进制看一下：

  ```
  0x0001
  ```

- 第2组占用4个字节，整数`234567890`对应的十六进制数就是：

  ```
  0x0DFB38D2
  ```

- 第3组占用2个字节，整数`1234`对应的十六进制数就是：

  ```
  0x04D2
  ```

所以将这些十六进制数字连起来之后就是：

```
0x00010DFB38D204D2
```

最后还要将这个结果的最高位设置为1，所以最终十进制小数`1234567890.1234`使用定点数类型`DECIMAL(16, 4)`存储时共占用8个字节，具体内容为：

```
0x80010DFB38D204D2
```

有的同学会问，如果我们想使用定点数类型`DECIMAL(16, 4)`存储一个负数怎么办，比方说`-1234567890.1234`，这时只需要将`0x80010DFB38D204D2`中的每一个比特位都执行一个取反操作就好，也就是得到下边这个结果：

```
0x7FFEF204C72DFB2D
```

从上边的叙述中我们可以知道，对于`DECIMAL(M, D)`类型来说，给定的`M`和`D`的值不同，所需的存储空间大小也不同。可以看到，与浮点数相比，定点数需要更多的空间来存储数据，所以如果不是在某些需要存储精确小数的场景下，一般的小数用浮点数表示就足够了。

对于定点数类型`DECIMAL(M, D)`来说，`M`和`D`都是可选的，默认的`M`的值是10，默认的`D`的值是0，也就是说下列等式是成立的：

```scss
DECIMAL = DECIMAL(10) = DECIMAL(10, 0)
DECIMAL(n) = DECIMAL(n, 0)
```

另外`M`的范围是`1~65`，`D`的范围是`0~30`，且`D`的值不能超过`M`

#### 无符号数值类型的表示

对于数值类型，包括整数、浮点数和定点数，有些情况下我们只需要用到无符号数（就是非负数）。`MySQL`给我们提供了一个表示无符号数值类型的方式，就是在原数值类型后加一个单词`UNSIGNED`：

#### 日期和时间类型

我们有很多场景需要表示时间或日期，比如学生基本信息中的`入学时间`就需要用日期的格式保存。`MySQL`为我们提供了多种关于时间和日期的类型，各种类型能表示的范围如下：

| 类型        | 存储空间要求 | 取值范围                                       | 含义         |
| ----------- | ------------ | ---------------------------------------------- | ------------ |
| `YEAR`      | 1字节        | 1901~2155                                      | 年份值       |
| `DATE`      | 3字节        | '1000-01-01' ~ '9999-12-31'                    | 日期值       |
| `TIME`      | 3字节        | '-838:59:59' ~ '838:59:59'                     | 时间值       |
| `DATETIME`  | 8字节        | '1000-01-01 00:00:00' ～ '9999-12-31 23:59:59' | 日期加时间值 |
| `TIMESTAMP` | 4字节        | '1970-01-01 00:00:01' ～ '2038-01-19 03:14:07' | 时间戳       |



在`MySQL5.6.4`这个版本之后，`TIME`、`DATETIME`、`TIMESTAMP`这几种类型添加了对毫秒、微秒的支持。由于毫秒、微秒都不到1秒，所以也被称为`小数秒`，`MySQL`最多支持6位小数秒的精度，各个位代表的意思如下：

如果我们想让`TIME`、`DATETIME`、`TIMESTAMP`这几种类型支持小数秒，可以这样写：

```scss
类型(小数秒位数)

其中的小数秒位数可以在0、1、2、3、4、5、6中选择
```

比如`DATETIME(0)`表示精确到秒，`DATETIME(3)`表示精确到毫秒，`DATETIME(5)`表示精确到10微秒。如果你在选择`TIME`、`DATETIME`、`TIMESTAMP`这几种类型的时候添加了对小数秒的支持，那么所需的存储空间需要相应的扩大，保留不同的小数秒位数，那么增加的存储空间大小也不同，如下表：

| 保留的小数秒位数 | 额外需要的存储空间要 |
| ---------------- | -------------------- |
| 0                | 0字节                |
| 1或2             | 1字节                |
| 3或4             | 2字节                |
| 5或6             | 3字节                |



也就是说如果你选择使用`DATETIME(1)`，那么需要的存储空间就是在`DATETIME`的空间上再加上小数秒需要的空间，就是`8 + 1 = 9`个字节，类似的，`DATETIME(3)`就需要`8 + 2 = 10`个字节。所以，`MySQL5.6.4`这个版本之后的各个类型需要的存储空间和取值范围就如下：

| 类型        | 存储空间要求           | 取值范围                                                     | 含义         |
| ----------- | ---------------------- | ------------------------------------------------------------ | ------------ |
| `YEAR`      | 1字节                  | 1901~2155                                                    | 年份值       |
| `DATE`      | 3字节                  | '1000-01-01' ~ '9999-12-31'                                  | 日期值       |
| `TIME`      | 3字节+小数秒的存储空间 | '-838:59:59[.000000]' ~ '838:59:59[.000000]'                 | 时间值       |
| `DATETIME`  | 5字节+小数秒的存储空间 | '1000-01-01 00:00:00[.000000]' ～ '9999-12-31 23:59:59'[.999999] | 日期加时间值 |
| `TIMESTAMP` | 4字节+小数秒的存储空间 | '1970-01-01 00:00:01[.000000]' ～ '2038-01-19 03:14:07'[.999999] | 时间戳       |



大家应该发现其中的在没有存储小数秒的情况下，`DATETIME`类型占用的存储空间从原来的8字节变成了5字节，这是因为设计`MySQL`的大叔背后做了些努力，使存储格式变得更紧凑了些

##### YEAR

`YEAR`类型也可以写成`YEAR(4)`，它单纯表示一个年份值，取值范围为`1901 ～ 2155`，仅仅占用1个字节大小而已。因为可以存储的年份值有限，如果我们想存储更大范围的年份值，可以不使用`MySQL`自带的`YEAR`类型，换成`SMALLINT`（2字节）或者字符串类型啥的都可以。

```!
小贴士：

曾经也有YEAR(2)这种使用2个数字来表示年份的类型，比方说数字99表示1999年。不过在MySQL 5.7.5之后就不再支持这种类型了，我们稍微了解一下就好了。
```

##### DATE、TIME和DATETIME

顾名思义，`DATE`表示日期，格式是`YYYY-MM-DD`；`TIME`表示时间，格式是`hh:mm:ss[.uuuuuu]`或者`hhh:mm:ss[.uuuuuu]`（有时候要存储的小时值是三位数），`DATETIME`表示日期+时间，格式是`YYYY-MM-DD hh:mm:ss[.uuuuuu]`。其中的`YYYY`、`MM`、`DD`、`hh`、`mm`、`ss`、`uuuuuu`分别表示年、月、日、时、分、秒、小数秒。

需要注意的是，**DATETIME**中的时间部分表示的是一天内的时间(00:00:00 ~ 23:59:59)，而 **TIME**表示的是一段时间，而且可以表示负值。

##### TIMESTAMP

`1970-01-01 00:00:00`注定是一个特殊的时刻，我们把某个时刻距离`1970-01-01 00:00:00`的秒数称为`时间戳`。比方说当前时间是`2018-01-24 11:39:21`，距离`1970-01-01 00:00:00`的秒数为`1516765161`，那么`2018-01-24 11:39:21`这个时刻的时间戳就是`1516765161`。不过在`MySQL5.6.4`之后，时间戳的值也可以加入小数秒。

用时间戳存储时间的好处就是，它展示的值可以随着时区的变化而变化。比方说我们把`2018-01-24 11:39:21`这个时刻存储到一个`TIMESTAMP`的列中，那么在中国你看到的时间就是`2018-01-24 11:39:21`，如果你去了日本，他们哪里的使用的是东京时间，比北京时间早一个小时，所以他们那显示的就是`2018-01-24 12:39:21`。而如果你用`DATETIME`存储`2018-01-24 11:39:21`的话，那不同时区看到的时间值都是一样的。

### 字符串类型

#### 字符和字符串

`字符`可以大致分为两种，一种叫`可见字符`，一种叫`不可见字符`。顾名思义，`可见字符`就是打印出来后能看见的字符。比如`'a'`，`'b'`，`'我'`，`'。'` ... 这样的人眼能看见的单个的国家文字、标点符号、图形符号、数字等这样的东东，我们就叫做一个`可见字符`。`不可见字符`也好理解，就是打印机或者在黑框框里打印字符的时候有时候需要换行，打个制表符啥的，或者在输出某个字符的时候就发出`嘟`地一声，这种我们看不到，只是为了控制输出效果的字符叫做`不可见字符`。`字符串`就是把字符连起来的样子，比如`'abc'`，就是由`'a'`、`'b'`、`'c'`三个字符连起来的一个`字符串`，下边列举了4个字符串的例子：

```arduino
'我喜欢你'
'me, too'
'give me a hug'
'么么哒'
```

#### 字符编码简介

在具体分析`MySQL`中各个字符串类型之前，我们一定要先搞明白字符和字节的区别。字符是面向人的概念，字节是面向计算机的概念。如果你想在计算机中表示字符，那就需要将该字符与一个特定的字节序列对应起来，这个映射过程称之为`编码`。不幸的是，这种映射关系并不是唯一的，不同的人制作了不同的编码方案，根据表示一个字符使用的字节数量是不是固定的，编码方案可以分为下边两种：

- 固定长度的编码方案

  表示不同的字符所需要的字节数量是相同的。比方说`ASCII`编码方案采用1个字节来编码一个字符，`ucs2`采用2个字节来编码一个字符。

- 变长的编码方案

  表示不同的字符所需要的字节数量是不同的。比方说`utf8`编码方案采用`1~3`个字节来编码一个字符，`gb2312`采用`1~2`个字节来编码一个字符。

对于不同的字符编码方案来说，同一个字符可能被编码成不同的字节序列。比如同样一个字符：`我`，在`utf8`和`gb2312`这两种编码方案下被映射成如下的字节序列：

- utf8编码方案

  字符`'我'`被编码成：

  ```
  111001101000100010010001
  ```

  共占用3个字节，用十六进制表示就是：`0xE68891`。

- gb2312编码方案

  字符`'我'`被编码成：

  ```
  1100111011010010
  ```

  共占用2个字节，用十六进制表示就是：`0xCED2`。

```!
小贴士：

注：十六进制前边的0x是前缀，表示后边的是16进制数据。
```

另外，设计MySQL的大叔似乎对`编码方案`和`字符集`这两个概念并没做什么区分，也就是说我们之后所讲的`utf8`字符集指的就是`utf8`编码方案，`gb2312`字符集指的也就是`gb2312`编码方案。

```!
小贴士：

正宗的utf8字符集是使用1~4个字节来编码一个字符的，不过MySQL中对utf8字符集做了阉割，编码一个字符最多使用3个字节。如果我们之后有存储使用4个字节来编码的字符的情景，可以使用一种称之为utf8mb4的字符集，它才是正宗的utf8字符集。
```

现在我们可以看一下`MySQL`中提供的各种字符串类型（注：其中`M`代表该数据类型最多能存储的字符数量，`L`代表我们实际向该类型的属性中存储的字符串在特定字符集下所占的字节数，`W`代表在该特定字符集下，编码一个字符最多需要的字节数）：

| 类型         | 最大长度     | 存储空间要求      | 含义             |
| ------------ | ------------ | ----------------- | ---------------- |
| `CHAR(M)`    | M个字符      | M×W个字节         | 固定长度的字符串 |
| `VARCHAR(M)` | M个字符      | L+1 或 L+2 个字节 | 可变长度的字符串 |
| `TINYTEXT`   | 2⁸-1 个字节  | L+1个字节         | 非常小型的字符串 |
| `TEXT`       | 2¹⁶-1 个字节 | L+2 个字节        | 小型的字符串     |
| `MEDIUMTEXT` | 2²⁴-1 个字节 | L+3个字节         | 中等大小的字符串 |
| `LONGTEXT`   | 2³²-1 个字节 | L+4个字节         | 大型的字符串     |



当然，就画这么个表格大家一准儿有些懵，我们接下来看一下各种字符串类型的细节。

##### CHAR(M)

`CHAR(M)`中的`M`代表该类型最多可以存储的字符数量，注意，是字符数量，不是字节数量。其中`M`的取值范围是`0~255`。如果省略掉`M`的值，那它的默认值就是1，也就是说`CHAR`和`CHAR(1)`是一个意思。`CHAR(0)`是一种特别的类型，它只能存储空字符串`''`或者`NULL`值（我们后边会详细介绍啥是个`NULL`）。再回头看一眼我们的学生基本信息表，如果你觉得学生的姓名不会超过5个字符，你就可以指定这个姓名列的类型为`CHAR(5)`。

`CHAR(M)`在不同的字符集下需要的存储空间也是不一样的，我们假设某个字符集编码一个字符最多需要`W`个字节，那么类型`CHAR(M)`占用的存储空间大小就是`M×W`个字节。比方说：

- 对于采用`ascii`字符集的`CHAR(5)`类型来说，`ascii`字符集编码一个字符最多需要1个字节，也就是`M=5`、`W=1`，所以这种情况下该类型占用的存储空间大小就是`5×1 = 5`个字节。
- 对于采用`gbk`字符集的`CHAR(5)`类型来说，`gbk`字符集编码一个字符最多需要2个字节，也就是`M=5`、`W=2`，所以这种情况下该类型占用的存储空间大小就是`5×2 = 10`个字节。
- 对于采用`utf8`字符集的`CHAR(5)`类型来说，`utf8`字符集编码一个字符最多需要3个字节，也就是`M=5`、`W=3`，所以这种情况下该类型占用的存储空间大小就是`5×3 = 15`个字节。

如果我们实际存储的字符串在特定字符集编码下占用的字节数不足`M×W`，那么剩余的那些存储空间用空格字符（也就是：`' '`）补齐。比方说表的某个属性的类型是采用`ascii`字符集的`CHAR(5)`类型，我们想将字符串`'abc'`存入使用这个类型的表属性中，其中字符串`'abc'`在`ascii`字符集下需要3个字节存储，而采用`ascii`字符集的`CHAR(5)`类型又需要5个字节的存储空间，那么剩下的那两个字节的存储空间就会存储空格字符`' '`的编码。这也就是说：一旦你确定了CHAR(M)类型的M的值，如果M的值很大，而你实际存储的字符串占用字节数又很少，会造成存储空间的浪费。

```!
小贴士：

字符'a'在ascii字符集下被编码为0x61，字符'b'在ascii字符集下被编码为0x62，字符'c'在ascii字符集下被编码为0x63，空格字符被编码为0x20，所以将字符串'abc'存入采用ascii字符集的CHAR(5)类型的表属性中时，实际存储的字节序列就是：0x6162632020
```

##### VARCHAR(M)

如果你表中的某个列需要存储字符串类型的数据，而且这些字符串长短不一，那么使用`CHAR(M)`可能会浪费很多存储空间，`VARCHAR(M)`正是为了解决这个问题而生的。

`VARCHAR(M)`中的`M`也是代表该类型最多可以存储的字符数量，理论上的取值范围是`1~65535`。但是`MySQL`中还有一个规定，表中某一行包含的所有列中存储的数据大小总共不得超过65535个字节（注意是字节），也就是说`VARCHAR(M)`类型实际能够容纳的字符数量是小于65535的。

`VARCHAR(M)`类型占用的存储空间不确定，那系统在读一个`VARCHAR(M)`类型的数据时怎么知道该数据占用多少个字节呢？答案是：不知道。所以一个`VARCHAR(M)`类型表示的数据其实是由这么两部分组成：

1. 真正的字符串内容。

   假设真正的字符串在特定字符集编码后占用的字节数为`L`。

2. 占用字节数。

   假设`VARCHAR(M)`类型采用的字符集编码一个字符最多需要`W`个字节，那么：

   - 当`M×W < 256`时，只需要一个字节来表示占用的字节数。
   - 当`M×W >= 256`且`M×W < 65536`时，需要两个字节来表示占用的字节数。

   ```!
   小贴士：
   
   一个字节占用8个比特位，能表示的最大无符号数就是255，两个字节占用16个比特位，能表示的最大无符号数就是65535。
   ```

我们还用学生的姓名属性做例子，假设我们给姓名列定义的类型为采用`utf8`字符集的`VARCHAR(5)`，也就是说`M = 5`、`W = 3`，所以`M × W= 5×3 = 15`，而`15 < 256`，所以我们只需要一个字节来表示真实数据占用的字节长度就好了。对于`'杜子腾'`和`'范统'`这两个字符串来说，它们在`utf8`字符集下可以被编码成如下的样子(二进制太长了，用16进制表示)：

```arduino
'杜子腾'：0xE69D9CE5AD90E885BE （共9个字节）
'范统'：0xE88C83E7BB9F （共6个字节）
```

从上边的示例中可以看出，`VARCHAR(M)`类型占用的存储空间大小随着实际存储的内容变化而变化，假设实际存储的内容占用的字节长度为`L`，那么整个`VARCHAR(M)`类型占用的存储空间大小就是`L+1`或者`L+2`个字节。所以我们说**VARCHAR(M)**是一种可变长度的字符串类型。

##### 各种TEXT类型

虽然`VARCHAR(M)`已经可以存储很长的字符串了，可是有时候还是不够咋办？对于很长的字符串，设计`MySQL`的大叔们给我们提供了`TINYTEXT`、`TEXT`、`MEDIUMTEXT`、`LONGTEXT`四种可以存储大型的字符串的类型。它们也都是变长类型，也就是说这些类型占用的存储空间由实际内容和内容占用的字节长度两部分构成。

- 因为`TINYTEXT`最多可以存储`2⁸-1`个字节，所以内容占用的字节长度用1个字节就可以表示
- `TEXT`最多可以存储`2¹⁶-1`个字节，所以内容占用的字节长度用2个字节就可以表示。
- `MEDIUMTEXT`最多可以存储`2²⁴-1`个字节，所以内容占用的字节长度用3个字节就可以表示。
- `LONGTEXT`最多可以存储`2³²-1`个字节，所以内容占用的字节长度用4个字节就可以表示。

不过之前不是有个规定说某一行包含的所有列中存储的数据大小总和不得超过65535个字节么？这个规定对这些TEXT类型是不起作用的，它们并不在这个规定的限制范围之内。一个表中如果有的属性需要存储特别长的文本的话，就可以考虑使用这几个类型了。

#### ENUM类型和SET类型

视角回到我们的学生信息表，性别一列也需要填写字符串，但是比较特殊的一点是，这一列只能填`男`或者`女`，填别的字符串就尴尬了！针对这种情况，我们提出了一个叫`ENUM`的类型，也称为`枚举类型`，它的格式如下：

```arduino
ENUM('str1', 'str2', 'str3' ⋯)
```

它表示在给定的字符串列表里选择一个。比如我们的性别一列可以定义成`ENUM('男', '女')`类型。这个的意思就是性别一列只能在`'男'`或者`'女'`这两个字符串之间选择一个，相当于一个单选框～

有的时候某一列的值可以在给定的字符串列表中挑选多个，假设学生的基本信息加了一列`兴趣`属性，这个属性的值可以从给定的兴趣列表中挑选多个，那我们可以使用`SET`类型，它的格式如下：

```sql
SET('str1', 'str2', 'str3' ⋯)
```

它表示可以在给定的字符串列表里选择多个。我们的兴趣一列就可以定义成`SET('打球', '画画', '扯犊子', '玩游戏')`类型。这个的意思就是兴趣一列可以在给定的这几个字符串中选择一个或多个，相当于一个多选框～效果就像这样：

| 学号     | 姓名   | ···  | 兴趣                       |
| -------- | ------ | ---- | -------------------------- |
| 20180101 | 杜子腾 | ···  | '打球', '画画'             |
| 20180102 | 杜琦燕 | ···  | '扯犊子'                   |
| 20180103 | 范统   | ···  | '扯犊子', '玩游戏'         |
| 20180104 | 史珍香 | ···  | '画画', '扯犊子', '玩游戏' |



综上所述，ENUM和SET类型都是一种特殊的字符串类型，在从字符串列表中单选或多选元素的时候会用得到它们。

### 二进制类型

#### BIT类型

有时候我们有存储单个或者多个比特位的需求，此时就可以用到下边这种类型：

`BIT(M)`

其中`M`的取值范围为`1~64`，而且`M`可以省略，它的默认值为1，也就是说`BIT(1)`和`BIT`的意思是一样的。

`MySQL`是以字节为单位存储数据的，一个字节拥有8个比特位。如果我们想存储的比特位个数不足整数个字节，那么`MySQL`会偷偷的填充满，比方说：

- `BIT(1)`类型仅仅需要存储1个比特位的数据，但是`MySQL`会为其申请`(1+7)/8 = 1`个字节。
- `BIT(5)`类型仅仅需要存储5个比特位的数据，但是`MySQL`会为其申请`(5+7)/8 = 1`个字节。
- `BIT(9)`类型仅仅需要存储9个比特位的数据，但是`MySQL`会为其申请`(9+7)/8 = 2`个字节

#### BINARY(M)与VARBINARY(M)

`BINARY(M)`和`VARBINARY(M)`对应于我们前边提到的`CHAR(M)`和`VARCHAR(M)`，都是前者是固定长度的类型，后者是可变长度的类型，只不过`BINARY(M)`和`VARBINARY(M)`是用来存放字节的，其中的`M`代表该类型最多能存放的字节数量，而`CHAR(M)`和`VARCHAR(M)`是用来存储字符的，其中的`M`代表该类型最多能存放的字符数量。

#### 其他的二进制类型

`TINYBLOB`、`BLOB`、`MEDIUMBLOB`、`LONGBLOB`是针对数据量很大的二进制数据提出的，比如图片、音频、压缩文件啥的。它们很像`TINYTEXT`、`TEXT`、`MEDIUMTEXT`、`LONGTEXT`，不过各种`BLOB`类型是用来存储字节的，而各种`TEXT`类型是用来存储字符的而已。

```!
小贴士：

对于比较大的二进制数据，比方说图片、音频、压缩文件什么的，通常情况下都不直接存储到数据库管理系统中，而是将它们保存到文件系统中，然后在数据库中之存放一个文件路径即可。
```

## 数据库基本操作

`MySQL`中把一些表的集合称为一个`数据库`，`MySQL`服务器管理着若干个数据库，每个数据库下都可以有若干个表，画个图就是这样：

![image-20230305014351452](..\images\mysql_scope.png)

### 展示数据库

在我们刚刚安装好`MySQL`的时候，它已经内建了许多数据库和表了，我们可以使用下边这个命令来看一下都有哪些数据库：

```
SHOW DATABASES;

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

可以看到，这一版本的`MySQL`已经为我们内建了4个数据库，这些数据库都是给`MySQL`自己内部使用的，如果我们想使用`MySQL`存放自己的数据，首先需要创建一个属于自己的数据库。

### 创建数据库

创建数据库的语法贼简单：

```ini
CREATE DATABASE 数据库名;
```

```
mysql> CREATE DATABASE xiaohaizi;
Query OK, 1 row affected (0.01 sec)
```

### IF NOT EXISTS

我们在一个数据库已经存在的情况下再使用`CREATE DATABASE`去创建这个数据库会产生错误：

```arduino
mysql> CREATE DATABASE xiaohaizi;
ERROR 1007 (HY000): Can't create database 'xiaohaizi'; database exists
```

执行结果提示了一个`ERROR`，意思是数据库`xiaohaizi`已经存在！所以如果我们并不清楚数据库是否存在，可以使用下边的语句来创建数据库：

```sql
CREATE DATABASE IF NOT EXISTS 数据库名;
```

这个命令的意思是如果指定的数据库不存在的话就创建它，否则什么都不做。我们试一试：

```sql
mysql> CREATE DATABASE IF NOT EXISTS xiaohaizi;
Query OK, 1 row affected, 1 warning (0.00 sec)
```

可以看到语句执行成功了，提示的`ERROR`也没有了，只是结果中有1个`warning`，这个`warning`只是`MySQL`善意的提醒我们数据库`xiaohaizi`已存在而已。

### 切换当前数据库

对于每一个连接到`MySQL`服务器的客户端，都有一个`当前数据库`的概念（也可以称之为`默认数据库`），我们创建的表默认都会被放到当前数据库中，切换当前数据库的命令也贼简单

```
USE 数据库名称*;*
```

```
mysql> USE xiaohaizi;
Database changed
```

看到显示了`Database changed`说明当前数据库已经切换成功了。需要注意的是，在退出当前客户端之后，也就是你输入了`exit`或者`quit`命令之后或者直接把当前的黑框框页面关掉，当你再次调用`mysql -h 主机名 -u 用户名 -p密码`的时候，相当于重新开启了一个客户端，需要重新调用`USE 数据库名称`的语句来选择一下当前数据库。

```
mysql -h localhost -u root -p123456 xiaohaizi
```

### 删除数据库

如果我们觉得某个数据库没用了，还可以把它删掉，语法如下：

```
DROP DATABASE 数据库名;
```

### IF EXISTS

如果某个数据库并不存在，我们仍旧调用`DROP DATABASE`语句去删除它，不过会报错的：

```rust
mysql> DROP DATABASE xiaohaizi;
```

如果想避免这种报错，可以使用这种形式的语句来删除数据库：

```sql
DROP DATABASE IF EXISTS 数据库名;
```

### 查看当前处于哪个数据库

select database() ;

```
mysql> select database();
+------------+
| database() |
+------------+
| mysql      |
+------------+
1 row in set (0.00 sec)

```

show tables ;

```
mysql> show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |

查询出来的结果中，第一行为Tables_in_mysql ， 这里的mysql 就是当前正在使用的数据库
```

用status语句

```
mysql> status
--------------
mysql  Ver 8.0.32-0ubuntu0.20.04.2 for Linux on x86_64 ((Ubuntu))

Connection id:          111
Current database:       mysql
```

## 表的基本操作

数据库建好之后，我们就可以接着创建真正存储数据的表了。创建表的时候首先需要描述清楚这个表长什么样，它有哪些列，这些列都是用来存什么类型的数据等等，这个对表的描述称为表的`结构`或者`定义`。有了表的结构之后，我们就可以着手把数据塞到这个表里了。表中的一行叫做一条`记录`，一列叫做一个`字段`。

### 创建表

#### 基本语法

创建一个表时至少需要完成下列事情：

1. 给表起个名。
2. 给表定义一些列，并且给这些列都起个名。
3. 每一个列都需要定义一种数据类型。
4. 如果有需要的话，可以给这些列定义一些列的属性，比如不许存储`NULL`，设置默认值等等，具体列可以设置哪些属性我们稍后详细唠叨。

`MySQL`中创建表的基本语法就是这样的：

```css
CREATE TABLE 表名 (
    列名1    数据类型    [列的属性],
    列名2    数据类型    [列的属性],
    ...
    列名n    数据类型    [列的属性]
);
```

也就是说：

- 在`CREATE TABLE`后写清楚我们要创建的表的名称。
- 然后在小括号`()`中定义上这个表的各个列的信息，包括列的名称、列的数据类型，如果有需要的话也可以定义这个列的属性（列的属性用中括号`[]`引起来的意思就是这部分是可选的，也就是可有可无的）。
- 列名、数据类型、列的属性之间用空白字符分开就好，然后各个列的信息之间用逗号`,`分隔开。

```!
小贴士：

我们也可以把这个创建表的语句都放在单行中，而示例中将建表语句分成多行并且加上缩进仅仅是为了美观而已～
```

废话不多说，赶紧定义一个超级简单的表瞅瞅：

```sql
CREATE TABLE first_table (
    first_column INT,
    second_column VARCHAR(100)
);
```

#### 为建表语句添加注释

我们可以在创建表时将该表的用处以注释的形式添加到语句中，只要在建表语句最后加上`COMMENT`语句就好，如下：

```sql
CREATE TABLE 表名 (
    各个列的信息 ...
) COMMENT '表的注释信息';
```

比如我们可以这样写`first_table`表的建表语句：

```sql
CREATE TABLE first_table (
    first_column INT,
    second_column VARCHAR(100)
) COMMENT '第一个表';
```

注释没必要太长，言简意赅即可，毕竟是给人看的，让人看明白是个啥意思就好了。为了我们自己的方便，也为了阅读你创建的人的方便，请遵守一下职业道德，写个注释吧～

#### 创建现实生活中的表

有了创建`first_table`的经验，我们就可以着手用`MySQL`把之前提到的学生基本信息表和成绩表给创建出来了，先把学生基本信息表搬下来看看：

**学生基本信息表** 

| 学号     | 姓名   | 性别 | 身份证号           | 学院       | 专业             | 入学时间 |
| -------- | ------ | ---- | ------------------ | ---------- | ---------------- | -------- |
| 20180101 | 杜子腾 | 男   | 158177199901044792 | 计算机学院 | 计算机科学与工程 | 2018/9/1 |
| 20180102 | 杜琦燕 | 女   | 151008199801178529 | 计算机学院 | 计算机科学与工程 | 2018/9/1 |
| 20180103 | 范统   | 男   | 17156319980116959X | 计算机学院 | 软件工程         | 2018/9/1 |
| 20180104 | 史珍香 | 女   | 141992199701078600 | 计算机学院 | 软件工程         | 2018/9/1 |



很显然，这个表有`学号`、`姓名`、`性别`、`身份证号`、`学院`、`专业`、`入学时间`这几个列，其中的`学号`是整数类型的，`入学时间`是日期类型的，由于身份证号是固定的18位，我们可以把`身份证号`这一列定义成固定长度的字符串类型，`性别`一列只能填`男`或`女`，所以我们这里把它定义为`ENUM`类型的，其余各个列都是变长的字符串类型。看一下创建学生基本信息表的语句：

```sql
CREATE TABLE student_info (
    number INT,
    name VARCHAR(5),
    sex ENUM('男', '女'),
    id_number CHAR(18),
    department VARCHAR(30),
    major VARCHAR(30),
    enrollment_time DATE
) COMMENT '学生基本信息表';
```

然后再看一下学生成绩表：

然后再看一下学生成绩表：

**学生成绩表** 

| 学号     | 科目               | 成绩 |
| -------- | ------------------ | ---- |
| 20180101 | 母猪的产后护理     | 78   |
| 20180101 | 论萨达姆的战争准备 | 88   |
| 20180102 | 母猪的产后护理     | 100  |
| 20180102 | 论萨达姆的战争准备 | 98   |
| 20180103 | 母猪的产后护理     | 59   |
| 20180103 | 论萨达姆的战争准备 | 61   |
| 20180104 | 母猪的产后护理     | 55   |
| 20180104 | 论萨达姆的战争准备 | 46   |

这个表有`学号`、`科目`、`成绩`这几个列，`学号`和`成绩`是整数类型的，科目是字符串类型的，所以我们可以这样写建表语句：

```sql
CREATE TABLE student_score (
    number INT,
    subject VARCHAR(30),
    score TINYINT
) COMMENT '学生成绩表';
```

#### IF NOT EXISTS

和重复创建数据库一样，如果创建一个已经存在的表的话是会报错的，我们来试试重复创建一下`first_table`表：

```sql
mysql> CREATE TABLE first_table (
    ->     first_column INT,
    ->     second_column VARCHAR(100)
    -> ) COMMENT '第一个表';
ERROR 1050 (42S01): Table 'first_table' already exists
mysql>
```

执行结果提示了一个`ERROR`，意思是`first_table`已经存在！所以如果想要避免这种错误发生，可以在创建表的时候使用这种形式：

```sql
CREATE TABLE IF NOT EXISTS 表名(
    各个列的信息 ...
);
```

加入了`IF NOT EXISTS`的语句表示如果指定的表名不存在则创建这个表，如果存在那就什么都不做。我们使用这种`IF NOT EXISTS`的语法再来执行一遍创建`first_table`表的语句：

```sql
mysql> CREATE TABLE IF NOT EXISTS first_table (
    ->     first_column INT,
    ->     second_column VARCHAR(100)
    -> ) COMMENT '第一个表';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql>
```

可以看到语句执行成功了，只是结果中有1个`warning`而已

### 删除表

如果我们觉得某个表以后都用不到了，就可以把它删除掉。在真实工作环境中删除表一定要慎重谨慎，失去了的就再也回不来了～ 看一下删除的语法：

```sql
DROP TABLE 表1, 表2, ..., 表n;
```

也就是说我们可以同时删除多个表。我们现在把`first_table`表给删掉看看：

```sql
mysql> DROP TABLE first_table;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW TABLES;
+---------------------+
| Tables_in_xiaohaizi |
+---------------------+
| student_info        |
| student_score       |
+---------------------+
2 rows in set (0.00 sec)

mysql>
```

可以看到现在数据库`xiaohaizi`中没有了`first_table`表，说明删除成功了！

#### IF EXISTS

如果我们尝试删除某个不存在的表的话会报错：

```sql
mysql> DROP TABLE first_table;
ERROR 1051 (42S02): Unknown table 'xiaohaizi.first_table'
mysql>
```

执行结果提示了一个`ERROR`，提示我们要删除的表并不存在，如果想避免报错，可以使用这种删除语法：

```sql
DROP TABLE IF EXISTS 表名;
```

然后再删除一下不存在的`first_table`表：

```sql
mysql> DROP TABLE IF EXISTS first_table;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql>
```

这样就不报错了～

### 查看表结构

有时候我们可能忘记了自己定义的表的结构，可以使用下边这些语句来查看，它们起到的效果都是一样的：

```sql
DESCRIBE 表名;
DESC 表名;
EXPLAIN 表名;
SHOW COLUMNS FROM 表名;
SHOW FIELDS FROM 表名;
```

比如我们看一下`student_info`这个表的结构：

```sql
mysql> DESC student_info;
+-----------------+-------------------+------+-----+---------+-------+
| Field           | Type              | Null | Key | Default | Extra |
+-----------------+-------------------+------+-----+---------+-------+
| number          | int(11)           | YES  |     | NULL    |       |
| name            | varchar(5)        | YES  |     | NULL    |       |
| sex             | enum('男','女')   | YES  |     | NULL    |       |
| id_number       | char(18)          | YES  |     | NULL    |       |
| department      | varchar(30)       | YES  |     | NULL    |       |
| major           | varchar(30)       | YES  |     | NULL    |       |
| enrollment_time | date              | YES  |     | NULL    |       |
+-----------------+-------------------+------+-----+---------+-------+
7 rows in set (0.00 sec)

mysql>
```

可以看到，这个`student_info`表的各个列的名称、类型和属性就都展示出来了。当然我们现在还未学习过列的属性（我们下一章才会唠叨），所以我们现在只需要看结果中的`Field`和`Type`列就好了。

如果你看不惯这种以表格的形式展示各个列信息的方式，我们还可以使用下边这个语句来查看表结构：

```sql
SHOW CREATE TABLE 表名;
```

比如：

```sql
mysql> show create table student_info\G
*************************** 1. row ***************************
       Table: student_info
Create Table: CREATE TABLE `student_info` (
  `number` int DEFAULT NULL,
  `name` varchar(5) DEFAULT NULL,
  `sex` enum('男','女') DEFAULT NULL,
  `id_number` char(18) DEFAULT NULL,
  `department` varchar(30) DEFAULT NULL,
  `major` varchar(30) DEFAULT NULL,
  `enrollment_time` date DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='学生基本信息表'
1 row in set (0.00 sec)
```

### 没有选择当前数据库时对表的操作

有时候我们并没有使用`USE`语句来选择当前数据库，或者在一条语句中遇到的表分散在不同的数据库中，如果我们想在语句中使用这些表，那么就必须显式的指定这些表所属的数据库了。比如不管当前数据库是不是`xiaohaizi`，我们都可以调用这个语句来展示数据库`xiaohaizi`里边的表：

```sql
mysql> SHOW TABLES FROM xiaohaizi;
+---------------------+
| Tables_in_xiaohaizi |
+---------------------+
| first_table         |
| student_info        |
| student_score       |
+---------------------+
3 rows in set (0.00 sec)

mysql>
```

其他地方如果使用到表名的话，需要显式指定这个表所属的数据库，指明方式是这样的：

```
数据库名.表名
```

比方说我们想查看`xiaohaizi`数据库下`first_table`表的结构，但是又没有使用`USE xiaohaizi`语句指定当前数据库，此时可以这样写语句：

```sql
SHOW CREATE TABLE xiaohaizi.first_table\G
```

### 修改表

在表创建好之后如果对表的结构不满意，比如想增加或者删除一列，想修改某一列的数据类型或者属性，想对表名或者列名进行重命名，这些操作统统都算是修改表结构。`MySQL`给我们提供了一系列修改表结构的语句。

#### 修改表名

我们可以通过下边这两种方式来修改表的名称：

方式一：

```css
ALTER TABLE 旧表名 RENAME TO 新表名;
```

我们把`first_table`表的名称修改为`first_table1`（当前数据库为`xiaohaizi`）：

```sql
mysql> ALTER TABLE first_table RENAME TO first_table1;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW TABLES;
+---------------------+
| Tables_in_xiaohaizi |
+---------------------+
| first_table1        |
| student_info        |
| student_score       |
+---------------------+
3 rows in set (0.00 sec)

mysql>
```

通过`SHOW TABLES`命令可以看到已经改名成功了。

方式二：

```css
RENAME TABLE 旧表名1 TO 新表名1, 旧表名2 TO 新表名2, ... 旧表名n TO 新表名n;
```

这种改名方式的牛逼之处就是它可以在一条语句中修改多个表的名称。这里就不举例了，自己测试一下吧。

如果在修改表名的时候指定了数据库名，还可以将该表转移到对应的数据库下，比方说我们先再创建一个数据库`dahaizi`：

```shell
mysql> CREATE DATABASE dahaizi;
Query OK, 1 row affected (0.00 sec)
```

然后把`first_table1`表转移到这个`dahaizi`数据库下：

```sql
mysql> ALTER TABLE first_table1 RENAME TO dahaizi.first_table1;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW TABLES FROM dahaizi;
+-------------------+
| Tables_in_dahaizi |
+-------------------+
| first_table1      |
+-------------------+
1 row in set (0.00 sec)

mysql> SHOW TABLES FROM xiaohaizi;
+---------------------+
| Tables_in_xiaohaizi |
+---------------------+
| student_info        |
| student_score       |
+---------------------+
2 rows in set (0.00 sec)

mysql>
```

可以看到`first_table1`就从数据库`xiaohaizi`转移到`dahaizi`里边了。我们再用修改表名的方式二再把该表转移到`xiaohaizi`数据库中，并且将其更名为`first_table`：

```css
mysql> RENAME TABLE dahaizi.first_table1 TO xiaohaizi.first_table;
Query OK, 0 rows affected (0.00 sec)
```

#### 增加列

我们可以使用下边的语句来增加表中的列：

```sql
ALTER TABLE 表名 ADD COLUMN 列名 数据类型 [列的属性];
```

比如我们向`first_table`里添加一个名叫`third_column`的列就可以这么写：

```sql
mysql> ALTER TABLE first_table ADD COLUMN third_column CHAR(4) ;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE first_table\G
*************************** 1. row ***************************
       Table: first_table
Create Table: CREATE TABLE `first_table` (
  `first_column` int(11) DEFAULT NULL,
  `second_column` varchar(100) DEFAULT NULL,
  `third_column` char(4) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
1 row in set (0.01 sec)

mysql>
```

通过查看表的结构可以看到`third_column`列已经添加成功了。

##### 增加列到特定位置

默认的情况下列都是加到现有列的最后一列后面，我们也可以在添加列的时候指定它的位置，常用的方式如下

添加到第一列：

```sql
ALTER TABLE 表名 ADD COLUMN 列名 列的类型 [列的属性] FIRST;
```

让我们把`fourth_column`插入到第一列：

```sql
mysql> ALTER TABLE first_table ADD COLUMN fourth_column CHAR(4) FIRST;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE first_table\G
*************************** 1. row ***************************
   Table: first_table
Create Table: CREATE TABLE `first_table` (
`fourth_column` char(4) DEFAULT NULL,
`first_column` int(11) DEFAULT NULL,
`second_column` varchar(100) DEFAULT NULL,
`third_column` char(4) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
1 row in set (0.01 sec)

mysql>
```

看到插入成功了

- 添加到指定列的后边：

  ```sql
  ALTER TABLE 表名 ADD COLUMN 列名 列的类型 [列的属性] AFTER 指定列名;
  ```

  再插入一个`fifth_column`到`first_column`后边瞅瞅：

  ```sql
  mysql> ALTER TABLE first_table ADD COLUMN fifth_column CHAR(4) AFTER first_column;
  Query OK, 0 rows affected (0.03 sec)
  Records: 0  Duplicates: 0  Warnings: 0
  
  mysql> SHOW CREATE TABLE first_table\G
  *************************** 1. row ***************************
         Table: first_table
  Create Table: CREATE TABLE `first_table` (
    `fourth_column` char(4) DEFAULT NULL,
    `first_column` int(11) DEFAULT NULL,
    `fifth_column` char(4) DEFAULT NULL,
    `second_column` varchar(100) DEFAULT NULL,
    `third_column` char(4) DEFAULT NULL
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
  1 row in set (0.00 sec)
  
  mysql>
  ```

  可以看到`fifth_column`列就被插到`first_column`列后边了。

#### 删除列

我们可以使用下边的语句来删除表中的列：

```sql
ALTER TABLE 表名 DROP COLUMN 列名;
```

我们把刚才向`first_table`里添加几个列都删掉试试：

```sql
mysql> ALTER TABLE first_table DROP COLUMN third_column;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE first_table DROP COLUMN fourth_column;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE first_table DROP COLUMN fifth_column;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> SHOW CREATE TABLE first_table\G
*************************** 1. row ***************************
       Table: first_table
Create Table: CREATE TABLE `first_table` (
  `first_column` int(11) DEFAULT NULL,
  `second_column` varchar(100) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
1 row in set (0.00 sec)

mysql>
```

从结果中可以看出`third_column`、`fourth_column`、`fifth_column`这几个列都被删除了。

#### 修改列信息

修改列的信息有下边这两种方式：

- 方式一：

  ```css
  ALTER TABLE 表名 MODIFY 列名 新数据类型 [新属性];
  ```

  我们来修改一下`first_table`表的`second_column`列，把它的数据类型修改为`VARCHAR(2)`：

  ```sql
  mysql> ALTER TABLE first_table MODIFY second_column VARCHAR(2);
  Query OK, 0 rows affected (0.04 sec)
  Records: 0  Duplicates: 0  Warnings: 0
  
  mysql> SHOW CREATE TABLE first_table\G
  *************************** 1. row ***************************
         Table: first_table
  Create Table: CREATE TABLE `first_table` (
    `first_column` int(11) DEFAULT NULL,
    `second_column` varchar(2) DEFAULT NULL
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
  1 row in set (0.00 sec)    
  
  mysql>
  ```

  可以看到`second_column`的数据类型就已经被修改为`VARCHAR(2)`了。不过在修改列信息的时候需要注意：修改后的数据类型和属性一定要兼容表中现有的数据！比方说原先`first_table`表的类型是`VARCHAR(100)`，该类型最多能存储100个字符，如果表中的某条记录的`second_column`列值为`'aaa'`，也就是占用了3个字符，而此时我们尝试使用上边的语句将`second_column`列的数据类型修改为`VARCHAR(2)`，那么此时就会报错，因为`VARCHAR(2)`并不能存储3个字符。

- 方式二：

  ```css
  ALTER TABLE 表名 CHANGE 旧列名 新列名 新数据类型 [新属性];
  ```

  可以看到这种修改方式需要我们填两个列名，也就是说在修改数据类型和属性的同时也可以修改列名！比如我们修改`second_column`的列名为`second_column1`：

  ```sql
  mysql> ALTER TABLE first_table CHANGE second_column second_column1 VARCHAR(2)\G
  Query OK, 0 rows affected (0.04 sec)
  Records: 0  Duplicates: 0  Warnings: 0
  
  mysql> SHOW CREATE TABLE first_table\G
  *************************** 1. row ***************************
         Table: first_table
  Create Table: CREATE TABLE `first_table` (
    `first_column` int(11) DEFAULT NULL,
    `second_column1` varchar(2) DEFAULT NULL
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
  1 row in set (0.00 sec)
  
  mysql>
  ```

  可以看到结果中`second_column`的列名已经被修改为了`second_column1`，不过我们并没有改动该列的数据类型和属性，所以直接把旧的数据类型和属性抄过来就好了。

#### 修改列排列位置

如果我们觉得当前列的顺序有问题的话，可以使用下边这几条语句进行修改：

1. 将列设为表的第一列：

   ```sql
   ALTER TABLE 表名 MODIFY 列名 列的类型 列的属性 FIRST;
   ```

   先看一下现在表`first_table`的各个列的排列顺序：

   ```sql
   mysql> SHOW CREATE TABLE first_table\G
   *************************** 1. row ***************************
          Table: first_table
   Create Table: CREATE TABLE `first_table` (
     `first_column` int(11) DEFAULT NULL,
     `second_column1` varchar(2) DEFAULT NULL
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
   1 row in set (0.00 sec)
   
   mysql>
   ```

   可以看到，列的顺序依次是：`first_column`、`second_column1`。现在我们想把`second_column`放在第一列可以这么写：

   ```sql
   mysql> ALTER TABLE first_table MODIFY second_column1 VARCHAR(2) FIRST;
   Query OK, 0 rows affected (0.02 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> SHOW CREATE TABLE first_table\G
   *************************** 1. row ***************************
          Table: first_table
   Create Table: CREATE TABLE `first_table` (
     `second_column1` varchar(2) DEFAULT NULL,
     `first_column` int(11) DEFAULT NULL
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
   1 row in set (0.00 sec)
   
   mysql>
   ```

   看到`second_column1`已经成为第一列了！

2. 将列放到指定列的后边：

   ```sql
   ALTER TABLE 表名 MODIFY 列名 列的类型 列的属性 AFTER 指定列名;
   ```

   比方说我们想把`second_column1`再放到`first_column`后边可以这么写：

   ```sql
   mysql> ALTER TABLE first_table MODIFY second_column1 VARCHAR(2) AFTER first_column;
   Query OK, 0 rows affected (0.03 sec)
   Records: 0  Duplicates: 0  Warnings: 0
   
   mysql> SHOW CREATE TABLE first_table\G
   *************************** 1. row ***************************
          Table: first_table
   Create Table: CREATE TABLE `first_table` (
     `first_column` int(11) DEFAULT NULL,
     `second_column1` varchar(2) DEFAULT NULL
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='第一个表'
   1 row in set (0.00 sec)
   ```

#### 一条语句中包含多个修改操作

如果对同一个表有多个修改操作的话，我们可以把它们放到一条语句中执行，就像这样：

```sql
ALTER TABLE 表名 操作1, 操作2, ..., 操作n;
```

上边我们在演示删除列操作的时候用三条语句连着删了`third_column`、`fourth_column`和`fifth_column`这三个列，其实这三条语句可以合并为一条：

```sql
ALTER TABLE first_table DROP COLUMN third_column, DROP COLUMN fourth_column, DROP COLUMN fifth_column;
```

这样人敲的语句也少了，服务器也不用分多次执行从而效率也高了，何乐而不为呢？

## 列的属性

### 简单插入语句

`MySQL`插入数据的时候是以行为单位的，语法格式如下：

```scss
INSERT INTO 表名(列1, 列2, ...) VALUES(列1的值，列2的值, ...);
```

```sql
INSERT INTO first_table(first_column, second_column) VALUES(1, 'aaa');
```

我们也可以只指定部分的列，没有显式指定的列的值将被设置为`NULL`，`NULL`的意思就是此列的值尚不确定。比如这样写：

```scss
mysql> INSERT INTO first_table(first_column) VALUES(2);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO first_table(second_column) VALUES('ccc');
Query OK, 1 row affected (0.00 sec)

mysql>
```

这两条语句的意思就是：

- 第一条插入语句我们只指定了`first_column`列的值是2，而没有指定`second_column`的值，所以`second_column`的值就是`NULL`。
- 第二条插入语句我们只指定了`second_column`的值是`'ccc'`，而没有指定`first_column`的值，所以`first_column`的值就是`NULL`。

### 批量插入

每插入一行数据写一条语句也不是不行，但是对人来说太烦了，而且每插入一行数据就向服务器提交一个请求远没有一次把所有插入的数据提交给服务器效率高，所以`MySQL`为我们提供了批量插入记录的语句：

```scss
INSERT INTO 表名(列1,列2, ...) VAULES(列1的值，列2的值, ...), (列1的值，列2的值, ...), (列1的值，列2的值, ...), ...;
```

也就是在原来的单条插入语句后边多写几条记录的内容，用逗号分隔开就好了，举个例子：

### 列的属性

我们在上一章唠叨表结构的时候说表中的每个列都可以有一些属性，至于这些属性是什么以及怎么在创建表的时候把它们定义出来就是本章接下来的内容哈。不过我们之后还会用到`first_table`表做示例，所以先把该表删掉：

```sql
mysql> DROP TABLE first_table;
Query OK, 0 rows affected (0.01 sec)
```

### 默认值

我们刚说过在书写`INSERT`语句插入记录的时候可以只指定部分的列，那些没有被显式指定的列的值将被设置为`NULL`，换一种说法就是列的默认值为`NULL`，`NULL`的含义是这个列的值还没有被设置。如果我们不想让默认值为`NULL`，而是设置成某个有意义的值，可以在定义列的时候给该列增加一个`DEFAULT`属性，就像这样：

```arduino
列名 列的类型 DEFAULT 默认值
```

比如我们把`first_table`的`second_column`列的默认值指定为`'abc'`，创建一下这个表：

```sql
mysql> CREATE TABLE first_table (
    ->     first_column INT,
    ->     second_column VARCHAR(100) DEFAULT 'abc'
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql>
```

然后插入一条数据后看看默认值是不是起了作用：

### NOT NULL属性

有时候我们需要要求表中的某些列中必须有值，不能存放`NULL`，那么可以用这样的语法来定义这个列：

```arduino
列名 列的类型 NOT NULL
```

比如我们把`first_table`表的`first_column`列定义一个`NOT NULL`属性。当然，我们在重新定义表之前需要把原来的表删掉：

```sql
mysql> DROP TABLE first_table;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE first_table (
    ->     first_column INT NOT NULL,
    ->     second_column VARCHAR(100) DEFAULT 'abc'
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql>
```

这样的话，我们就不能再往这个字段里插入`NULL`值了，比如这样：

```sql
mysql> INSERT INTO first_table(first_column, second_column) VALUES(NULL, 'aaa');
ERROR 1048 (23000): Column 'first_column' cannot be null
mysql>
```

看到报了个错，提示`first_column`列不能存储`NULL`。

另外，一旦对某个列定义了`NOT NULL`属性，那这个列的默认值就不为`NULL`了。上边`first_column`并没有指定默认值，意味着我们在使用`INSERT`插入行时必须显式的指定这个列的值，而不能省略它，比如这样就会报错的：

```scss
mysql> INSERT INTO first_table(second_column) VALUES('aaa');
ERROR 1364 (HY000): Field 'first_column' doesn't have a default value
mysql>
```

可以看到执行结果提示我们`first_column`并没有设置默认值，所以在使用`INSERT`语句插入记录的时候不能省略掉这个列的值。

### 主键

有时候在我们的表里可以通过某个列或者某些列确定唯一的一条记录，我们就可以把这个列或者这些列称为`候选键`。比如在学生信息表`student_info`中，只要我们知道某个学生的学号，就可以确定一个唯一的学生信息，也就是一条记录。当然，我们也可以通过身份证号来确定唯一的一条学生信息记录，所以`学号`和`身份证号`都可以作为学生信息表的`候选键`。在学生成绩表`student_score`中，我们可以通过`学号`和`科目`这两个列的组合来确定唯一的一条成绩记录，所以`学号、科目`这两个列的组合可以作为学生成绩表的`候选键`。

一个表可能有多个候选键，我们可以选择一个候选键作为表的`主键`。一个表最多只能有一个主键，主键的值不能重复，通过主键可以找到唯一的一条记录。如果我们的表中有定义主键的需求可以选用下边这两种方式之一来指定主键：

1. 如果主键只是单个列的话，可以直接在该列后声明`PRIMARY KEY`，比如我们把学生信息表`student_info`的`学号`列声明为主键可以这么写：

   ```sql
   CREATE TABLE student_info (
       number INT PRIMARY KEY,
       name VARCHAR(5),
       sex ENUM('男', '女'),
       id_number CHAR(18),
       department VARCHAR(30),
       major VARCHAR(30),
       enrollment_time DATE
   );
   ```

2. 我们也可以把主键的声明单独提取出来，用这样的形式声明：

   ```java
   PRIMARY KEY (列名1, 列名2, ...)
   ```

   然后把这个主键声明放到列定义的后边就好了。比如`student_info`的`学号`列声明为主键也可以这么写：

   ```sql
   CREATE TABLE student_info (
       number INT,
       name VARCHAR(5),
       sex ENUM('男', '女'),
       id_number CHAR(18),
       department VARCHAR(30),
       major VARCHAR(30),
       enrollment_time DATE,
       PRIMARY KEY (number)
   );
   ```

   值得注意的是，对于多个列的组合作为主键的情况，必须使用这种单独声明的形式，比如`student_score`表里的`学号,科目`的列组合作为主键，可以这么写：

   ```sql
   CREATE TABLE student_score (
       number INT,
       subject VARCHAR(30),
       score TINYINT,
       PRIMARY KEY (number, subject)
   );
   ```

在我们创建表的时候就声明了主键的话，`MySQL`会对我们插入的记录做校验，如果新插入记录的主键值已经在表中存在了，那就会报错。

另外，主键列默认是有`NOT NULL`属性，也就是必填的，如果填入`NULL`值会报错(先删除原来的`student_info`表，使用刚才所说的两种方式之一重新创建表之后仔执行下边的语句)：

```sql
mysql> INSERT INTO student_info(number) VALUES(NULL);
ERROR 1048 (23000): Column 'number' cannot be null
mysql>
```

所以大家在插入数据的时候至少别忘了给主键列赋值哈～

### UNIQUE属性

对于不是主键的其他候选键，如果也想让`MySQL`在我们向表中插入新记录的时候帮助我们校验一下某个列或者列组合的值是否重复，那么我们可以把这个列或列组合添加一个`UNIQUE`属性，表明该列或者列组合的值是不允许重复的。与我们在建表语句中声明主键的方式类似，为某个列声明`UNIQUE`属性的方式也有两种：

1. 如果我们想为单个列声明`UNIQUE`属性，可以直接在该列后填写`UNIQUE`或者`UNIQUE KEY`，比如在学生信息表`student_info`中，我们不允许两条学生基本信息记录中的身份证号是一样的，那我们可以为`id_number`列添加`UNIQUE`属性：

   ```sql
   CREATE TABLE student_info (
       number INT PRIMARY KEY,
       name VARCHAR(5),
       sex ENUM('男', '女'),
       id_number CHAR(18) UNIQUE,
       department VARCHAR(30),
       major VARCHAR(30),
       enrollment_time DATE
   );
   ```

2. 我们也可以把`UNIQUE`属性的声明单独提取出来，用这样的形式声明：

   ```scss
   UNIQUE [约束名称] (列名1, 列名2, ...)
   ```

   或者：

   ```scss
   UNIQUE KEY [约束名称] (列名1, 列名2, ...)
   ```

   其实每当我们为某个列添加了一个`UNIQUE`属性，就像是在孙悟空头上带了个紧箍咒一样，从此我们插入的记录的该列的值就不能重复，所以为某个列添加一个`UNIQUE`属性也可以认为是为这个表添加了一个`约束`，我们就称之为`UNIQUE`约束。每个约束都可以有一个名字，像主键也算是一个约束，它的名字就是默认的`PRIMARY`。不过一个表中可以为不同的列添加多个`UNIQUE`属性，也就是添加多个`UNIQUE`约束，每添加一个`UNIQUE`约束，我们就可以给它起个名，这也是上边的`约束名称`的含义。不过`约束名称`是被中括号`[]`扩起来的，意味着我们写不写都可以，如果不写的话`MySQL`自己会帮我们起名。其实就像是自己生了个孩子，如果自己不起名的话，人家公安局的警察叔叔也得给孩子起个名上户口。

   为约束起名的事儿理解了之后，我们把这个`UNIQUE`属性的声明放到列定义的后边就好了。比如我们为`student_info`表的`id_number`（身份证号）列添加`UNIQUE`属性也可以这么写：

   ```sql
   CREATE TABLE student_info (
       number INT PRIMARY KEY,
       name VARCHAR(5),
       sex ENUM('男', '女'),
       id_number CHAR(18),
       department VARCHAR(30),
       major VARCHAR(30),
       enrollment_time DATE,
       UNIQUE KEY uk_id_number (id_number)
   );
   ```

   可以看到，我们给这个`UNIQUE`约束起的名儿就是`uk_id_number`。

   不过值得注意的是，对于多个列的组合具有`UNIQUE`属性的情况，必须使用这种单独声明的形式。

如果表中为某个列或者列组合定义了`UNIQUE`属性的话，`MySQL`会对我们插入的记录做校验，如果新插入记录在该列或者列组合的值已经在表中存在了，那就会报错！

### 主键和`UNIQUE`约束的区别

主键和`UNIQUE`约束都能保证某个列或者列组合的唯一性，但是：

1. 一张表中只能定义一个主键，却可以定义多个`UNIQUE`约束！
2. 规定：主键列不允许存放NULL，而声明了`UNIQUE`属性的列可以存放`NULL`，而且`NULL`可以重复地出现在多条记录中！

```!
小贴士：

一个表的某个列声明了UNIQUE属性，那这个列的值不就不可以重复了么，为啥NULL这么特殊？哈哈，NULL就是这么特殊。NULL其实并不是一个值，它代表不确定，我们平常说某个列的值为NULL，意味着这一列的值尚未被填入。
```

### 外键

插入到学生成绩表`student_score`中的`number`(学号)列中的值必须能在学生基本信息表`student_info`中的`number`列中找到，否则如果一个学号只在成绩表里出现，而在基本信息表里找不到相应的记录的话，就相当于插入了不知道是哪个学生的成绩，这显然是荒谬的。为了防止这样荒谬的情况出现，`MySQL`给我们提供了外键约束机制。定义外键的语法是这样的：

```scss
CONSTRAINT [外键名称] FOREIGN KEY(列1, 列2, ...) REFERENCES 父表名(父列1, 父列2, ...);
```

其中的`外键名称`也是可选的，一个名字而已，我们不自己命名的话，MySQL自己会帮助我们命名。

如果A表中的某个列或者某些列依赖与B表中的某个列或者某些列，那么就称A表为`子表`，B表为`父表`。子表和父表可以使用外键来关联起来，上边例子中`student_score`表的`number`列依赖于`student_info`的`number`列，所以`student_info`就是一个父表，`student_score`就是子表。我们可以在`student_score`的建表语句中来定义一个外键：

```sql
CREATE TABLE student_score (
    number INT,
    subject VARCHAR(30),
    score TINYINT,
    PRIMARY KEY (number, subject),
    CONSTRAINT FOREIGN KEY(number) REFERENCES student_info(number)
);
```

这样，在对`student_score`表插入数据的时候，`MySQL`都会为我们检查一下插入的学号是否能在`student_info`表中找到，如果找不到则会报错。

```typescript
小贴士：

父表中被子表依赖的列或者列组合必须建立索引，如果该列或者列组合已经是主键或者有UNIQUE属性，那么它们也就被默认建立了索引。示例中student_score表依赖于stuent_info表的number列，而number列又是stuent_info的主键（注意上一章定义的student_info结构中没有把number列定义为主键，本章才将其定义为主键，如果你的机器上还没有将其定义为主键的话，赶紧修改表结构呗～），所以在student_score表中创建外键是没问题的。

当然至于什么是索引，不是我们从零蛋开始学习MySQL的同学们需要关心的事，等学完本书之后再去看《MySQL是怎样运行的：从根儿上理解MySQL》就懂了。
```

### AUTO_INCREMENT属性

`AUTO_INCREMENT`翻译成中文可以理解为`自动增长`，简称自增。如果一个表中的某个列的数据类型是整数类型或者浮点数类型，那么这个列可以设置`AUTO_INCREMENT`属性。当我们把某个列设置了`AUTO_INCREMENT`属性之后，如果我们在插入新记录的时候不指定该列的值，或者将该列的值显式地指定为`NULL`或者`0`，那么新插入的记录在该列上的值就是当前该列的最大值加1后的值（有一点点绕，稍后一举例子大家就明白了）。我们可以用这样的语法来定义这个列：

```
列名 列的类型 AUTO_INCREMENT
```

比如我们想在`first_table`表里设置一个名为`id`的列，把这个列设置为主键，来唯一标记一条记录，然后让其拥有`AUTO_INCREMENT`属性，我们可以这么写：

```sql
mysql> DROP TABLE first_table;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE first_table (
    ->     id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    ->     first_column INT,
    ->     second_column VARCHAR(100) DEFAULT 'abc'
    -> );
Query OK, 0 rows affected (0.01 sec)

mysql>
```

先把原来的表删掉，然后在新表中增加了一个非负`INT`类型的`id`列，把它设置为主键而且具有`AUTO_INCREMENT`属性，那我们在插入新记录时可以忽略掉这个列，或者将列值显式地指定为`NULL`或`0`，但是它的值将会递增，看：

```sql
mysql> INSERT INTO first_table(first_column, second_column) VALUES(1, 'aaa');
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO first_table(id, first_column, second_column) VALUES(NULL, 1, 'aaa');
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO first_table(id, first_column, second_column) VALUES(0, 1, 'aaa');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM first_table;
+----+--------------+---------------+
| id | first_column | second_column |
+----+--------------+---------------+
|  1 |            1 | aaa           |
|  2 |            1 | aaa           |
|  3 |            1 | aaa           |
+----+--------------+---------------+
3 rows in set (0.01 sec)

mysql>
```

可以看到，列`id`是从1开始递增的。在为列定义`AUTO_INCREMENT`属性的时候需要注意这几点：

1. 一个表中最多有一个具有AUTO_INCREMENT属性的列。
2. 具有AUTO_INCREMENT属性的列必须建立索引。主键和具有`UNIQUE`属性的列会自动建立索引。不过至于什么是索引，在学习MySQL进阶的时候才会介绍。
3. 拥有AUTO_INCREMENT属性的列就不能再通过指定DEFAULT属性来指定默认值。
4. 一般拥有AUTO_INCREMENT属性的列都是作为主键的属性，来自动生成唯一标识一条记录的主键值。

### 列的注释

上一章中我们说了在建表语句的末尾可以添加`COMMENT`语句来给表添加注释，其实我们也可以在每一个列末尾添加`COMMENT`语句来为列来添加注释，比方说：

```sql
CREATE TABLE first_table (
    id int UNSIGNED AUTO_INCREMENT PRIMARY KEY COMMENT '自增主键',
    first_column INT COMMENT '第一列',
    second_column VARCHAR(100) DEFAULT 'abc' COMMENT '第二列'
) COMMENT '第一个表';
```

### 影响展示外观的ZEROFILL属性

下边是正整数`3`的三种写法：

- 写法一：`3`
- 写法二：`003`
- 写法三：`000003`

有的同学笑了，这不是脱了裤子放屁么，我在`3`前边加上一万个`0`最终的值也是`0`呀，这有啥用？提出这类问题的同学肯定没有艺术细胞，它们长的不一样啊 —— 有的数字前边没0，有的数字前边0少，有的数字前边0多，可能有的人就觉得在数字前头补一堆0长得好看呢？

对于无符号整数类型的列，我们可以在查询数据的时候让数字左边补0，如果想实现这个效果需要给该列加一个`ZEROFILL`属性（也可以理解为这是一个属于数据类型的属性），就像这样：

```sql
mysql> CREATE TABLE zerofill_table (
    ->     i1 INT UNSIGNED ZEROFILL,
    ->     i2 INT UNSIGNED
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql>
```

我们在`zerofill_table`表中创建了两个无符号整数列，不同的是`i1`列具有`ZEROFILL`属性，下边我们为这个表插入一条记录：

```scss
mysql> INSERT INTO zerofill_table(i1, i2) VALUES(1, 1);
Query OK, 1 row affected (0.00 sec)

mysql>
```

然后我们使用查询语句来展示一下刚插入的数据：

```sql
mysql> SELECT * FROM zerofill_table;
+------------+------+
| i1         | i2   |
+------------+------+
| 0000000001 |    1 |
+------------+------+
1 row in set (0.00 sec)

mysql>
```

对于具有`ZEROFILL`属性的`i1`列，在显示的时候在数字前边补了一堆0，仔细数数发现是9个0，而没有`ZEROFILL`属性的`i2`列，在显示的时候并没有在数字前补0。为什么`i1`列会补9个0呢？我们查看一下`zerofill_table`的表结构：

```sql
mysql> SHOW CREATE TABLE zerofill_table\G
*************************** 1. row ***************************
       Table: zerofill_table
Create Table: CREATE TABLE `zerofill_table` (
  `i1` int(10) unsigned zerofill DEFAULT NULL,
  `i2` int(10) unsigned DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1
1 row in set (0.01 sec)

mysql>
```

可以看到，其实`i1`和`i2`列的类型`INT`后边都加了一个`(10)`，这个`10`就是所谓的`显示宽度`。`显示宽度`是在查询语句显示的结果中，如果声明了 **ZEROFILL** 属性的整数列的实际值的位数小于显示宽度时，会在实际值的左侧补0，使补0的位数和实际值的位数相加正好等于显示宽度。我们也可以自己指定显示宽度，比方说这样：

```sql
mysql> DROP TABLE zerofill_table;
Query OK, 0 rows affected (0.00 sec)

mysql> CREATE TABLE zerofill_table (
    ->     i1 INT(5) UNSIGNED ZEROFILL,
    ->     i2 INT UNSIGNED
    -> );
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO zerofill_table(i1, i2) VALUES(1, 1);
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM zerofill_table;
+-------+------+
| i1    | i2   |
+-------+------+
| 00001 |    1 |
+-------+------+
1 row in set (0.00 sec)

mysql>
```

新创建的表中，`i1`字段的显示宽度是5，所以最后的显示结果中补了4个0。不过在使用`ZEROFILL`属性时应该注意下边几点：

- 在展示查询结果时，某列数据自动补0的条件有这几个：

  - 该列必须是整数类型的
  - 该列必须有`UNSIGNED ZEROFILL`的属性
  - 该列的实际值的位数必须小于显示宽度

- 在创建表的时候，如果声明了`ZEROFILL`属性的列没有声明`UNSIGNED`属性，那`MySQL`会为该列自动生成`UNSIGNED`属性。

  也就是说如果我们创建表语句是这样的：

  ```sql
  CREATE TABLE zerofill_table (
      i1 INT ZEROFILL,
      i2 INT UNSIGNED
  );
  ```

  `MySQL`会自动帮我们为`i1`列加上`UNSIGNED`属性，也就是这样：

  ```sql
  CREATE TABLE zerofill_table (
      i1 INT UNSIGNED ZEROFILL,
      i2 INT UNSIGNED
  );
  ```

  也就是说`MySQL`现在只支持对无符号整数进行自动补0的操作。

- 每个整数类型都会有默认的显示宽度。

  比如`TINYINT`的默认显示宽度是`4`，`INT`的默认显示宽度是`(11)`... 如果加了`UNSIGNED`属性，则该类型的显示宽度减1，比如`TINYINT UNSIGNED`的显示宽度是`3`，`INT UNSIGNED`的显示宽度是`10`。

- 显示宽度并不会影响实际类型的实际存储空间。

  显示宽度仅仅是在展示查询结果时，如果整数的位数不够显示宽度的情况下起作用的，并不影响该数据类型要求的存储空间以及该类型能存储的数据范围，也就是说`INT(1)`和`INT(10)`仅仅在展示时可能有区别，在别的方面没有任何区别。比方说`zerofill_table`表中`i1`列的显示宽度是5，而数字`12345678`的位数是8，它照样可以被填入`i1`列中：

  ```scss
  mysql> INSERT INTO zerofill_table(i1, i2) VALUES(12345678, 12345678);
  Query OK, 1 row affected (0.01 sec)
  
  mysql>
  ```

- 只有列的实际值的位数小于显示宽度时才会补0，实际值的位数大于显示宽度时照原样输出。

  比方说我们刚刚把`12345678`存到了`i1`列里，在展示这个值时，并不会截短显示的数据，而是照原样输出：

  ```sql
  mysql> SELECT * FROM zero_table;
  +----------+----------+
  | i1       | i2       |
  +----------+----------+
  |    00001 |        1 |
  | 12345678 | 12345678 |
  +----------+----------+
  2 rows in set (0.00 sec)
  
  mysql>
  ```

- 对于没有声明`ZEROFILL`属性的列，显示宽度没有一毛钱卵用。

  只有在查询声明了`ZEROFILL`属性的列时，显示宽度才会起作用，否则忽略显示宽度这个东西的存在。

### 一个列同时具有多个属性

每个列可以同时具有多个属性，属性声明的顺序无所谓，各个属性之间用空白隔开就好了～

```sql
小贴士：

注意，有的属性是冲突的，一个列不能具有两个冲突的属性，。如一个列不能既声明为PRIMARY KEY，又声明为UNIQUE KEY，不能既声明为DEFAULT NULL，又声明为NOT NULL。大家在使用过程中需要注意这一点。
```

### 查看表结构时的列属性

上一章我们唠叨了一些可以以表格的形式展示表结构的语句，但是忽略了关于列的属性的一些列，现在我们再看一遍`student_info`表的结构：

```sql
mysql> DESC student_info;
+-----------------+-------------------+------+-----+---------+-------+
| Field           | Type              | Null | Key | Default | Extra |
+-----------------+-------------------+------+-----+---------+-------+
| number          | int(11)           | NO   | PRI | NULL    |       |
| name            | varchar(5)        | YES  |     | NULL    |       |
| sex             | enum('男','女')   | YES  |     | NULL    |       |
| id_number       | char(18)          | YES  | UNI | NULL    |       |
| department      | varchar(30)       | YES  |     | NULL    |       |
| major           | varchar(30)       | YES  |     | NULL    |       |
| enrollment_time | date              | YES  |     | NULL    |       |
+-----------------+-------------------+------+-----+---------+-------+
7 rows in set (0.00 sec)

mysql>
```

可以看到：

- `NULL`列代表该列是否可以存储`NULL`，值为`NO`时，表示不允许存储`NULL`，值为`YES`是表示可以存储`NULL`。
- `Key`列存储关于所谓的`键`的信息，当值为`PRI`是`PRIMARY KEY`的缩写，代表主键；`UNI`是`UNIQUE KEY`的缩写，代表`UNIQUE`属性。
- `Default`列代表该列的默认值。
- `Extra`列展示一些额外的信息。比方说如果某个列具有`AUTO_INCREMENT`属性就会被展示在这个列里。

### 标识符的命名

像数据库名、表名、列名、约束名称或者我们之后会遇到的别的名称，这些名称统统被称为`标识符`。虽然`MySQL`中对`标识符`的命名没多少限制，但是却不欢迎下边的这几种命名：

1. 名称中全都是数字。

   因为在一些`MySQL`语句中也会使用到数字，如果你起的名称中全部都是数字，会让`MySQL`服务器分别不清哪个是名称，哪个是数字了。比如名称`1234567`就是非法的。

2. 名称中有空白字符

   `MySQL`命令是靠空白字符来分隔各个单词的，比如下边这两行命令是等价的：

   ```ini
   CREATE DATABASE xiaohaizi;
   CREATE   DATABASE   xiaohaizi;
   ```

   但是如果你定义的名称中有空白字符，这样会被当作两个词去处理，就会造成歧义。比如名称`word1 word2 word3`就是非法的。

3. 名称使用了`MySQL`中的保留字

   比方说`CREATE`、`DATABASE`、`INT`、`DOUBLE`、`DROP`、`TABLE`等等这些单词，这些单词都是供MySQL内部使用的，称之为保留字。如果你自己定义的名称用到了这些词儿也会导致歧义。比如名称`create`就是非法的。

虽然某些名称可能会导致歧义，但是如果你坚持要使用的话，也不是不行，你可以使用反引号````来将你定义的名称扩起来，这样`MySQL`的服务器就能检测到你提供的是一个名称而不是别的什么东西，比如说把上边几个非法的名称加上反引号````就变成合法的名称了：

```go
`1234567`
`word1 word2    word3`
`create`
```

我们上边对表`first_table`的定义可以把里边的标识符全都使用反引号````引起来，这样语义更清晰一点：

```sql
CREATE TABLE `first_table` (
    `id` int UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    `first_column` INT,
    `second_column` VARCHAR(100) DEFAULT 'abc'
);
```

虽然反引号比较强大，但是我们还是建议大家不要起各种非主流的名称，也不要使用全数字、带有空白字符或者MySQL保留字的名称。由于MySQL是C语言实现的，所以在名称定义上还是尽量遵从C语言的规范吧，就是用小写字母、数字、下划线、美元符号等作为名称，如果有多个单词的话，各个单词之间用下划线连接起来，比如`student`、`student_info`啥的～

## 简单查询

### 查询单个列

查看某个表中的某一列的数据的通用格式是这样：

```sql
SELECT 列名 FROM 表名;
```

也就是说把需要查询的列名放到单词`SELECT`后边就好了，比如查看`student_info`表中的`number`列的数据可以这么写：

```sql
mysql> SELECT number FROM student_info;
+----------+
| number   |
+----------+
| 20180104 |
| 20180102 |
| 20180101 |
| 20180103 |
| 20180105 |
| 20180106 |
+----------+
6 rows in set (0.00 sec)
```

#### 列的别名

我们也可以为结果集中的列重新定义一个`别名`，命令格式如下：

```vbnet
SELECT 列名 [AS] 列的别名 FROM 表名;
```

我们看到`AS`被加了个中括号，意味着可有可无，没有`AS`的话，列名和列的别名之间用空白字符隔开就好了。比如我们想给`number`列起个别名，可以使用下边这两种方式之一：

- 方式一

  ```vbnet
  SELECT number AS 学号 FROM student_info;
  ```

- 方式二：

  ```sql
  SELECT number 学号 FROM student_info;
  ```

### 查询多个列

如果想查询多个列的数据，可以在`SELECT`后边写多个列名，用逗号`,`分隔开就好：

```sql
SELECT 列名1, 列名2, ... 列名n FROM 表名;
```

我们把`SELECT`语句后边跟随的多个列统称为`查询列表`，需要注意的是，查询列表中的列名可以按任意顺序摆放，结果集将按照我们指定的列名顺序显示。比如我们查询`student_info`中的多个列：

```sql
mysql> SELECT number, name, id_number, major FROM student_info;
+----------+-----------+--------------------+--------------------------+
| number   | name      | id_number          | major                    |
+----------+-----------+--------------------+--------------------------+
| 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
| 20180103 | 范统      | 17156319980116959X | 软件工程                 |
| 20180104 | 史珍香    | 141992199701078600 | 软件工程                 |
| 20180105 | 范剑      | 181048199308156368 | 飞行器设计               |
| 20180106 | 朱逸群    | 197995199501078445 | 电子信息                 |
+----------+-----------+--------------------+--------------------------+
6 rows in set (0.00 sec)

mysql>
```

本例中的查询列表就是`number，name，id_number，major`，所以结果集中列的顺序就按照这个顺序来显示。当然，我们也可以用别名来输出这些数据：

```sql
mysql> SELECT number AS 学号, name AS 姓名, id_number AS 身份证号, major AS 专业 FROM student_info;
+----------+-----------+--------------------+--------------------------+
| 学号     | 姓名      | 身份证号           | 专业                     |
+----------+-----------+--------------------+--------------------------+
| 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
| 20180103 | 范统      | 17156319980116959X | 软件工程                 |
| 20180104 | 史珍香    | 141992199701078600 | 软件工程                 |
| 20180105 | 范剑      | 181048199308156368 | 飞行器设计               |
| 20180106 | 朱逸群    | 197995199501078445 | 电子信息                 |
+----------+-----------+--------------------+--------------------------+
6 rows in set (0.00 sec)

mysql>
```

如果你乐意，同一个列可以在查询列表处重复出现（虽然这通常没什么卵用），比如这样：

```sql
mysql> SELECT number, number, number FROM student_info;
+----------+----------+----------+
| number   | number   | number   |
+----------+----------+----------+
| 20180104 | 20180104 | 20180104 |
| 20180102 | 20180102 | 20180102 |
| 20180101 | 20180101 | 20180101 |
| 20180103 | 20180103 | 20180103 |
| 20180105 | 20180105 | 20180105 |
| 20180106 | 20180106 | 20180106 |
+----------+----------+----------+
6 rows in set (0.00 sec)
```

### 查询所有列

如果需要把记录中的所有列都查出来，`MySQL`也提供一个省事儿的办法，我们之前也介绍过，就是直接用星号`*`来表示要查询的东西，就像这样：

```sql
SELECT * FROM 表名;
```

这个命令我们之前看过了，就不多唠叨了。不过需要注意的是，除非你确实需要表中的每个列，否则一般最好别使用星号`*`来查询所有列，虽然星号`*`看起来很方便，不用明确列出所需的列，但是查询不需要的列通常会降低性能。

### 查询结果去重

#### 去除单列的重复结果

有的时候我们查询某个列的数据时会有一些重复的结果，比如我们查询一下`student_info`表的学院信息：

```sql
mysql> SELECT department FROM student_info;
+-----------------+
| department      |
+-----------------+
| 计算机学院      |
| 计算机学院      |
| 计算机学院      |
| 计算机学院      |
| 航天学院        |
| 航天学院        |
+-----------------+
```

因为表里有6条记录，所以给我们返回了6条结果。但是其实好多都是重复的结果，如果我们想去除重复结果的话，可以将`DISTINCT`放在被查询的列前边，就是这样：

```sql
SELECT DISTINCT 列名 FROM 表名;
```

我们对学院信息做一下去重处理：

```sql
mysql> SELECT DISTINCT department FROM student_info;
+-----------------+
| department      |
+-----------------+
| 计算机学院      |
| 航天学院        |
+-----------------+
2 rows in set (0.00 sec)
```

看到结果集中就只剩下不重复的信息了。

#### 去除多列的重复结果

对于查询多列的情况，两条结果重复的意思是：两条结果的每一个列中的值都相同。比如查询学院和专业信息：

```sql
mysql> SELECT department, major FROM student_info;
+-----------------+--------------------------+
| department      | major                    |
+-----------------+--------------------------+
| 计算机学院      | 计算机科学与工程         |
| 计算机学院      | 计算机科学与工程         |
| 计算机学院      | 软件工程                 |
| 计算机学院      | 软件工程                 |
| 航天学院        | 飞行器设计               |
| 航天学院        | 电子信息                 |
+-----------------+--------------------------+
6 rows in set (0.00 sec)
```

查询结果中第1、2行记录中的`department`和`major`列都相同，所以这两条记录就是重复的，同理，第3、4行也是重复的。如果我们想对多列查询的结果去重的话，可以直接把`DISTINCT`放在被查询的列的最前边：

```sql
SELECT DISTINCT 列名1, 列名2, ... 列名n  FROM 表名;
```

比如这样：

```sql
mysql> SELECT DISTINCT department, major FROM student_info;
+-----------------+--------------------------+
| department      | major                    |
+-----------------+--------------------------+
| 计算机学院      | 计算机科学与工程         |
| 计算机学院      | 软件工程                 |
| 航天学院        | 飞行器设计               |
| 航天学院        | 电子信息                 |
+-----------------+--------------------------+
4 rows in set (0.00 sec)
```

### 限制查询结果条数

有时候查询结果的条数会很多，都显示出来可能会撑爆屏幕～ 所以`MySQL`给我们提供了一种限制结果集中的记录条数的方式，就是在查询语句的末尾使用这样的语法：

```ini
LIMIT 开始行, 限制条数;
```

`开始行`指的是我们想从第几行数据开始查询，`限制条数`是结果集中最多包含多少条记录。

```!
小贴士：

在生活中通常都是从1开始计数的，而在计算机中都是从0开始计数的，所以我们平时所说的第1条记录在计算机中算是第0条。比如`student_info`表里的6条记录在计算机中依次表示为：第0条、第1条、第2条、第3条、第4条、第5条。
```

比如我们查询一下`student_info`表，从第0条记录开始，最多查询2条记录可以这么写：

```sql
mysql> SELECT number, name, id_number, major FROM student_info LIMIT 0, 2;
+----------+-----------+--------------------+--------------------------+
| number   | name      | id_number          | major                    |
+----------+-----------+--------------------+--------------------------+
| 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
+----------+-----------+--------------------+--------------------------+
2 rows in set (0.00 sec)

mysql>
```

如果指定的`开始行`大于结果中的行数，那查询结果就什么都没有：

```sql
mysql> SELECT number, name, id_number, major FROM student_info LIMIT 6, 2;
Empty set (0.00 sec)

mysql>
```

如果查询的结果条数不超过`限制条数`，那就可以全部显式出来：

```sql
mysql> SELECT number, name, id_number, major FROM student_info LIMIT 4, 3;
+----------+-----------+--------------------+-----------------+
| number   | name      | id_number          | major           |
+----------+-----------+--------------------+-----------------+
| 20180105 | 范剑      | 181048199308156368 | 飞行器设计      |
| 20180106 | 朱逸群    | 197995199501078445 | 电子信息        |
+----------+-----------+--------------------+-----------------+
2 rows in set (0.00 sec)

mysql>
```

从第4条开始的记录有两条，`限制条数`为3，所以这两条记录都可以被展示在结果集中。

### 使用默认的开始行

`LIMIT`后边也可以只有一个参数，那这个参数就代表着`限制行数`。也就是说我们可以不指定`开始行`，默认的开始行就是第0行，比如我们可以这么写：

```sql
mysql> SELECT number, name, id_number, major FROM student_info LIMIT 3;
+----------+-----------+--------------------+--------------------------+
| number   | name      | id_number          | major                    |
+----------+-----------+--------------------+--------------------------+
| 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
| 20180103 | 范统      | 17156319980116959X | 软件工程                 |
+----------+-----------+--------------------+--------------------------+
3 rows in set (0.00 sec)

mysql>
```

查询结果就展示了从第0条开始的3条记录。

### 对查询结果排序

我们之前查询`number`列的时候得到的记录并不是有序的，这是为什么呢？`MySQL`其实默认会按照这些数据底层存储的顺序来给我们返回数据，但是这些数据可能会经过更新或者删除，如果我们不明确指定按照什么顺序来排序返回结果的话，那我们可以认为该结果中记录的顺序是不确定的。换句话说如果我们想让返回结果中的记录按照某种特定的规则排序，那我们必须显式的指定排序规则

#### 按照单个列的值进行排序

我们可以用下边的语法来指定返回结果的记录按照某一列的值进行排序：

```sql
ORDER BY 列名 ASC|DESC
```

`ASC`和`DESC`指的是排序方向。`ASC`是指按照指定列的值进行由小到大进行排序，也叫做`升序`，`DESC`是指按照指定列的值进行由大到小进行排序，也叫做`降序`，中间的`|`表示这两种方式只能选一个。这回我们用`student_score`表测试一下：

```sql
mysql> SELECT * FROM student_score ORDER BY score ASC;
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180104 | 论萨达姆的战争准备          |    46 |
| 20180104 | 母猪的产后护理              |    55 |
| 20180103 | 母猪的产后护理              |    59 |
| 20180103 | 论萨达姆的战争准备          |    61 |
| 20180101 | 母猪的产后护理              |    78 |
| 20180101 | 论萨达姆的战争准备          |    88 |
| 20180102 | 论萨达姆的战争准备          |    98 |
| 20180102 | 母猪的产后护理              |   100 |
+----------+-----------------------------+-------+
8 rows in set (0.01 sec)

mysql>
```

可以看到输出的记录就是按照成绩由小到大进行排序的。如果省略了 ORDER BY 语句中的排序方向，则默认按照从小到大的顺序进行排序，也就是说`ORDER BY 列名`和`ORDER BY 列名 ASC`的语义是一样的，我们试一下：

```sql
mysql> SELECT * FROM student_score ORDER BY score;
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180104 | 论萨达姆的战争准备          |    46 |
| 20180104 | 母猪的产后护理              |    55 |
| 20180103 | 母猪的产后护理              |    59 |
| 20180103 | 论萨达姆的战争准备          |    61 |
| 20180101 | 母猪的产后护理              |    78 |
| 20180101 | 论萨达姆的战争准备          |    88 |
| 20180102 | 论萨达姆的战争准备          |    98 |
| 20180102 | 母猪的产后护理              |   100 |
+----------+-----------------------------+-------+
8 rows in set (0.01 sec)
```

再看一下从大到小排序的样子：

```sql
mysql> SELECT * FROM student_score ORDER BY score DESC;
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
| 20180101 | 论萨达姆的战争准备          |    88 |
| 20180101 | 母猪的产后护理              |    78 |
| 20180103 | 论萨达姆的战争准备          |    61 |
| 20180103 | 母猪的产后护理              |    59 |
| 20180104 | 母猪的产后护理              |    55 |
| 20180104 | 论萨达姆的战争准备          |    46 |
+----------+-----------------------------+-------+
8 rows in set (0.00 sec)

mysql>
```

#### 按照多个列的值进行排序

我们也可以同时指定多个排序的列，多个排序列之间用逗号`,`隔开就好了，就是这样：

```sql
ORDER BY 列1 ASC|DESC, 列2 ASC|DESC ...
```

比如我们想让对`student_score`的查询结果先按照`subjuect`排序，再按照`score`值从大到小的顺序进行排列，可以这么写：

```sql
mysql> SELECT * FROM student_score ORDER BY subject, score DESC;
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 母猪的产后护理              |   100 |
| 20180101 | 母猪的产后护理              |    78 |
| 20180103 | 母猪的产后护理              |    59 |
| 20180104 | 母猪的产后护理              |    55 |
| 20180102 | 论萨达姆的战争准备          |    98 |
| 20180101 | 论萨达姆的战争准备          |    88 |
| 20180103 | 论萨达姆的战争准备          |    61 |
| 20180104 | 论萨达姆的战争准备          |    46 |
+----------+-----------------------------+-------+
8 rows in set (0.00 sec)

mysql>
```

再提醒一遍，如果不指定排序方向，则默认使用的是`ASC`，也就是从小到大的升序规则。

```!
小贴士：

对于数字的排序还是很好理解的，但是字符串怎么排序呢？大写的A和小写的a哪个大哪个小？这个问题涉及到字符串使用的编码方式以及字符串排序规则，我们之后会详细的介绍它们，现在你只需要知道排序的语法就好了。
```

我们还可以让`ORDER BY`语句和`LIMIT`语句结合使用，不过 ORDER BY 语句必须放在 LIMIT 语句前边，比如这样：

```sql
mysql> SELECT * FROM student_score ORDER BY score LIMIT 1;
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180104 | 论萨达姆的战争准备          |    46 |
+----------+-----------------------------+-------+
1 row in set (0.00 sec)

mysql>
```

这样就能找出成绩最低的那条记录了。

## 带搜索条件的查询

我们上边介绍的`student_info`、`student_score`表中的记录都很少，但是实际应用中的表里可能存储几千万条，甚至上亿条记录。而且我们通常并不是对所有的记录都感兴趣，只是想查询到符合某些条件的那些记录。比如我们只想查询名字为`范剑`的学生基本信息，或者`计算机学院`的学生都有哪些什么的，这些条件也被称为`搜索条件`或者`过滤条件`，当某条记录符合`搜索条件`时，它将被放入结果集中

### 简单搜索条件

我们需要把`搜索条件`放在`WHERE`子句中，比如我们想查询`student_info`表中名字是`范剑`的学生的一些信息，可以这么写：

```sql
mysql> SELECT number, name, id_number, major FROM student_info WHERE name = '范剑';
+----------+--------+--------------------+-----------------+
| number   | name   | id_number          | major           |
+----------+--------+--------------------+-----------------+
| 20180105 | 范剑   | 181048199308156368 | 飞行器设计      |
+----------+--------+--------------------+-----------------+
1 row in set (0.01 sec)
```

这个例子中的`搜索条件`就是`name = '范剑'`，也就是当记录中的`name`列的值是`'范剑'`的时候，该条记录的`number`、`name`、`id_number`、`major`这些字段才可以被放入结果集。搜索条件`name = '范剑'`中的`=`称之为`比较操作符`，除了`=`之外，设计`MySQL`的大叔还提供了很多别的比较操作符，比如：

| 操作符        | 示例                    | 描述               |
| ------------- | ----------------------- | ------------------ |
| `=`           | `a = b`                 | a等于b             |
| `<>`或者`！=` | `a <> b`                | a不等于b           |
| `<`           | `a < b`                 | a小于b             |
| `<=`          | `a <= b`                | a小于或等于b       |
| `>`           | `a > b`                 | a大于b             |
| `>=`          | `a >= b`                | a大于或等于b       |
| `BETWEEN`     | `a BETWEEN b AND c`     | 满足 b <= a <= c   |
| `NOT BETWEEN` | `a NOT BETWEEN b AND c` | 不满足 b <= a <= c |



通过这些比较操作符可以组成搜索条件，满足搜索条件的记录将会被放入结果集中。下边我们举几个例子：

- 查询学号大于`20180103`的学生信息可以这么写：

  ```sql
  mysql> SELECT number, name, id_number, major FROM student_info WHERE number > 20180103;
  +----------+-----------+--------------------+-----------------+
  | number   | name      | id_number          | major           |
  +----------+-----------+--------------------+-----------------+
  | 20180104 | 史珍香    | 141992199701078600 | 软件工程        |
  | 20180105 | 范剑      | 181048199308156368 | 飞行器设计      |
  | 20180106 | 朱逸群    | 197995199501078445 | 电子信息        |
  +----------+-----------+--------------------+-----------------+
  3 rows in set (0.01 sec)
  
  mysql>
  ```

- 查询专业不是`计算机科学与工程`的一些学生信息可以这么写：

  ```sql
  mysql> SELECT number, name, id_number, major FROM student_info WHERE major != '计算机科学与工程';
  +----------+-----------+--------------------+-----------------+
  | number   | name      | id_number          | major           |
  +----------+-----------+--------------------+-----------------+
  | 20180103 | 范统      | 17156319980116959X | 软件工程        |
  | 20180104 | 史珍香    | 141992199701078600 | 软件工程        |
  | 20180105 | 范剑      | 181048199308156368 | 飞行器设计      |
  | 20180106 | 朱逸群    | 197995199501078445 | 电子信息        |
  +----------+-----------+--------------------+-----------------+
  4 rows in set (0.00 sec)
  
  mysql>
  ```

- 查询学号在`20180102`~`20180104`间的学生信息，可以这么写：

  ```sql
  mysql> SELECT number, name, id_number, major FROM student_info WHERE number BETWEEN 20180102 AND 20180104;
  +----------+-----------+--------------------+--------------------------+
  | number   | name      | id_number          | major                    |
  +----------+-----------+--------------------+--------------------------+
  | 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
  | 20180103 | 范统      | 17156319980116959X | 软件工程                 |
  | 20180104 | 史珍香    | 141992199701078600 | 软件工程                 |
  +----------+-----------+--------------------+--------------------------+
  3 rows in set (0.00 sec)
  
  mysql>
  ```

- 查询学号不在`20180102`~`20180104`这个区间内的所有学生信息，可以这么写：

  ```sql
  mysql> SELECT number, name, id_number, major FROM student_info WHERE number NOT BETWEEN 20180102 AND 20180104;
  +----------+-----------+--------------------+--------------------------+
  | number   | name      | id_number          | major                    |
  +----------+-----------+--------------------+--------------------------+
  | 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
  | 20180105 | 范剑      | 181048199308156368 | 飞行器设计               |
  | 20180106 | 朱逸群    | 197995199501078445 | 电子信息                 |
  +----------+-----------+--------------------+--------------------------+
  3 rows in set (0.00 sec)
  ```

### 匹配列表中的元素

有时候搜索条件中指定的匹配值并不是单个值，而是一个列表，只要匹配到列表中的某一项就算匹配成功，这种情况可以使用`IN`操作符：

| 操作符   | 示例                     | 描述                          |
| -------- | ------------------------ | ----------------------------- |
| `IN`     | `a IN (b1, b2, ...)`     | a是b1, b2, ... 中的某一个     |
| `NOT IN` | `a NOT IN (b1, b2, ...)` | a不是b1, b2, ... 中的任意一个 |



比如我们想查询`软件工程`和`飞行器设计`专业的学生信息，可以这么写：

```sql
mysql> SELECT number, name, id_number, major FROM student_info WHERE major IN ('软件工程', '飞行器设计');
+----------+-----------+--------------------+-----------------+
| number   | name      | id_number          | major           |
+----------+-----------+--------------------+-----------------+
| 20180103 | 范统      | 17156319980116959X | 软件工程        |
| 20180104 | 史珍香    | 141992199701078600 | 软件工程        |
| 20180105 | 范剑      | 181048199308156368 | 飞行器设计      |
+----------+-----------+--------------------+-----------------+
3 rows in set (0.01 sec)

mysql>
```

如果想查询不是这两个专业的学生的信息，可以这么写：

```sql
mysql> SELECT number, name, id_number, major FROM student_info WHERE major NOT IN ('软件工程', '飞行器设计');
+----------+-----------+--------------------+--------------------------+
| number   | name      | id_number          | major                    |
+----------+-----------+--------------------+--------------------------+
| 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
| 20180106 | 朱逸群    | 197995199501078445 | 电子信息                 |
+----------+-----------+--------------------+--------------------------+
3 rows in set (0.00 sec)

mysql>
```

### 匹配`NULL`值

我们前边说过，`NULL`代表没有值，意味着你并不知道该列应该填入什么数据，在判断某一列是否为`NULL`的时候并不能单纯的使用`=`操作符，而是需要专业判断值是否是`NULL`的操作符：

| 操作符        | 示例            | 描述            |
| ------------- | --------------- | --------------- |
| `IS NULL`     | `a IS NULL`     | a的值是`NULL`   |
| `IS NOT NULL` | `a IS NOT NULL` | a的值不是`NULL` |



比如我们想看一下`student_info`表的`name`列是`NULL`的学生记录有哪些，可以这么写：

```sql
mysql> SELECT number, name, id_number, major FROM student_info WHERE name IS NULL;
Empty set (0.00 sec)

mysql>
```

由于所有记录的`name`列都不是`NULL`值，所以最后的结果集是空的，我们看一下查询`name`列不是`NULL`值的方式：

```sql
mysql> SELECT number, name, id_number, major FROM student_info WHERE name IS NOT NULL;
+----------+-----------+--------------------+--------------------------+
| number   | name      | id_number          | major                    |
+----------+-----------+--------------------+--------------------------+
| 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
| 20180103 | 范统      | 17156319980116959X | 软件工程                 |
| 20180104 | 史珍香    | 141992199701078600 | 软件工程                 |
| 20180105 | 范剑      | 181048199308156368 | 飞行器设计               |
| 20180106 | 朱逸群    | 197995199501078445 | 电子信息                 |
+----------+-----------+--------------------+--------------------------+
6 rows in set (0.00 sec)

mysql>
```

`name`列不是`NULL`值的记录就被查询出来啦！

再次强调一遍，不能直接使用普通的操作符来与`NULL`值进行比较，必须使用`IS NULL`或者`IS NOT NULL`！

### 多个搜索条件的查询

上边介绍的都是指定单个的搜索条件的查询，我们也可以在一个查询语句中指定多个搜索条件。

#### AND操作符

在给定多个搜索条件的时候，我们有时需要某条记录只在符合所有搜索条件的时候才将其加入结果集，这种情况我们可以使用`AND`操作符来连接多个搜索条件。比如我们想从`student_score`表中找出科目为`'母猪的产后护理'`并且成绩大于`75`分的记录，可以这么写：

```sql
mysql> SELECT * FROM student_score WHERE subject = '母猪的产后护理' AND score > 75;
+----------+-----------------------+-------+
| number   | subject               | score |
+----------+-----------------------+-------+
| 20180101 | 母猪的产后护理        |    78 |
| 20180102 | 母猪的产后护理        |   100 |
+----------+-----------------------+-------+
2 rows in set (0.00 sec)

mysql>
```

其中的`subject = '母猪的产后护理'`和`score > 75`是两个搜索条件，我们使用`AND`操作符把这两个搜索条件连接起来表示只有当两个条件都满足的记录才能被加入到结果集。

#### OR操作符

在给定多个搜索条件的时候，我们有时需要某条记录在符合某一个搜索条件的时候就将其加入结果集中，这种情况我们可以使用`OR`操作符来连接多个搜索条件。比如我们想从`student_score`表中找出成绩大于`95`分或者小于`55`分的记录，可以这么写：

```sql
mysql> SELECT * FROM student_score WHERE score > 95 OR score < 55;
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
| 20180104 | 论萨达姆的战争准备          |    46 |
+----------+-----------------------------+-------+
3 rows in set (0.00 sec)

mysql>
```

#### 更复杂的搜索条件的组合

如果我们需要在某个查询中指定很多的搜索条件，比方说我们想从`student_score`表中找出课程为`'论萨达姆的战争准备'`，并且成绩大于`95`分或者小于`55`分的记录，那我们可能会这么写：

```sql
mysql> SELECT * FROM student_score WHERE score > 95 OR score < 55 AND subject = '论萨达姆的战争准备';
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
| 20180104 | 论萨达姆的战争准备          |    46 |
+----------+-----------------------------+-------+
3 rows in set (0.00 sec)

mysql>
```

为什么结果中仍然会有`'母猪的产后护理'`课程的记录呢？因为：AND操作符的优先级高于OR操作符，也就是说在判断某条记录是否符合条件时会先检测AND操作符两边的搜索条件。所以

```ini
score > 95 OR score < 55 AND subject = '论萨达姆的战争准备'
```

可以被看作下边这两个条件中任一条件成立则整个式子成立：

1. `score > 95`
2. `score < 55 AND subject = '论萨达姆的战争准备'`

因为结果集中`subject`是`'母猪的产后护理'`的记录中`score`值为`100`，符合第1个条件，所以整条记录会被加到结果集中。为了避免这种尴尬，在一个查询中有多个搜索条件时最好使用小括号`()`来显式的指定各个搜索条件的检测顺序，比如上边的例子可以写成下边这样：

```sql
mysql> SELECT * FROM student_score WHERE (score > 95 OR score < 55) AND subject = '论萨达姆的战争准备';
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 论萨达姆的战争准备          |    98 |
| 20180104 | 论萨达姆的战争准备          |    46 |
+----------+-----------------------------+-------+
2 rows in set (0.00 sec)

mysql>
```

### 通配符

有时候我们并不能精确的描述我们要查询的哪些结果，比方说我们只是想看看姓`'杜'`的学生信息，而不能精确的描述出这些姓`'杜'`的同学的完整姓名，我们称这种查询为`模糊查询`。`MySQL`中使用下边这两个操作符来支持`模糊查询`：

| 操作符     | 示例           | 描述     |
| ---------- | -------------- | -------- |
| `LIKE`     | `a LIKE b`     | a匹配b   |
| `NOT LIKE` | `a NOT LIKE b` | a不匹配b |



既然我们不能完整描述要查询的信息，那就用某个符号来替代这些模糊的信息，这个符号就被称为`通配符`。`MySQL`中支持下边这两个`通配符`：

1. `%`：代表任意一个字符串。

   比方说我们想查询`student_info`表中`name`以`'杜'`开头的记录，我们可以这样写：

   ```sql
   mysql> SELECT number, name, id_number, major FROM student_info WHERE name LIKE '杜%';
   +----------+-----------+--------------------+--------------------------+
   | number   | name      | id_number          | major                    |
   +----------+-----------+--------------------+--------------------------+
   | 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
   | 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
   +----------+-----------+--------------------+--------------------------+
   2 rows in set (0.00 sec)
   
   mysql>
   ```

   或者我们只知道学生名字里边包含了一个`'香'`字，那我们可以这么查：

   ```sql
   mysql> SELECT number, name, id_number, major FROM student_info WHERE name LIKE '%香%';
   +----------+-----------+--------------------+--------------+
   | number   | name      | id_number          | major        |
   +----------+-----------+--------------------+--------------+
   | 20180104 | 史珍香    | 141992199701078600 | 软件工程     |
   +----------+-----------+--------------------+--------------+
   1 row in set (0.00 sec)
   
   mysql>
   ```

2. `_`：代表任意一个字符。

   有的时候我们知道要查询的字符串中有多少个字符，而使用`%`时匹配的范围太大，我们就可以用`_`来做通配符。就像是支付宝的万能福卡，一张万能福卡能且只能代表任意一张福卡(也就是它不能代表多张福卡)。

   比方说我们想查询姓`'范'`，并且姓名只有2个字符的记录，可以这么写：

   ```sql
   mysql> SELECT number, name, id_number, major FROM student_info WHERE name LIKE '范_';
   +----------+--------+--------------------+-----------------+
   | number   | name   | id_number          | major           |
   +----------+--------+--------------------+-----------------+
   | 20180103 | 范统   | 17156319980116959X | 软件工程        |
   | 20180105 | 范剑   | 181048199308156368 | 飞行器设计      |
   +----------+--------+--------------------+-----------------+
   2 rows in set (0.00 sec)
   
   mysql>
   ```

   不过下边这个查询却什么都没有查到：

   ```sql
   mysql> SELECT number, name, id_number, major FROM student_info WHERE name LIKE '杜_';
   Empty set (0.00 sec)
   
   mysql>
   ```

   这是因为一个`_`只能代表一个字符（`%`是代表任意一个字符串），并且`student_info`表中并没有姓`'杜'`并且姓名长度是2个字符的记录，所以这么写是查不出东西的。

   ```!
   小贴士：
   
   LIKE或者NOT LIKE操作符只用于字符串匹配。另外，通配符不能代表NULL，如果需要匹配NULL的话，需要使用IS NULL或者IS NOT NULL。
   ```

#### 转义通配符

如果待匹配的字符串中本身就包含普通字符`'%'`或者`'_'`该咋办，怎么区分它是一个通配符还是一个普通字符呢？

答：如果匹配字符串中需要普通字符`'%'`或者`'_'`的话，需要在它们前边加一个反斜杠`\`来和通配符区分开来，也就是说：

- `'\%'`代表普通字符`'%'`
- `'\_'`代表普通字符`'_'` 比方说这样：

```sql
mysql> SELECT number, name, id_number, major FROM student_info WHERE name LIKE '范\_';
Empty set (0.00 sec)
    
mysql>
```

由于`student_info`表中没有叫`范_`的学生，所以查询结果为空。

## 表达式和函数

### 表达式

学过小学数学的我们应该知道，将数字和运算符连接起来的组合称之为`表达式`，比方说这样：

```
1 + 1
5 * 8
```

我们可以将其中的数字称之为`操作数`，运算符可以称之为`操作符`。特殊的，单个操作数也可以被看作是一个特殊的表达式。

在`MySQL`中也有表达式的概念，不过`操作数`和`操作符`的含义有了扩充。下边详细看一下。

#### 操作数

`MySQL`中`操作数`可以是下边这几种类型：

1. 常数

   常数很好理解，我们平时用到的数字、字符串、时间值什么的都可以被称为常数，它是一个确定的值，比如数字`1`，字符串`'abc'`，时间值`2019-08-16 17:10:43`啥的。

2. 列名

   针对某个具体的表，它的列名可以被当作表达式的一部分，比如对于`student_info`表来说，`number`、`name`都可以作为`操作数`。

3. 函数调用

   `MySQL`中有`函数`的概念，比方说获取当前时间的函数`NOW`，而在函数后边加个小括号就算是一个`函数调用`，比如`NOW()`。

   ```
   如果你不清楚函数的概念，我们之后会详细唠叨的，现在不知道也可以～
   ```

4. 标量子查询或者行子查询

   这个子查询我们稍后会详细唠叨的～

5. 其他表达式

   一个表达式也可以作为一个操作数与另一个操作数来形成一个更复杂的表达式，比方说（假设`col`是一个列名）：

   - (col - 5) / 3
   - (1 + 1) * 2 + col * 3

```!
小贴士：

当然，可以作为操作数的东西不止这么几种，不过我们这是一个入门书籍，大家在熟练使用MySQL后再到文档中查看更多的操作数类型吧。
```

#### 操作符

对于小白的我们来说，目前熟悉掌握下边三种操作符就应该够用了：

1. 算术操作符

   就是加减乘除法那一堆，我们看一下`MySQL`中都支持哪些：

   | 操作符 | 示例      | 描述                 |
   | ------ | --------- | -------------------- |
   | `+`    | `a + b`   | 加法                 |
   | `-`    | `a - b`   | 减法                 |
   | `*`    | `a * b`   | 乘法                 |
   | `/`    | `a / b`   | 除法                 |
   | `DIV`  | `a DIV b` | 除法，取商的整数部分 |
   | `%`    | `a % b`   | 取余                 |
   | `-`    | `-a`      | 负号                 |

   在使用`MySQL`中的`算术操作符`时需要注意，`DIV`和`/`都表示除法操作符，但是`DIV`只会取商的整数部分，`/`会保留商的小数部分。比如表达式 `2 DIV 3`的结果是`0`，而`2 / 3`的结果是`0.6667`。

2. 比较操作符

   就是在`搜索条件`中我们已经看过的`比较操作符`，我们把常用的都抄下来看一下：

   | 操作符        | 示例                     | 描述                          |
   | ------------- | ------------------------ | ----------------------------- |
   | `=`           | `a = b`                  | a等于b                        |
   | `<>`或者`!=`  | `a <> b`                 | a不等于b                      |
   | `<`           | `a < b`                  | a小于b                        |
   | `<=`          | `a <= b`                 | a小于或等于b                  |
   | `>`           | `a > b`                  | a大于b                        |
   | `>=`          | `a >= b`                 | a大于或等于b                  |
   | `BETWEEN`     | `a BETWEEN b AND c`      | 满足 b <= a <= c              |
   | `NOT BETWEEN` | `a NOT BETWEEN b AND c`  | 不满足 b <= a <= c            |
   | `IN`          | `a IN (b1, b2, ...)`     | a是b1, b2, ... 中的某一个     |
   | `NOT IN`      | `a NOT IN (b1, b2, ...)` | a不是b1, b2, ... 中的任意一个 |
   | `IS NULL`     | `a IS NULL`              | a的值是`NULL`                 |
   | `IS NOT NULL` | `a IS NOT NULL`          | a的值不是`NULL`               |
   | `LIKE`        | `a LIKE b`               | a匹配b                        |
   | `NOT LIKE`    | `a NOT LIKE b`           | a不匹配b                      |

   由`比较操作符`连接而成的表达式也称为`布尔表达式`，表示`真`或者`假`，也可以称为`TRUE`或者`FALSE`。比如`1 > 3`就代表`FALSE`，`3 != 2`就代表`TRUE`。

3. 逻辑操作符

   逻辑操作符是用来将多个`布尔表达式`连接起来，我们需要了解这几个`逻辑操作符`：

   | 操作符 | 示例      | 描述                                 |
   | ------ | --------- | ------------------------------------ |
   | `AND`  | `a AND b` | 只有a和b同时为真，表达式才为真       |
   | `OR`   | `a OR b`  | 只要a或b有任意一个为真，表达式就为真 |
   | `XOR`  | `a XOR b` | a和b有且只有一个为真，表达式为真     |

#### 表达式的使用

只要把这些`操作数`和`操作符`相互组合起来就可以组成一个`表达式`。`表达式`主要以下边这两种方式使用：

1. 放在查询列表中

   我们前边都是将列名放在查询列表中的(`*`号代表所有的列名～)。列名只是`表达式`中超级简单的一种，我们可以将任意一个表达式作为查询列表的一部分来处理，比方说我们可以在查询`student_score`表时把`score`字段的数据都加`100`，就像这样：

   ```sql
   mysql> SELECT  number, subject, score + 100 FROM student_score;
   +----------+-----------------------------+-------------+
   | number   | subject                     | score + 100 |
   +----------+-----------------------------+-------------+
   | 20180101 | 母猪的产后护理              |         178 |
   | 20180101 | 论萨达姆的战争准备          |         188 |
   | 20180102 | 母猪的产后护理              |         200 |
   | 20180102 | 论萨达姆的战争准备          |         198 |
   | 20180103 | 母猪的产后护理              |         159 |
   | 20180103 | 论萨达姆的战争准备          |         161 |
   | 20180104 | 母猪的产后护理              |         155 |
   | 20180104 | 论萨达姆的战争准备          |         146 |
   +----------+-----------------------------+-------------+
   8 rows in set (0.00 sec)
   
   mysql>
   ```

   其中的`number`、`subject`、`score + 100`都是表达式，结果集中的列的名称也将默认使用这些表达式的名称，所以如果你觉得原名称不好，我们可以使用别名：

   ```sql
   mysql> SELECT  number, subject, score + 100 AS score FROM student_score;
   +----------+-----------------------------+-------+
   | number   | subject                     | score |
   +----------+-----------------------------+-------+
   | 20180101 | 母猪的产后护理              |   178 |
   | 20180101 | 论萨达姆的战争准备          |   188 |
   | 20180102 | 母猪的产后护理              |   200 |
   | 20180102 | 论萨达姆的战争准备          |   198 |
   | 20180103 | 母猪的产后护理              |   159 |
   | 20180103 | 论萨达姆的战争准备          |   161 |
   | 20180104 | 母猪的产后护理              |   155 |
   | 20180104 | 论萨达姆的战争准备          |   146 |
   +----------+-----------------------------+-------+
   8 rows in set (0.00 sec)
   
   mysql>
   ```

   这样`score + 100`列就可以按照别名`score`来展示了！

   需要注意的是，放在查询列表的表达式也可以不涉及列名，就像这样：

   ```sql
   mysql> SELECT 1 FROM student_info;
   +---+
   | 1 |
   +---+
   | 1 |
   | 1 |
   | 1 |
   | 1 |
   | 1 |
   | 1 |
   +---+
   6 rows in set (0.01 sec)
   
   mysql>
   ```

   因为`student_info`中有6条记录，所以结果集中也就展示了6条结果，不过我们的查询列表处只有一个常数`1`，所以所有的结果的值也都是常数`1`。这种查询列表中不涉及列名的情况下，我们甚至可以省略掉`FROM`子句后边的表名，就像这样：

   ```sql
   mysql> SELECT 1;
   +---+
   | 1 |
   +---+
   | 1 |
   +---+
   1 row in set (0.00 sec)
   
   mysql>
   ```

   可是这么写有什么现实用处么？好像有的，可以做个计算器[偷笑]～

2. 作为搜索条件

   我们在介绍搜索条件的时候介绍的都是带有列名的表达式，搜索条件也可以不带列名，比如这样：

   ```sql
   mysql> SELECT number, name, id_number, major FROM student_info WHERE 2 > 1;
   +----------+-----------+--------------------+--------------------------+
   | number   | name      | id_number          | major                    |
   +----------+-----------+--------------------+--------------------------+
   | 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
   | 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
   | 20180103 | 范统      | 17156319980116959X | 软件工程                 |
   | 20180104 | 史珍香    | 141992199701078600 | 软件工程                 |
   | 20180105 | 范剑      | 181048199308156368 | 飞行器设计               |
   | 20180106 | 朱逸群    | 197995199501078445 | 电子信息                 |
   +----------+-----------+--------------------+--------------------------+
   6 rows in set (0.00 sec)
   
   mysql>
   ```

   由于我们的搜索条件是`2 > 1`，这个条件对于表中的每一条记录都成立，所以最后的查询结果就是全部的记录。不过这么写有点儿傻哈，没有一毛钱卵用，没一点实际意义～ 所以通常情况下搜索条件中都会包含列名的。

### 函数

我们在使用`MySQL`过程中经常会有一些需求，比方说将给定文本中的小写字母转换成大写字母，把某个日期数据中的月份值提取出来等等。为了解决这些常遇到的问题，设计`MySQL`的大叔贴心的为我们提供了很多所谓的`函数`，比方说：

- `UPPER`函数是用来把给定的文本中的小写字母转换成大写字母。
- `MONTH`函数是用来把某个日期数据中的月份值提取出来。
- `NOW`函数用来获取当前的日期和时间。

如果我们想使用这些函数，可以在函数名后加一个小括号`()`就好，表示调用一下这个函数，简称`函数调用`。比方说`NOW()`就代表调用`NOW`函数来获取当前日期和时间。针对某些包含参数的函数，我们也可以在小括号`()`里将参数填入，比方说`UPPER('abc')`表示将字符串`'abc'`转换为大写格式。

下边来介绍一些常用的`MySQL`内置函数：

#### 文本处理函数

| 名称        | 调用示例                      | 示例结果    | 描述                                   |
| ----------- | ----------------------------- | ----------- | -------------------------------------- |
| `LEFT`      | `LEFT('abc123', 3)`           | `abc`       | 给定字符串从左边取指定长度的子串       |
| `RIGHT`     | `RIGHT('abc123', 3)`          | `123`       | 给定字符串从右边取指定长度的子串       |
| `LENGTH`    | `LENGTH('abc')`               | `3`         | 给定字符串的长度                       |
| `LOWER`     | `LOWER('ABC')`                | `abc`       | 给定字符串的小写格式                   |
| `UPPER`     | `UPPER('abc')`                | `ABC`       | 给定字符串的大写格式                   |
| `LTRIM`     | `LTRIM(' abc')`               | `abc`       | 给定字符串左边空格去除后的格式         |
| `RTRIM`     | `RTRIM('abc ')`               | `abc`       | 给定字符串右边空格去除后的格式         |
| `SUBSTRING` | `SUBSTRING('abc123', 2, 3)`   | `bc1`       | 给定字符串从指定位置截取指定长度的子串 |
| `CONCAT`    | `CONCAT('abc', '123', 'xyz')` | `abc123xyz` | 将给定的各个字符串拼接成一个新字符串   |



我们以`SUBSTRING`函数为例试一下：

```sql
mysql> SELECT SUBSTRING('abc123', 2, 3);
+---------------------------+
| SUBSTRING('abc123', 2, 3) |
+---------------------------+
| bc1                       |
+---------------------------+
1 row in set (0.00 sec)

mysql>
```

我们前边在唠叨`表达式`的说过，`函数调用`也算是一种表达式的`操作数`，它可以和其他操作数用操作符连接起来组成一个表达式来作为查询列表的一部分或者放到搜索条件中。我们来以`CONCAT`函数为例来看一下：

```sql
mysql> SELECT CONCAT('学号为', number, '的学生在《', subject, '》课程的成绩是：', score) AS 成绩描述 FROM student_score;
+---------------------------------------------------------------------------------------+
| 成绩描述                                                                              |
+---------------------------------------------------------------------------------------+
| 学号为20180101的学生在《母猪的产后护理》课程的成绩是：78                              |
| 学号为20180101的学生在《论萨达姆的战争准备》课程的成绩是：88                          |
| 学号为20180102的学生在《母猪的产后护理》课程的成绩是：100                             |
| 学号为20180102的学生在《论萨达姆的战争准备》课程的成绩是：98                          |
| 学号为20180103的学生在《母猪的产后护理》课程的成绩是：59                              |
| 学号为20180103的学生在《论萨达姆的战争准备》课程的成绩是：61                          |
| 学号为20180104的学生在《母猪的产后护理》课程的成绩是：55                              |
| 学号为20180104的学生在《论萨达姆的战争准备》课程的成绩是：46                          |
+---------------------------------------------------------------------------------------+
8 rows in set (0.00 sec)

mysql>
```

#### 日期和时间处理函数

下边有些函数会用到当前日期，我编辑文章的日期是`2019-08-28`，在实际调用这些函数时以你的当前时间为准。

| 名称          | 调用示例                                          | 示例结果              | 描述                                                         |
| ------------- | ------------------------------------------------- | --------------------- | ------------------------------------------------------------ |
| `NOW`         | `NOW()`                                           | `2019-08-16 17:10:43` | 返回当前日期和时间                                           |
| `CURDATE`     | `CURDATE()`                                       | `2019-08-16`          | 返回当前日期                                                 |
| `CURTIME`     | `CURTIME()`                                       | `17:10:43`            | 返回当前时间                                                 |
| `DATE`        | `DATE('2019-08-16 17:10:43')`                     | `2019-08-16`          | 将给定日期和时间值的日期提取出来                             |
| `DATE_ADD`    | `DATE_ADD('2019-08-16 17:10:43', INTERVAL 2 DAY)` | `2019-08-18 17:10:43` | 将给定的日期和时间值添加指定的时间间隔                       |
| `DATE_SUB`    | `DATE_SUB('2019-08-16 17:10:43', INTERVAL 2 DAY)` | `2019-08-14 17:10:43` | 将给定的日期和时间值减去指定的时间间隔                       |
| `DATEDIFF`    | `DATEDIFF('2019-08-16', '2019-08-17');`           | `-1`                  | 返回两个日期之间的天数（负数代表前一个参数代表的日期比较小） |
| `DATE_FORMAT` | `DATE_FORMAT(NOW(),'%m-%d-%Y')`                   | `08-16-2019`          | 用给定的格式显示日期和时间                                   |



在使用这些函数时需要注意一些地方：

- 在使用`DATE_ADD`和`DATE_SUB`这两个函数时需要注意，增加或减去的时间间隔单位可以自己定义，下边是`MySQL`支持的一些时间单位：

  | 时间单位      | 描述 |
  | ------------- | ---- |
  | `MICROSECOND` | 毫秒 |
  | `SECOND`      | 秒   |
  | `MINUTE`      | 分钟 |
  | `HOUR`        | 小时 |
  | `DAY`         | 天   |
  | `WEEK`        | 星期 |
  | `MONTH`       | 月   |
  | `QUARTER`     | 季度 |
  | `YEAR`        | 年   |

  如果我们相让`2019-08-16 17:10:43`这个时间值增加2分钟，可以这么写：

  ```sql
  mysql> SELECT DATE_ADD('2019-08-16 17:10:43', INTERVAL 2 MINUTE);
  +----------------------------------------------------+
  | DATE_ADD('2019-08-16 17:10:43', INTERVAL 2 MINUTE) |
  +----------------------------------------------------+
  | 2019-08-16 17:12:43                                |
  +----------------------------------------------------+
  1 row in set (0.00 sec)
  
  mysql>
  ```

- 在使用`DATE_FORMAT`函数时需要注意，我们可以通过一些所谓的`格式符`来自定义日期和时间的显示格式，下边是`MySQL`中常用的一些日期和时间的格式符以及它们对应的含义：

  | 格式符 | 描述                                                    |
  | ------ | ------------------------------------------------------- |
  | `%b`   | 简写的月份名称（Jan、Feb、...、Dec)                     |
  | `%D`   | 带有英文后缀的月份中的日期（0th、1st、2nd、...、31st)） |
  | `%d`   | 数字格式的月份中的日期(00、01、02、...、31)             |
  | `%f`   | 微秒（000000-999999）                                   |
  | `%H`   | 二十四小时制的小时 (00-23)                              |
  | `%h`   | 十二小时制的小时 (01-12)                                |
  | `%i`   | 数值格式的分钟(00-59)                                   |
  | `%M`   | 月份名（January、February、...、December）              |
  | `%m`   | 数值形式的月份(00-12)                                   |
  | `%p`   | 上午或下午（AM代表上午、PM代表下午）                    |
  | `%S`   | 秒(00-59)                                               |
  | `%s`   | 秒(00-59)                                               |
  | `%W`   | 星期名（Sunday、Monday、...、Saturday）                 |
  | `%w`   | 周内第几天 （0=星期日、1=星期一、 6=星期六）            |
  | `%Y`   | 4位数字形式的年（例如2019）                             |
  | `%y`   | 2位数字形式的年（例如19）                               |

  我们可以把我们想要的显示格式用对应的格式符描述出来，就像这样：

  ```sql
  mysql> SELECT DATE_FORMAT(NOW(),'%b %d %Y %h:%i %p');
  +----------------------------------------+
  | DATE_FORMAT(NOW(),'%b %d %Y %h:%i %p') |
  +----------------------------------------+
  | Aug 16 2019 05:10 PM                   |
  +----------------------------------------+
  1 row in set (0.00 sec)
  
  mysql>
  ```

  `'%b %d %Y %h:%i %p'`就是一个用格式符描述的显示格式，意味着对应的日期和时间应该以下边描述的方式展示：

  - 先输出简写的月份名称（格式符`%b`），也就是示例中的`Aug`，然后输出一个空格。
  - 再输出用数字格式表示的的月份中的日期（格式符`%d`），也就是示例中的`16`，然后输出一个空格。
  - 再输出4位数字形式的年（格式符`%Y`），也就是示例中的`2019`，然后输出一个空格。
  - 再输出十二小时制的小时（格式符`%h`），也就是示例中的`05`，然后输出一个冒号`:`。
  - 再输出数值格式的分钟（格式符`%i`），也就是示例中的`10`，然后输出一个空格。
  - 最后输出上午或者下午（格式符`%p`），也就是示例中的`PM`。

#### 数值处理函数

下边列举一些数学上常用到的函数，在遇到需要数学计算的业务时会很有用：

| 名称   | 调用示例      | 示例结果             | 描述               |
| ------ | ------------- | -------------------- | ------------------ |
| `ABS`  | `ABS(-1)`     | `1`                  | 取绝对值           |
| `Pi`   | `PI()`        | `3.141593`           | 返回圆周率         |
| `COS`  | `COS(PI())`   | `-1`                 | 返回一个角度的余弦 |
| `EXP`  | `EXP(1)`      | `2.718281828459045`  | 返回e的指定次方    |
| `MOD`  | `MOD(5,2)`    | `1`                  | 返回除法的余数     |
| `RAND` | `RAND()`      | `0.7537623539136372` | 返回一个随机数     |
| `SIN`  | `SIN(PI()/2)` | `1`                  | 返回一个角度的正弦 |
| `SQRT` | `SQRT(9)`     | `3`                  | 返回一个数的平方根 |
| `TAN`  | `TAN(0)`      | `0`                  | 返回一个角度的正切 |



#### 聚集函数

如果将上边介绍的那些函数以函数调用的形式放在查询列表中，那么会为表中符合`WHERE`条件的每一条记录调用一次该函数。比方说这样：

```sql
mysql> SELECT number, LEFT(name, 1) FROM student_info WHERE number < 20180106;
+----------+---------------+
| number   | LEFT(name, 1) |
+----------+---------------+
| 20180101 | 杜            |
| 20180102 | 杜            |
| 20180103 | 范            |
| 20180104 | 史            |
| 20180105 | 范            |
+----------+---------------+
5 rows in set (0.00 sec)

mysql>
```

`student_info`表中符合`number < 20180106`搜索条件的每一条记录的`name`字段会依次被当作`LEFT`函数的参数，结果就是把这些人的名字的首个字符给提取出来了。但是有些函数是用来统计数据的，比方说统计一下表中的行数，某一列数据的最大值是什么，我们把这种函数称之为`聚集函数`，下边介绍`MySQL`中常用的几种`聚集函数`：

| 函数名  | 描述             |
| ------- | ---------------- |
| `COUNT` | 返回某列的行数   |
| `MAX`   | 返回某列的最大值 |
| `MIN`   | 返回某列的最小值 |
| `SUM`   | 返回某列值之和   |
| `AVG`   | 返回某列的平均值 |



```!
小贴士：

聚集函数这个名儿不太直观，把它理解为统计函数可能更符合中国人的理解习惯。
```

##### COUNT函数

`COUNT`函数使用来统计行数的，它有下边两种使用方式：

1. `COUNT(*)`：对表中行的数目进行计数，不管列的值是不是`NULL`。
2. `COUNT(列名)`：对特定的列进行计数，会忽略掉该列为`NULL`的行。

两者的区别是会不会忽略统计列的值为NULL的行！两者的区别是会不会忽略统计列的值为NULL的行！两者的区别是会不会忽略统计列的值为NULL的行！重要的事情说了3遍，希望你能记住。我们来数一下`student_info`表中有几行记录吧：

```sql
mysql> SELECT COUNT(*) FROM student_info;
+----------+
| COUNT(*) |
+----------+
|        6 |
+----------+
1 row in set (0.00 sec)

mysql>
```

##### MAX函数

`MAX`函数是用来查询某列中数据的最大值，以`student_score`表中的`score`列为例来看一下：

```sql
mysql> SELECT MAX(score) FROM student_score;
+------------+
| MAX(score) |
+------------+
|        100 |
+------------+
1 row in set (0.00 sec)

mysql>
```

`score`列的最大值`100`就被查找出来了～

##### MIN函数

`MIN`函数是用来查询某列中数据的最小值，以`student_score`表中的`score`列为例来看一下：

```sql
mysql> SELECT MIN(score) FROM student_score;
+------------+
| MIN(score) |
+------------+
|         46 |
+------------+
1 row in set (0.00 sec)

mysql>
```

`score`列的最小值`46`就被查找出来了～

##### SUM函数

`SUM`函数是用来计算某列数据的和，还是以`student_score`表中的`score`列为例来看一下：

```sql
mysql> SELECT SUM(score) FROM student_score;
+------------+
| SUM(score) |
+------------+
|        585 |
+------------+
1 row in set (0.01 sec)

mysql>
```

所有学生的成绩总和`585`就被查询出来了，比我们用自己算快多了哈～

##### AVG函数

`AVG`函数是用来计算某列数据的平均数，还是以`student_score`表中的`score`列为例来看一下：

```sql
mysql> SELECT AVG(score) FROM student_score;
+------------+
| AVG(score) |
+------------+
|    73.1250 |
+------------+
1 row in set (0.00 sec)

mysql>
```

可以看到平均分就是`73.1250`。

##### 给定搜索条件下聚集函数的使用

聚集函数并不是一定要统计一个表中的所有记录，我们也可以指定搜索条件来限定这些聚集函数作用的范围。比方说我们只想统计`'母猪的产后护理'`这门课程的平均分可以这么写：

```sql
mysql> SELECT AVG(score) FROM student_score WHERE subject = '母猪的产后护理';
+------------+
| AVG(score) |
+------------+
|    73.0000 |
+------------+
1 row in set (0.00 sec)

mysql>
```

换句话说就是：不在搜索条件中的那些记录是不参与统计的。

##### 聚集函数中DISTINCT的使用

默认情况下，上边介绍的聚集函数将计算指定列的所有非`NULL`数据，如果我们指定的列中有重复数据的话，可以选择使用`DISTINCT`来过滤掉这些重复数据。比方说我们想查看一下`student_info`表中存储了多少个专业的学生信息，就可以这么写：

```sql
mysql> SELECT COUNT(DISTINCT major) FROM student_info;
+-----------------------+
| COUNT(DISTINCT major) |
+-----------------------+
|                     4 |
+-----------------------+
1 row in set (0.01 sec)

mysql>
```

可以看到一共有4个专业。

##### 组合聚集函数

这些聚集函数也可以集中在一个查询中使用，比如这样：

```sql
mysql> SELECT COUNT(*) AS 成绩记录总数, MAX(score) AS 最高成绩, MIN(score) AS 最低成绩, AVG(score) AS 平均成绩 FROM student_score;
+--------------------+--------------+--------------+--------------+
| 成绩记录总数       | 最高成绩     | 最低成绩     | 平均成绩     |
+--------------------+--------------+--------------+--------------+
|                  8 |          100 |           46 |      73.1250 |
+--------------------+--------------+--------------+--------------+
1 row in set (0.00 sec)

mysql>
```

### 隐式类型转换

#### 隐式类型转换的场景

只要某个值的类型与上下文要求的类型不符，`MySQL`就会根据上下文环境中需要的类型对该值进行类型转换，由于这些类型转换都是`MySQL`自动完成的，所以也可以被称为`隐式类型转换`。我们列举几种常见的隐式类型转换的场景：

1. 把操作数类型转换为适合操作符计算的相应类型。

   比方说对于加法操作符`+`来说，它要求两个操作数都必须是数字才能进行计算，所以如果某个操作数不是数字的话，会将其隐式转换为数字，比方说下边这几个例子：

   ```arduino
   1 + 2       →   3
   '1' + 2     →   3
   '1' + '2'   →   3
   ```

   虽然`'1'`、`'2'`都是字符串，但是如果它们作为加法操作符`+`的操作数的话，都会被强制转换为数字，所以上边几个表达式其实都会被当作`1 + 2`去处理的，这些表达式被放在查询列表时的效果如下：

   ```sql
   mysql> SELECT 1 + 2, '1' + 2, '1' + '2';
   +-------+---------+-----------+
   | 1 + 2 | '1' + 2 | '1' + '2' |
   +-------+---------+-----------+
   |     3 |       3 |         3 |
   +-------+---------+-----------+
   1 row in set (0.00 sec)
   
   mysql>
   ```

2. 将函数参数转换为该函数期望的类型。

   我们拿用于拼接字符串的`CONCAT`函数举例，这个函数以字符串类型的值作为参数，如果我们在调用这个函数的时候，传入了别的类型的值作为参数，`MySQL`会自动把这些值的类型转换为字符串类型的：

   ```arduino
   CONCAT('1', '2')    →   '12'
   CONCAT('1', 2)      →   '12'
   CONCAT(1, 2)        →   '12'
   ```

   虽然`1`、`2`都是数字，但是如果它们作为`CONCAT`函数的参数的话，都会被强制转换为字符串，所以上边几个表达式其实都会被当作`CONCAT('1', '2)`去处理的，这些表达式被放到查询列表时的效果如下：

   ```scss
   mysql> SELECT CONCAT('1', '2'), CONCAT('1', 2), CONCAT(1, 2);
   +------------------+----------------+--------------+
   | CONCAT('1', '2') | CONCAT('1', 2) | CONCAT(1, 2) |
   +------------------+----------------+--------------+
   | 12               | 12             | 12           |
   +------------------+----------------+--------------+
   1 row in set (0.00 sec)
   
   mysql>
   ```

3. 存储数据时，把某个值转换为某个列需要的类型。

   我们先新建一个简单的表`t`：

   ```sql
   CREATE TABLE t (
       i1 TINYINT,
       i2 TINYINT,
       s VARCHAR(100)
   );
   ```

   这个表有三个列，列`i1`和`i2`是用来存储整数的，列`s`是用来存储字符串的，如果我们在存储数据的时候填入的不是期望的类型，就像这样：

   ```sql
   mysql> INSERT INTO t(i1, i2, s) VALUES('100', '100', 200);
   Query OK, 1 row affected (0.01 sec)
   
   mysql>
   ```

   我们为列`i1`和`i2`填入的值是一个字符串值：`'100'`，列`s`填入的值是一个整数值：`200`，虽然说类型都不对，但是由于隐式类型转换的存在，在插入数据的时候字符串`'100'`会被转型为整数`100`，整数`200`会被转型成字符串`'200'`，所以最后插入成功，我们来看一下效果：

   ```sql
   mysql> SELECT * FROM t;
   +------+------+------+
   | i1   | i2   | s    |
   +------+------+------+
   |  100 |  100 | 200  |
   +------+------+------+
   1 row in set (0.00 sec)
   
   mysql>
   ```

#### 类型转换的注意事项

1. `MySQL`会尽量把值转换为表达式中需要的类型，而不是产生错误。

   按理说`'23sfd'`这个字符串无法转换为数字，但是`MySQL`规定只要字符串的开头部分包含数字，那么就把这个字符串转换为开头的数字，如果开头并没有包含数字，那么将被转换成`0`，比方说这样：

   ```arduino
   '23sfd'         →   23
   '2019-08-28'    →   2019
   '11:30:32'      →   11
   'sfd'           →   0
   ```

   看个例子：

   ```sql
   mysql> SELECT '23sfd' + 0, 'sfd' + 0;
   +-------------+-----------+
   | '23sfd' + 0 | 'sfd' + 0 |
   +-------------+-----------+
   |          23 |         0 |
   +-------------+-----------+
   1 row in set, 2 warnings (0.00 sec)
   
   mysql>
   ```

   不过需要注意的是，这种强制转换不能用于存储数据中，比方说这样：

   ```sql
   mysql> INSERT INTO t(i1, i2, s) VALUES('sfd', 'sfd', 'aaa');
   ERROR 1366 (HY000): Incorrect integer value: 'sfd' for column 'i1' at row 1
   mysql>
   ```

   由于`i1`和`i2`列需要整数，而填入的字符串`'sfd'`并不能顺利的转为整数，所以报错了。

2. 在运算时会自动提升操作数的类型。

   我们知道不同数据类型能表示的数值范围是不一样的，在小的数据类型经过算数计算后得出的结果可能大于该可以表示的范围。比方说`t`表中有一条记录如下：

   ```sql
   mysql> SELECT * FROM t;
   +------+------+------+
   | i1   | i2   | s    |
   +------+------+------+
   |  100 |  100 | 200  |
   +------+------+------+
   1 row in set (0.00 sec)
   
   mysql>
   ```

   其中的`i1`列和`i2`列的类型都是`TINYINT`，而`TINYINT`能表示的最大正整数是`127`，如果我们把`i1`列的值和`i2`列的值相加会发生什么呢？请看：

   ```sql
   mysql> SELECT i1 + i2 FROM t;
   +---------+
   | i1 + i2 |
   +---------+
   |     200 |
   +---------+
   1 row in set (0.00 sec)
   
   mysql>
   ```

   可以看到最后的结果是`200`，可是它已经超过`TINYINT`类型的表示范围了。其实在运算的过程中，`MySQL`自动将整数类型的操作数提升到了`BIGINT`，这样就不会产生运算结果太大超过`TINYINT`能表示的数值范围的尴尬情况了。类似的，有浮点数的运算过程会把操作数自动转型为`DOUBLE`类型。

```!
小贴士：

有隐式类型转换，自然也有显式类型转换。在MySQL中，可以使用CAST函数完成显式地类型转换，就是我们明确指定要将特定的数值转换为某
```

## 分组查询

### 复杂的数据统计

前边介绍了一些用来统计数据的聚集函数，我们可以方便的使用这些函数来统计出某列数据的行数、最大值、最小值、平均值以及整列数据的和。但是有些统计是比较麻烦的，比如说老师想根据成绩表分别统计出`'母猪的产后护理'`和`'论萨达姆的战争准备'`这两门课的平均分，那我们需要下边两个查询：

```sql
mysql> SELECT AVG(score) FROM student_score WHERE subject = '母猪的产后护理';
+------------+
| AVG(score) |
+------------+
|    73.0000 |
+------------+
1 row in set (0.00 sec)

mysql> SELECT AVG(score) FROM student_score WHERE subject = '论萨达姆的战争准备';
+------------+
| AVG(score) |
+------------+
|    73.2500 |
+------------+
1 row in set (0.00 sec)
```

### 创建分组

如果课程增加到20门怎么办呢？我们一共需要写20个查询语句，这样神烦哎。为了在一条查询语句中就完成这20条语句的任务，所以引入了`分组`的概念，就是：针对某个列，将该列的值相同的记录分到一个组中。拿`subject`列来说，按照`subject`列分组的意思就是将`subject`列的值是`'母猪的产后护理'`的记录划分到一个组中，将`subject`列的值是`'论萨达姆的战争准备'`的记录划分到另一个组中，如果`subject`列还有别的值，则划分更多的组。其中分组依靠的列我们可以称之为`分组列`。所以在`student_score`表中按照`subject`列分组后的图示就是这样：

![image-20230310011731806](..\images\mysql_group.png)

`subject`列中有多少不重复的课程，那就会有多少个分组。幸运的是，只要我们在`GROUP BY`子句中添加上`分组列`就好了，`MySQL`会帮助我们自动建立分组来方便我们统计信息，具体语句如下：

```sql
mysql> SELECT subject, AVG(score) FROM student_score GROUP BY subject;
+-----------------------------+------------+
| subject                     | AVG(score) |
+-----------------------------+------------+
| 母猪的产后护理              |    73.0000 |
| 论萨达姆的战争准备          |    73.2500 |
+-----------------------------+------------+
2 rows in set (0.01 sec)

mysql>
```

这个查询的执行过程就是按照`subject`中的值将所有的记录分成两组，然后分别对每个分组中记录的`score`列调用`AVG`函数进行数据统计。

在使用`分组`的时候必须要意识到，分组的存在仅仅是为了方便我们分别统计各个分组中的信息，所以我们只需要把分组列和聚集函数放到查询列表处就好！当然，如果`非分组列`出现在查询列表中会出现什么情况呢？比如下边这个查询：

```vbnet
mysql> SELECT number, subject, AVG(score) FROM student_score GROUP BY subject;
ERROR 1055 (42000): Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'xiaohaizi.student_score.number' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
mysql>
```

可以看到出现了错误。为啥会错误呢？回想一下我们使用`GROUP BY`子句的初衷，我们只是想把记录分为若干组，然后再对各个组分别调用聚集函数去做一些统计工作。本例中的查询列表处放置了既非分组列、又非聚集函数的`number`列，那我们想表达啥意思呢？从各个分组中的记录中取一条记录的`number`列？该取分组中的哪条记录为好呢？比方说对于`'母猪的产后护理'`这个分组中的记录来说，该分组中有4条记录，那`number`列的值应该取`20180101`，还是`20180102`，还是`20180103`，还是`20180104`呢？这个我们也不知道，也就是说把非分组列放到查询列表中会引起争议，导致结果不确定。基于此，设计MySQL的大叔才会为上述语句报错。

```!
小贴士：

其实假如分组后的每个分组的所有记录的某个非分组列的值都一样，那我把该非分组列加入到查询列表中也没啥问题呀。比方说按照subject列进行分组后，假如在'母猪的产后护理'的分组中各条记录的number列的值都相同，在'论萨达姆的战争准备'的分组中各条记录的number列的值也都相同，那么我们把number列放在查询列表中也没啥问题。可能设计MySQL的大叔觉得这种说法也有点儿道理，他们提出了一个称之为ONLY_FULL_GROUP_BY的SQL模式，当我们关闭这个SQL模式时，就允许把非分组列放到查询列表中。当然，什么是SQL模式，怎么开启和关闭这个称之为ONLY_FULL_GROUP_BY的SQL模式，不是我们初学者要考虑的问题，等以后大家变牛的时候可以再到文档中去查看。
```

### 带有WHERE子句的分组查询

上边的例子是将表中每条记录都划分到某个分组中，我们也可以在划分分组的时候就将某些记录过滤掉，这时就需要使用`WHERE`子句了。比如老师觉得各个科目的平均分太低了，所以想先把分数低于`60`分的记录去掉之后再统计平均分，就可以这么写：

```sql
mysql> SELECT subject, AVG(score) FROM student_score WHERE score >= 60 GROUP BY subject;
+-----------------------------+------------+
| subject                     | AVG(score) |
+-----------------------------+------------+
| 母猪的产后护理              |    89.0000 |
| 论萨达姆的战争准备          |    82.3333 |
+-----------------------------+------------+
2 rows in set (0.00 sec)

mysql>
```

这个过程可以分成两个步骤理解：

将记录进行过滤后分组。

在进行分组的时候将过滤掉不符合`WHERE`子句的记录，所以，最后的分组情况其实是这样的（少于60分的记录被过滤掉了）：

![image-20230310012442852](F:\git\learn_summary\images\group_2.png)

分别对各个分组进行数据统计。

统计之后就产生了上述的结果。

### 作用于分组的过滤条件

有时候某个带有`GROUP BY`子句的查询中可能会产生非常多的分组，假设`student_score`表中存储了100门学科的成绩，也就是`subject`列中有100个不重复的值，那就会产生100个分组，也就意味着这个查询的结果集中会产生100条记录。如果我们不想在结果集中得到这么多记录，只想把那些符合某些条件的分组加入到结果集，从而减少结果集中记录的条数，那就需要把针对分组的条件放到`HAVING`子句了。比方说老师想要查询平均分大于`73`分的课程，就可以这么写：

```sql
mysql> SELECT subject, AVG(score) FROM student_score GROUP BY subject HAVING AVG(score) > 73;
+-----------------------------+------------+
| subject                     | AVG(score) |
+-----------------------------+------------+
| 论萨达姆的战争准备          |    73.2500 |
+-----------------------------+------------+
1 row in set (0.00 sec)

mysql>
```

其实这里所谓的`针对分组的条件`一般是指下边这两种：

- 分组列

  也就是说我们可以把用于分组的列放到`HAVING`子句的条件中，比如这样：

  ```sql
  SELECT subject, AVG(score) FROM student_score GROUP BY subject having subject = '母猪的产后护理';
  ```

- 作用于分组的聚集函数

  当然，并不是`HAVING`子句中只能放置在查询列表出现的那些聚集函数，只要是针对这个分组进行统计的聚集函数都可以，比方说老师想查询最高分大于98分的课程的平均分，可以这么写：

  ```sql
  mysql> SELECT subject, AVG(score) FROM student_score GROUP BY subject HAVING MAX(score) > 98;
  +-----------------------+------------+
  | subject               | AVG(score) |
  +-----------------------+------------+
  | 母猪的产后护理        |    73.0000 |
  +-----------------------+------------+
  1 row in set (0.00 sec)
  
  mysql>
  ```

  其中的`MAX(score)`这个聚集函数并没有出现在查询列表中，但仍然可以作为`HAVING`子句中表达式的一部分。

### 分组和排序

如果我们想对各个分组查询出来的统计数据进行排序，需要为查询列表中有聚集函数的表达式添加`别名`，比如想按照各个学科的平均分从大到小降序排序，可以这么写：

```sql
mysql> SELECT subject, AVG(score) AS avg_score FROM student_score GROUP BY subject ORDER BY avg_score DESC;
+-----------------------------+-----------+
| subject                     | avg_score |
+-----------------------------+-----------+
| 论萨达姆的战争准备          |   73.2500 |
| 母猪的产后护理              |   73.0000 |
+-----------------------------+-----------+
2 rows in set (0.01 sec)
```

### 嵌套分组

有时候按照某个列进行分组太笼统，一个分组内可以被继续划分成更小的分组。比方说对于`student_info`表来说，我们可以先按照`department`来进行分组，所以可以被划分为2个分组：

![image-20230310013033792](..\images\group_3.png)

我们觉得这样按照`department`分组后，各个分组可以再按照`major`来继续分组，从而划分成更小的分组，所以再次分组之后的样子就是这样：

![image-20230310013135749](..\images\group_4.png)

所以现在有了2个大分组，4个小分组，我们把这种对大的分组下继续分组的的情形叫做`嵌套分组`，如果你乐意，你可以继续把小分组划分成更小的分组。我们只需要在`GROUP BY`子句中把各个分组列依次写上，用逗号`,`分隔开就好了。比如这样：

```sql
mysql> SELECT department, major, COUNT(*) FROM student_info GROUP BY department, major;
+-----------------+--------------------------+----------+
| department      | major                    | COUNT(*) |
+-----------------+--------------------------+----------+
| 航天学院        | 电子信息                 |        1 |
| 航天学院        | 飞行器设计               |        1 |
| 计算机学院      | 计算机科学与工程         |        2 |
| 计算机学院      | 软件工程                 |        2 |
+-----------------+--------------------------+----------+
4 rows in set (0.00 sec)

mysql>
```

可以看到，在`嵌套分组`中，聚集函数将作用在最后一个分组列上，在这个例子中就是`major`列。

### 使用分组注意事项

使用分组来统计数据给我们带来了非常大的便利，但是要随时提防有坑的地方：

1. 如果分组列中含有`NULL`值，那么`NULL`也会作为一个独立的分组存在。

2. 如果存在多个分组列，也就是`嵌套分组`，聚集函数将作用在最后的那个分组列上。

3. 如果查询语句中存在`WHERE`子句和`ORDER BY`子句，那么`GROUP BY`子句必须出现在`WHERE`子句之后，`ORDER BY`子句之前。

4. `非分组列`不能单独出现在检索列表中(可以被放到聚集函数中)。

5. `GROUP BY`子句后也可以跟随`表达式`(但不能是聚集函数)。

   上边介绍的`GROUP BY`后跟随的都是表中的某个列或者某些列，其实一个表达式也可以，比如这样：

   ```sql
   mysql> SELECT concat('专业：', major), COUNT(*) FROM student_info GROUP BY concat('专业：', major);
   +-----------------------------------+----------+
   | concat('专业：', major)           | COUNT(*) |
   +-----------------------------------+----------+
   | 专业：电子信息                    |        1 |
   | 专业：计算机科学与工程            |        2 |
   | 专业：软件工程                    |        2 |
   | 专业：飞行器设计                  |        1 |
   +-----------------------------------+----------+
   4 rows in set (0.00 sec)
   
   mysql>
   ```

   `MySQL`会根据这个表达式的值来对记录进行分组，使用表达式进行分组的时候需要特别注意，查询列表中的表达式和`GROUP BY`子句中的表达式必须完全一样。不过一般情况下我们也不会用表达式进行分组，所以目前基本没啥用～

6. `WHERE`子句和`HAVING`子句的区别。

   `WHERE`子句在分组前进行过滤，作用于每一条记录，`WHERE`子句过滤掉的记录将不包括在分组中。而`HAVING`子句在数据分组后进行过滤，作用于整个分组。

### 简单查询语句中各子句的顺序

我们上边介绍了查询语句的各个子句，但是除了`SELECT`之外，其他的子句全都是可以省略的。如果在一个查询语句中出现了多个子句，那么它们之间的顺序是不能乱放的，顺序如下所示：

```vbnet
SELECT [DISTINCT] 查询列表
[FROM 表名]
[WHERE 布尔表达式]
[GROUP BY 分组列表 ]
[HAVING 分组过滤条件]
[ORDER BY 排序列表]
[LIMIT 开始行, 限制条数]
```

其中中括号`[]`中的内容表示可以省略，我们在书写查询语句的时候各个子句必须严格遵守这个顺序，不然会报错的！

## 子查询

### 多表查询的需求

截止到目前为止我们介绍的查询语句都是作用于单个表的，但是有时候会有从多个表中查询数据的需求，比如我们想查一下名叫`'杜琦燕'`的学生的各科成绩该怎么办呢？我们只能先从`student_info`表中根据名称找到对应的学生学号，然后再通过学号到`student_score`表中找着对应的成绩信息，所以这个问题的解决方案就是书写两个查询语句：

```sql
mysql> SELECT number FROM student_info WHERE name = '杜琦燕';
+----------+
| number   |
+----------+
| 20180102 |
+----------+
1 row in set (0.00 sec)

mysql> SELECT * FROM student_score WHERE number = 20180102;
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
+----------+-----------------------------+-------+
2 rows in set (0.00 sec)
```

### 标量子查询

我们回过头再仔细看看上述的两条查询语句，第二条查询语句的搜索条件其实是用到了第一条查询语句的查询结果。为了书写简便，我们可以把这两条语句合并到一条语句中，从而减少了把第一条查询语句的结果复制粘贴到第二条查询语句中的步骤，就像这样：

```sql
mysql> SELECT * FROM student_score WHERE number = (SELECT number FROM student_info WHERE name = '杜琦燕');
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
+----------+-----------------------------+-------+
2 rows in set (0.01 sec)
```

我们把第二条查询语句用小括号`()`扩起来作为一个操作数放到了第一条的搜索条件处，这样就起到了合并两条查询语句的作用。小括号中的查询语句也被称为`子查询`或者`内层查询`，使用`内层查询`的结果作为搜索条件的操作数的查询称为`外层查询`。如果你在一个查询语句中需要用到更多的表的话，那么在一个子查询中可以继续嵌套另一个子查询，在执行查询语句时，将按照从内到外的顺序依次执行这些查询。

```!
小贴士：

事实上，所有的子查询都必须用小括号扩起来，否则是非法的。
```

在这个例子中的子查询的结果只有一个值(也就是`'杜琦燕'`的学号)，这种子查询称之为`标量子查询`。正因为`标量子查询`单纯的代表一个值，所以它可以作为表达式的操作数来参与运算，它除了用在外层查询的搜索条件中以外，也可以被放到查询列表处，比如这样：

```sql
mysql> SELECT (SELECT number FROM student_info WHERE name = '杜琦燕') AS 学号;
+----------+
| 学号     |
+----------+
| 20180102 |
+----------+
1 row in set (0.00 sec)

mysql>
```

`标量子查询`单纯的代表一个值，由`标量子查询`作为的操作数组成的搜索条件只要符合表达语法就可以。比方说我们来查询学号大于`'杜琦燕'`的学号的学生成绩，可以这么写：

```sql
mysql> SELECT * FROM student_score WHERE number > (SELECT number FROM student_info WHERE name = '杜琦燕');
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180103 | 母猪的产后护理              |    59 |
| 20180103 | 论萨达姆的战争准备          |    61 |
| 20180104 | 母猪的产后护理              |    55 |
| 20180104 | 论萨达姆的战争准备          |    46 |
+----------+-----------------------------+-------+
4 rows in set (0.00 sec)

mysql>
```

这样结果集的记录中的学号都大于`'杜琦燕'`的学号。

### 列子查询

如果我们想查询`'计算机科学与工程'`专业的学生的成绩，我们需要先从`student_info`表中根据专业名称找到对应的学生学号，然后再通过学号到`student_score`表中找着对应的成绩信息，所以这个问题的解决方案就是书写下述两个查询语句：

```sql
mysql> SELECT number FROM student_info WHERE major = '计算机科学与工程';
+----------+
| number   |
+----------+
| 20180101 |
| 20180102 |
+----------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM student_score WHERE number IN (20180101, 20180102);
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180101 | 母猪的产后护理              |    78 |
| 20180101 | 论萨达姆的战争准备          |    88 |
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
+----------+-----------------------------+-------+
4 rows in set (0.00 sec)

mysql>
```

第二条查询语句的搜索条件也是用到了第一条查询语句的查询结果，我们自然可以想到把第一条查询语句作为`内层查询`，把第二条查询语句作为`外层`查询来将这两个查询语句合并为一个查询语句，就像这样：

```sql
mysql> SELECT * FROM student_score WHERE number IN (SELECT number FROM student_info WHERE major = '计算机科学与工程');
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180101 | 母猪的产后护理              |    78 |
| 20180101 | 论萨达姆的战争准备          |    88 |
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
+----------+-----------------------------+-------+
4 rows in set (0.00 sec)

mysql>
```

很显然第一条查询语句的结果集中并不是一个单独的值，而是一个列（本例中第一条查询语句的结果集中该列包含2个值，分别是：`20180101`和`20180102`），所以它对应的子查询也被称之为`列子查询`。因为`列子查询`得到的结果是多个值，相当于一个列表。我们前边的章节中说过，`IN`和`NOT IN`操作符正好是用来匹配列表的，上边使用的例子是使用`IN`操作符和子查询的结果组成表达式来作为外层查询的搜索条件的。`NOT IN`和`IN`的操作符的使用方式是一样的，只不过语义不同罢了，我们就不赘述了。

### 行子查询

有`列子查询`，大家肯定就好奇有没有`行子查询`。哈哈，当然有了，只要子查询的结果集中最多只包含一条记录，而且这条记录中有超过一个列的数据（如果该条记录只包含一个列的话，该子查询就成了`标量子查询`），那么这个子查询就可以被称之为`行子查询`，比如这样：

```sql
mysql> SELECT * FROM student_score WHERE (number, subject) = (SELECT number, '母猪的产后护理' FROM student_info LIMIT 1);
+----------+-----------------------+-------+
| number   | subject               | score |
+----------+-----------------------+-------+
| 20180104 | 母猪的产后护理        |    55 |
+----------+-----------------------+-------+
1 row in set (0.01 sec)

mysql>
```

该子查询的查询列表是`number, '母猪的产后护理'`，其中`number`是列名，`'母猪的产后护理'`是一个常数。我们在子查询语句中加了`LIMIT 1`这个子句，意味着子查询最多只能返回一条记录，所以该子查询就可以被看作一个`行子查询`。

```!
小贴士：

在想要得到标量子查询或者行子查询，但又不能保证子查询的结果集只有一条记录时，应该使用LIMIT 1子句来限制记录数量。
```

另外，我们之前在唠叨表达式的时候操作数都是单一的一个值，不过由于上述的子查询执行后产生的结果集是一个行（包含2个列），所以用作等值比较的另一个操作数也得是2个值，本例中就是`(number, subject)`（注意，这两个值必须用小括号`()`扩住，否则会产生歧义）。它表达的语义就是：先获取到子查询的执行结果，然后再执行外层查询，如果`student_score`中记录的`number`等于子查询结果中的`number`列并且`subject`列等于子查询结果中的`'母猪的产后护理'`，那么就将该记录加入到结果集。

### 表子查询

如果子查询结果集中包含多行多列，那么这个子查询也可以被称之为`表子查询`，比如这样：

```sql
mysql> SELECT * FROM student_score WHERE (number, subject) IN (SELECT number, '母猪的产后护理' FROM student_info WHERE major = '计算机科学与工程');
+----------+-----------------------+-------+
| number   | subject               | score |
+----------+-----------------------+-------+
| 20180101 | 母猪的产后护理        |    78 |
| 20180102 | 母猪的产后护理        |   100 |
+----------+-----------------------+-------+
2 rows in set (0.00 sec)

mysql>
```

在这个例子中的子查询执行之后的结果集中包含多行多列，所以可以被看作是一个`表子查询`。

### EXISTS和NOT EXISTS子查询

有时候外层查询并不关心子查询中的结果是什么，而只关心子查询的结果集是不是为空集，这时可以用到下边这两个操作符：

| 操作符       | 示例                      | 描述                               |
| ------------ | ------------------------- | ---------------------------------- |
| `EXISTS`     | `EXISTS (SELECT ...)`     | 当子查询结果集不是空集时表达式为真 |
| `NOT EXISTS` | `NOT EXISTS (SELECT ...)` | 当子查询结果集是空集时表达式为真   |



我们来举个例子：

```sql
mysql> SELECT * FROM student_score WHERE EXISTS (SELECT * FROM student_info WHERE number = 20180108);
Empty set (0.00 sec)

mysql>
```

其中子查询的意思是在`student_info`表中查找学号为`20180108`的学生信息，很显然并没有学号为`20180108`的学生，所以子查询的结果集是一个空集，于是`EXISTS`表达式的结果为`FALSE`，所以外层查询也就不查了，直接返回了一个`Empty set`，表示没有结果。你可以自己试一下`NOT EXISTS`的使用。

### 不相关子查询和相关子查询

前边介绍的子查询和外层查询都没有依赖关系，也就是说子查询可以独立运行并产生结果之后，再拿结果作为外层查询的条件去执行外层查询，这种子查询称为`不相关子查询`，比如下边这个查询：

```sql
mysql> SELECT * FROM student_score WHERE number = (SELECT number FROM student_info WHERE name = '杜琦燕');
+----------+-----------------------------+-------+
| number   | subject                     | score |
+----------+-----------------------------+-------+
| 20180102 | 母猪的产后护理              |   100 |
| 20180102 | 论萨达姆的战争准备          |    98 |
+----------+-----------------------------+-------+
2 rows in set (0.00 sec)

mysql>
```

子查询中只用到了`student_info`表而没有使用到`student_score`表，它可以单独运行并产生结果，这就是一种典型的`不相关子查询`。

而有时候我们需要在子查询的语句中引用到外层查询的值，这样的话子查询就不能当作一个独立的语句去执行，这种子查询被称为`相关子查询`。比方说我们想查看一些学生的基本信息，但是前提是这些学生在`student_score`表中有成绩记录，那可以这么写：

```sql
mysql> SELECT number, name, id_number, major FROM student_info WHERE EXISTS (SELECT * FROM student_score WHERE student_score.number = student_info.number);
+----------+-----------+--------------------+--------------------------+
| number   | name      | id_number          | major                    |
+----------+-----------+--------------------+--------------------------+
| 20180101 | 杜子腾    | 158177199901044792 | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 151008199801178529 | 计算机科学与工程         |
| 20180103 | 范统      | 17156319980116959X | 软件工程                 |
| 20180104 | 史珍香    | 141992199701078600 | 软件工程                 |
+----------+-----------+--------------------+--------------------------+
4 rows in set (0.00 sec)

mysql>
小贴士：

student_info和student_score表里都有number列，所以在子查询的WHERE语句中书写number = number会造成二义性，也就是让服务器懵逼，不知道这个number列到底是哪个表的，所以为了区分，在列名前边加上了表名，并用点.连接起来，这种显式的将列所属的表名书写出来的名称称为该列的全限定名。所以上边子查询的WHERE语句中用了列的全限定名：student_score.number = student_info.number。
```

这条查询语句可以分成这么两部分来理解

- 我们要查询学生的一些基本信息。
- 这些学生必须符合这样的条件：`必须有成绩记录保存在student_score表中`。

所以这个例子中的`相关子查询`的查询过程是这样的：

- 先执行外层查询获得到`student_info`表的第一条记录，发现它的`number`值是`20180101`。把`20180101`当作参数传入到子查询，此时子查询的意思是判断`student_score`表的`number`字段是否有`20180101`这个值存在，子查询的结果是该值存在，所以整个`EXISTS`表达式的值为`TRUE`，那么`student_info`表的第一条记录可以被加入到结果集。
- 再执行外层查询获得到`student_info`表的第二条记录，发现它的`number`值是`20180102`，与上边的步骤相同，`student_info`表的第二条记录也可以被加入到结果集。
- 与上边类似，`student_info`表的第三条记录也可以被加入到结果集。
- 与上边类似，`student_info`表的第四条记录也可以被加入到结果集。
- 再执行外层查询获得到`student_info`表的第五条记录，发现它的`number`值是`20180105`，把`20180105`当作参数传入到它的子查询，此时子查询的意思是判断`student_score`表的`number`字段是否有`20180105`这个值存在，子查询的结果是该值不存在，所以整个`EXISTS`表达式的值为`FALSE`，那么`student_info`表的第五条记录就不被加入结果集中。
- 与上一步骤类似，`student_info`表的第六条记录也不被加入结果集中。
- `student_info`表没有更多的记录了，结束查询。

所以最后的查询结果是上边展示的4条记录。如果你觉得`相关子查询`还是有点儿绕的话，那就返回去再重新看几遍这个查询的执行过程。

### 对同一个表的子查询

其实不只是在涉及多个表查询的时候会用到子查询，在只涉及单个表的查询中有时也会用到子查询。比方说我们想看看在`student_score`表的`'母猪的产后护理'`这门课的成绩中，有哪些超过了平均分的记录，脑子中第一印象是这么写：

```sql
mysql> SELECT * FROM student_score WHERE subject = '母猪的产后护理' AND score > AVG(score);
ERROR 1111 (HY000): Invalid use of group function
mysql>
```

很抱歉，报错了。为啥呢？因为聚集函数是用来对分组做数据统计的（如果没有GROUP BY语句那么意味着只有一个分组），而`WHERE`子句是以记录为单位来执行过滤操作的，在`WHERE`子句执行完成之后才会得到分组，也就是说：聚集函数不能放到WHERE子句中！！！ 如果我们想实现上边的需求，就需要搞一个`student_score`表的副本，就相当于有了两个`student_score`表，在一个表上使用聚集函数统计，统计完了之后拿着统计结果再到另一个表中进行过滤，这个过程可以这么写：

```sql
mysql>  SELECT * FROM student_score WHERE subject = '母猪的产后护理' AND score > (SELECT AVG(score) FROM student_score WHERE subject = '母猪的产后护理');
+----------+-----------------------+-------+
| number   | subject               | score |
+----------+-----------------------+-------+
| 20180101 | 母猪的产后护理        |    78 |
| 20180102 | 母猪的产后护理        |   100 |
+----------+-----------------------+-------+
2 rows in set (0.01 sec)

mysql>
```

我们使用子查询先统计出了`'母猪的产后护理'`这门课的平均分，然后再到外层查询中使用这个平均分组成的表达式来作为搜索条件去查找大于平均分的记录。

## 连接查询

### 再次认识关系表

我们之前一直使用`student_info`和`student_score`两个表来分别存储学生的基本信息和学生的成绩信息，其实合并成一张表也不是不可以，假设将两张表合并后的新表名称为`student_merge`，那它应该长这样：

**student_merge表** 

| number   | name   | sex  | id_number          | department | major            | enrollment_time | subject            | score |
| -------- | ------ | ---- | ------------------ | ---------- | ---------------- | --------------- | ------------------ | ----- |
| 20180101 | 杜子腾 | 男   | 158177199901044792 | 计算机学院 | 计算机科学与工程 | 2018-09-01      | 母猪的产后护理     | 78    |
| 20180101 | 杜子腾 | 男   | 158177199901044792 | 计算机学院 | 计算机科学与工程 | 2018-09-01      | 论萨达姆的战争准备 | 88    |
| 20180102 | 杜琦燕 | 女   | 151008199801178529 | 计算机学院 | 计算机科学与工程 | 2018-09-01      | 母猪的产后护理     | 100   |
| 20180102 | 杜琦燕 | 女   | 151008199801178529 | 计算机学院 | 计算机科学与工程 | 2018-09-01      | 论萨达姆的战争准备 | 98    |
| 20180103 | 范统   | 男   | 17156319980116959X | 计算机学院 | 软件工程         | 2018-09-01      | 母猪的产后护理     | 59    |
| 20180103 | 范统   | 男   | 17156319980116959X | 计算机学院 | 软件工程         | 2018-09-01      | 论萨达姆的战争准备 | 61    |
| 20180104 | 史珍香 | 女   | 141992199701078600 | 计算机学院 | 软件工程         | 2018-09-01      | 母猪的产后护理     | 55    |
| 20180104 | 史珍香 | 女   | 141992199701078600 | 计算机学院 | 软件工程         | 2018-09-01      | 论萨达姆的战争准备 | 46    |
| 20180105 | 范剑   | 男   | 181048200008156368 | 航天学院   | 飞行器设计       | 2018-09-01      | NULL               | NULL  |
| 20180106 | 朱逸群 | 男   | 197995199801078445 | 航天学院   | 电子信息         | 2018-09-01      | NULL               | NULL  |



有了这个合并后的表，我们就可以在一个查询语句中既查询到学生的基本信息，也查询到学生的成绩信息，比如这个查询语句：

```sql
SELECT number, name, major, subject, score FROM student_merge;
```

其中查询列表处的`name`和`major`属于学生的基本信息，`subject`和`score`属于学生的成绩信息，而`number`既属于成绩信息也属于基本信息，我们可以在一个对`student_merge`表的查询语句中很轻松的把这些信息都查询出来。但是别忘了一个学生可能会有很多门学科的成绩信息，也就是说每当我们想为一个学生增加一门学科的成绩信息时，我们必须把他的基本信息再抄一遍，这种同一个学生的基本信息被冗余存储会带来下边的问题：

- 问题一：浪费存储空间。
- 问题二：当修改某个学生的基本信息时必须修改多处，很容易造成信息的不一致，增大维护的困难。

所以为了尽可能少的存储冗余信息，一开始我们就把这个所谓的`student_merge`表拆分成了`student_info`和`student_score`表，但是这两张表之间有某种关系作为纽带，这里的`某种关系`指的就是两个表都拥有的`number`列。

### 连接的概念

拆分之后的表的确解决了数据冗余问题，但是查询数据却成了一个问题。截至目前为止，在我们介绍的查询方式中，查询结果集只能是一个表中的一个列或者多个列，也就是说到目前为止还没有一种可以在一条查询语句中把某个学生的`number`、`name`、`major`、`subject`、`score`这几个信息都查询出来的方式。

```!
小贴士：

虽然我们前边介绍的子查询可以在一个查询语句中涉及到多个表，但是整个查询语句最终产生的结果集还是用来展示外层查询的结果，子查询的结果只是被当作中间结果来使用。
```

时代在召唤一种可以在一个查询语句结果集中展示多个表的信息的方式，`连接查询`承担了这个艰巨的历史使命。当然，为了故事的顺利发展，我们先建立两个简单的表并给它们填充一点数据：

```sql
mysql> CREATE TABLE t1 (m1 int, n1 char(1));
Query OK, 0 rows affected (0.02 sec)

mysql> CREATE TABLE t2 (m2 int, n2 char(1));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES(1, 'a'), (2, 'b'), (3, 'c');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> INSERT INTO t2 VALUES(2, 'b'), (3, 'c'), (4, 'd');
Query OK, 3 rows affected (0.00 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql>
```

我们成功建立了`t1`、`t2`两个表，这两个表都有两个列，一个是`INT`类型的，一个是`CHAR(1)`类型的，填充好数据的两个表长这样：

```sql
mysql> SELECT * FROM t1;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
+------+------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM t2;
+------+------+
| m2   | n2   |
+------+------+
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
3 rows in set (0.00 sec)

mysql>
```

`连接`的本质就是把各个表中的记录都取出来依次匹配的组合加入结果集并返回给用户。我们把t1和t2两个表连接起来的过程如下图所示：

![image-20230313005819675](..\images\mysql_join.png)

这个过程看起来就是把t1表的记录和t2表的记录连起来组成新的更大的记录，所以这个查询过程称之为连接查询。连接查询的结果集中包含一个表中的每一条记录与另一个表中的每一条记录相互匹配的组合，像这样的结果集就可以称之为`笛卡尔积`。因为表`t1`中有3条记录，表`t2`中也有3条记录，所以这两个表连接之后的笛卡尔积就有`3×3=9`行记录。在`MySQL`中，连接查询的语法也很随意，只要在`FROM`语句后边跟多个用逗号`,`隔开的表名就好了，比如我们把t1表和t2表连接起来的查询语句可以写成这样：

```css
mysql> SELECT * FROM t1, t2;
+------+------+------+------+
| m1   | n1   | m2   | n2   |
+------+------+------+------+
|    1 | a    |    2 | b    |
|    2 | b    |    2 | b    |
|    3 | c    |    2 | b    |
|    1 | a    |    3 | c    |
|    2 | b    |    3 | c    |
|    3 | c    |    3 | c    |
|    1 | a    |    4 | d    |
|    2 | b    |    4 | d    |
|    3 | c    |    4 | d    |
+------+------+------+------+
9 rows in set (0.00 sec)
```

查询列表处的`*`代表从FROM语句后列出的表中选取每个列，上边的查询语句其实和下边这几种写法都是等价的：

- 写法一：

  ```sql
  SELECT t1.m1, t1.n1, t2.m2, t2.n2 FROM t1, t2;
  ```

  这种写法是将`t1`、`t2`表中的列名都显式的写出来，也就是使用了列的全限定名。

- 写法二：

  ```sql
  SELECT m1, n1, m2, n2 FROM t1, t2;
  ```

  由于`t1`、`t2`表中的列名并不重复，所以没有可能让服务器懵逼的二义性，在查询列表上直接使用列名也是可以的。

- 写法三：

  ```sql
  SELECT t1.*, t2.* FROM t1, t2;
  ```

  这种写法意思就是查询`t1`表的全部的列，`t2`表的全部的列。

### 连接过程简介

如果我们乐意，我们可以连接任意数量张表，但是如果没有任何限制条件的话，这些表连接起来产生的`笛卡尔积`可能是非常巨大的。比方说3个100行记录的表连接起来产生的`笛卡尔积`就有`100×100×100=1000000`行数据！所以在连接的时候过滤掉特定记录组合是有必要的，在连接查询中的过滤条件可以分成两种：

- 涉及单表的条件

  这种只涉及单表的过滤条件我们之前都提到过一万遍了，我们之前也一直称为`搜索条件`，比如`t1.m1 > 1`是只针对`t1`表的过滤条件，`t2.n2 < 'd'`是只针对`t2`表的过滤条件。

- 涉及两表的条件

  这种过滤条件我们之前没见过，比如`t1.m1 = t2.m2`、`t1.n1 > t2.n2`等，这些条件中涉及到了两个表，我们稍后会仔细分析这种过滤条件是如何使用的哈。

下边我们就要看一下携带过滤条件的连接查询的大致执行过程了，比方说下边这个查询语句：

```ini
SELECT * FROM t1, t2 WHERE t1.m1 > 1 AND t1.m1 = t2.m2 AND t2.n2 < 'd';
```

在这个查询中我们指明了这三个过滤条件：

- `t1.m1 > 1`
- `t1.m1 = t2.m2`
- `t2.n2 < 'd'`

那么这个连接查询的大致执行过程如下：

1. 首先确定第一个需要查询的表，这个表称之为`驱动表`。此处假设使用`t1`作为驱动表，那么就需要到`t1`表中找满足`t1.m1 > 1`的记录，符合这个条件的`t1`表记录如下所示：

   ```sql
   +------+------+
   | m1   | n1   |
   +------+------+
   |    2 | b    |
   |    3 | c    |
   +------+------+
   2 rows in set (0.01 sec)
   ```

   我们可以看到，`t1`表中符合`t1.m1 > 1`的记录有两条。

2. 上一步骤中从驱动表每获取到一条记录，都需要到`t2`表中查找匹配的记录，所谓`匹配的记录`，指的是符合过滤条件的记录。因为是根据`t1`表中的记录去找`t2`表中的记录，所以`t2`表也可以被称之为`被驱动表`。上一步骤从驱动表中得到了2条记录，也就意味着需要查询2次`t2`表。此时涉及两个表的列的过滤条件`t1.m1 = t2.m2`就派上用场了：

   - 对于从`t1`表种查询得到的第一条记录，也就是当`t1.m1 = 2, t1.n1 = 'b'`时，过滤条件`t1.m1 = t2.m2`就相当于`t2.m2 = 2`，所以此时`t2`表相当于有了`t2.m2 = 2`、`t2.n2 < 'd'`这两个过滤条件，然后到`t2`表中执行单表查询，将得到的记录和从`t1`表中查询得到的第一条记录相组合得到下边的结果：

     ```diff
     +------+------+------+------+
     | m1   | n1   | m2   | n2   |
     +------+------+------+------+
     |    2 | b    |    2 | b    |
     +------+------+------+------+
     ```

   - 对于从`t1`表种查询得到的第二条记录，也就是当`t1.m1 = 3, t1.n1 = 'c'`时，过滤条件`t1.m1 = t2.m2`就相当于`t2.m2 = 3`，所以此时`t2`表相当于有了`t2.m2 = 3`、`t2.n2 < 'd'`这两个过滤条件，然后到`t2`表中执行单表查询，将得到的记录和从`t1`表中查询得到的第二条记录相组合得到下边的结果：

     ```diff
     +------+------+------+------+
     | m1   | n1   | m2   | n2   |
     +------+------+------+------+
     |    3 | c    |    3 | c    |
     +------+------+------+------+
     ```

   所以整个连接查询的执行最后得到的结果集就是这样：

   ```sql
   +------+------+------+------+
   | m1   | n1   | m2   | n2   |
   +------+------+------+------+
   |    2 | b    |    2 | b    |
   |    3 | c    |    3 | c    |
   +------+------+------+------+
   2 rows in set (0.00 sec)
   ```

从上边两个步骤可以看出来，我们上边唠叨的这个两表连接查询共需要查询1次`t1`表，2次`t2`表。当然这是在特定的过滤条件下的结果，如果我们把`t1.m1 > 1`这个条件去掉，那么从`t1`表中查出的记录就有3条，就需要查询3次`t2`表了。也就是说在两表连接查询中，驱动表只需要查询一次，被驱动表可能会被查询多次。

### 内连接和外连接

了解了连接查询的执行过程之后，视角再回到我们的`student_info`表和`student_score`表。现在我们想在一个查询语句中既查询到学生的基本信息，也查询到学生的成绩信息，就需要进行两表连接了。连接过程就是从`student_info`表中取出记录，在`student_score`表中查找`number`值相同的成绩记录，所以过滤条件就是`student_info.number = student_score.number`，整个查询语句就是这样：

```sql
mysql> SELECT student_info.number, name, major, subject, score FROM student_info, student_score WHERE student_info.number = student_score.number;
+----------+-----------+--------------------------+-----------------------------+-------+
| number   | name      | major                    | subject                     | score |
+----------+-----------+--------------------------+-----------------------------+-------+
| 20180101 | 杜子腾    | 计算机科学与工程         | 母猪的产后护理              |    78 |
| 20180101 | 杜子腾    | 计算机科学与工程         | 论萨达姆的战争准备          |    88 |
| 20180102 | 杜琦燕    | 计算机科学与工程         | 母猪的产后护理              |   100 |
| 20180102 | 杜琦燕    | 计算机科学与工程         | 论萨达姆的战争准备          |    98 |
| 20180103 | 范统      | 软件工程                 | 母猪的产后护理              |    59 |
| 20180103 | 范统      | 软件工程                 | 论萨达姆的战争准备          |    61 |
| 20180104 | 史珍香    | 软件工程                 | 母猪的产后护理              |    55 |
| 20180104 | 史珍香    | 软件工程                 | 论萨达姆的战争准备          |    46 |
+----------+-----------+--------------------------+-----------------------------+-------+
8 rows in set (0.00 sec)

mysql>
小贴士：

student_info表和student_score表都有number列，不过我们在上述查询语句的查询列表中只放置了student_info表的number列，这是因为我们的过滤条件是student_info.number = student_score.number，从两个表中取出的记录的number列都相同，所以只需要放置一个表中的number列到查询列表即可，也就是说我们把student_score.number放到查询列表处也是可以滴～
```

从上述查询结果中我们可以看到，各个同学对应的各科成绩就都被查出来了，可是有个问题，`范剑`和`朱逸群`同学，也就是学号为`20180105`和`20180106`的同学因为某些原因没有参加考试，所以在`studnet_score`表中没有对应的成绩记录。那如果老师想查看所有同学的考试成绩，即使是缺考的同学也应该展示出来，但是到目前为止我们介绍的`连接查询`是无法完成这样的需求的。我们稍微思考一下这个需求，其本质是想：驱动表中的记录即使在被驱动表中没有匹配的记录，也仍然需要加入到结果集。为了解决这个问题，就有了`内连接`和`外连接`的概念：

- 对于`内连接`的两个表，驱动表中的记录在被驱动表中找不到匹配的记录，该记录不会加入到最后的结果集，我们上边提到的连接都是所谓的`内连接`。

- 对于`外连接`的两个表，驱动表中的记录即使在被驱动表中没有匹配的记录，也仍然需要加入到结果集。

  在`MySQL`中，根据选取驱动表的不同，外连接仍然可以细分为2种：

  - 左外连接

    选取左侧的表为驱动表。

  - 右外连接

    选取右侧的表为驱动表。

可是这样仍然存在问题，即使对于外连接来说，有时候我们也并不想把驱动表的全部记录都加入到最后的结果集。这就犯难了，有时候匹配失败要加入结果集，有时候又不要加入结果集，这咋办，有点儿愁啊。。。噫，把过滤条件分为两种不就解决了这个问题了么，所以放在不同地方的过滤条件是有不同语义的：

- `WHERE`子句中的过滤条件

  `WHERE`子句中的过滤条件就是我们平时见的那种，不论是内连接还是外连接，凡是不符合`WHERE`子句中的过滤条件的记录都不会被加入最后的结果集。

- `ON`子句中的过滤条件

  对于外连接的驱动表的记录来说，如果无法在被驱动表中找到匹配`ON`子句中的过滤条件的记录，那么该记录仍然会被加入到结果集中，对应的被驱动表记录的各个字段使用`NULL`值填充。

  需要注意的是，这个`ON`子句是专门为外连接驱动表中的记录在被驱动表找不到匹配记录时应不应该把该记录加入结果集这个场景下提出的，所以如果把`ON`子句放到内连接中，`MySQL`会把它和`WHERE`子句一样对待，也就是说：内连接中的WHERE子句和ON子句是等价的。

一般情况下，我们都把只涉及单表的过滤条件放到`WHERE`子句中，把涉及两表的过滤条件都放到`ON`子句中，我们也一般把放到`ON`子句中的过滤条件也称之为`连接条件`。

```!
小贴士：

左外连接和右外连接简称左连接和右连接，所以下边提到的左外连接和右外连接中的`外`字都用括号扩起来，以表示这个字儿可有可无。
```

#### 左（外）连接的语法

左（外）连接的语法还是挺简单的，比如我们要把`t1`表和`t2`表进行左外连接查询可以这么写：

```sql
SELECT * FROM t1 LEFT [OUTER] JOIN t2 ON 连接条件 [WHERE 普通过滤条件];
```

其中中括号里的`OUTER`单词是可以省略的。对于`LEFT JOIN`类型的连接来说，我们把放在左边的表称之为外表或者驱动表，右边的表称之为内表或者被驱动表。所以上述例子中`t1`就是外表或者驱动表，`t2`就是内表或者被驱动表。需要注意的是，对于左（外）连接和右（外）连接来说，必须使用`ON`子句来指出连接条件。了解了左（外）连接的基本语法之后，再次回到我们上边那个现实问题中来，看看怎样写查询语句才能把所有的学生的成绩信息都查询出来，即使是缺考的考生也应该被放到结果集中：

```sql
mysql> SELECT student_info.number, name, major, subject, score FROM student_info LEFT JOIN student_score ON student_info.number = student_score.number;
+----------+-----------+--------------------------+-----------------------------+-------+
| number   | name      | major                    | subject                     | score |
+----------+-----------+--------------------------+-----------------------------+-------+
| 20180101 | 杜子腾    | 计算机科学与工程         | 母猪的产后护理              |    78 |
| 20180101 | 杜子腾    | 计算机科学与工程         | 论萨达姆的战争准备          |    88 |
| 20180102 | 杜琦燕    | 计算机科学与工程         | 母猪的产后护理              |   100 |
| 20180102 | 杜琦燕    | 计算机科学与工程         | 论萨达姆的战争准备          |    98 |
| 20180103 | 范统      | 软件工程                 | 母猪的产后护理              |    59 |
| 20180103 | 范统      | 软件工程                 | 论萨达姆的战争准备          |    61 |
| 20180104 | 史珍香    | 软件工程                 | 母猪的产后护理              |    55 |
| 20180104 | 史珍香    | 软件工程                 | 论萨达姆的战争准备          |    46 |
| 20180105 | 范剑      | 飞行器设计               | NULL                        |  NULL |
| 20180106 | 朱逸群    | 电子信息                 | NULL                        |  NULL |
+----------+-----------+--------------------------+-----------------------------+-------+
10 rows in set (0.00 sec)

mysql>
```

从结果集中可以看出来，虽然`范剑`和`朱逸群`并没有对应的成绩记录，但是由于采用的是连接类型为左（外）连接，所以仍然把它放到了结果集中，只不过在对应的成绩记录的各列使用`NULL`值填充而已。

#### 右（外）连接的语法

右（外）连接和左（外）连接的原理是一样一样的，语法也只是把`LEFT`换成`RIGHT`而已：

```sql
SELECT * FROM t1 RIGHT [OUTER] JOIN t2 ON 连接条件 [WHERE 普通过滤条件];
```

只不过驱动表是右边的表，被驱动表是左边的表，具体就不唠叨了。

#### 内连接的语法

内连接和外连接的根本区别就是在驱动表中的记录不符合`ON`子句中的连接条件时不会把该记录加入到最后的结果集，我们最开始唠叨的那些连接查询的类型都是内连接。不过之前仅仅提到了一种最简单的内连接语法，就是直接把需要连接的多个表都放到`FROM`子句后边。其实针对内连接，MySQL提供了好多不同的语法，我们以`t1`和`t2`表为例瞅瞅：

```sql
SELECT * FROM t1 [INNER | CROSS] JOIN t2 [ON 连接条件] [WHERE 普通过滤条件];
```

也就是说在`MySQL`中，下边这几种内连接的写法都是等价的：

- SELECT * FROM t1 JOIN t2;
- SELECT * FROM t1 INNER JOIN t2;
- SELECT * FROM t1 CROSS JOIN t2;

上边的这些写法和直接把需要连接的表名放到`FROM`语句之后，用逗号`,`分隔开的写法是等价的：

```sql
 SELECT * FROM t1, t2;
```

现在我们虽然介绍了很多种`内连接`的书写方式，不过熟悉一种就好了，这里我们推荐`INNER JOIN`的形式书写内连接（因为`INNER JOIN`语义很明确嘛，可以和`LEFT JOIN `和`RIGHT JOIN`很轻松的区分开）。这里需要注意的是，由于在内连接中ON子句和WHERE子句是等价的，所以内连接中不要求强制写明ON子句。

我们前边说过，连接的本质就是把各个连接表中的记录都取出来依次匹配的组合加入结果集并返回给用户。不论哪个表作为驱动表，两表连接产生的笛卡尔积肯定是一样的。而对于内连接来说，由于凡是不符合`ON`子句或`WHERE`子句中的条件的记录都会被过滤掉，其实也就相当于从两表连接的笛卡尔积中把不符合过滤条件的记录给踢出去，所以对于内连接来说，驱动表和被驱动表是可以互换的，并不会影响最后的查询结果。但是对于外连接来说，由于驱动表中的记录即使在被驱动表中找不到符合`ON`子句连接条件的记录也会被加入结果集，所以此时驱动表和被驱动表的关系就很重要了，也就是说左外连接和右外连接的驱动表和被驱动表不能轻易互换。

#### 小结

上边说了很多，给大家的感觉不是很直观，我们直接把表`t1`和`t2`的三种连接方式写在一起，这样大家理解起来就很easy了：

```sql
mysql> SELECT * FROM t1 INNER JOIN t2 ON t1.m1 = t2.m2;
+------+------+------+------+
| m1   | n1   | m2   | n2   |
+------+------+------+------+
|    2 | b    |    2 | b    |
|    3 | c    |    3 | c    |
+------+------+------+------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM t1 LEFT JOIN t2 ON t1.m1 = t2.m2;
+------+------+------+------+
| m1   | n1   | m2   | n2   |
+------+------+------+------+
|    2 | b    |    2 | b    |
|    3 | c    |    3 | c    |
|    1 | a    | NULL | NULL |
+------+------+------+------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM t1 RIGHT JOIN t2 ON t1.m1 = t2.m2;
+------+------+------+------+
| m1   | n1   | m2   | n2   |
+------+------+------+------+
|    2 | b    |    2 | b    |
|    3 | c    |    3 | c    |
| NULL | NULL |    4 | d    |
+------+------+------+------+
3 rows in set (0.00 sec)
```

连接查询产生的结果集就好像把散布到两个表中的信息被重新粘贴到了一个表，这个粘贴后的结果集可以方便我们分析数据，就不用老是两个表对照的看了。

### 多表连接

上边说过，如果我们乐意的话可以连接任意数量的表，我们再来创建一个简单的`t3`表：

```sql
mysql> CREATE TABLE t3 (m3 int, n3 char(1));
ERROR 1050 (42S01): Table 't3' already exists
mysql> INSERT INTO t3 VALUES(3, 'c'), (4, 'd'), (5, 'e');
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql>
```

与`t1`和`t2`表的结构一样，也是一个`INT`列，一个`CHAR(1)`列，现在我们看一下把这3个表连起来的样子：

```sql
mysql> SELECT * FROM t1 INNER JOIN t2 INNER JOIN t3 WHERE t1.m1 = t2.m2 AND t1.m1 = t3.m3;
+------+------+------+------+------+------+
| m1   | n1   | m2   | n2   | m3   | n3   |
+------+------+------+------+------+------+
|    3 | c    |    3 | c    |    3 | c    |
+------+------+------+------+------+------+
1 row in set (0.00 sec)

mysql>
```

其实上边的查询语句也可以写成这样，用哪个取决于你的心情：

```sql
SELECT * FROM t1 INNER JOIN t2 ON t1.m1 = t2.m2 INNER JOIN t3 ON t1.m1 = t3.m3;
```

这个查询的执行过程用伪代码表示一下就是这样：

```sql
for each row in t1 {

    for each row in t2 which satisfies t1.m1 = t2.m2 {
        
        for each row in t3 which satisfies t1.m1 = t3.m3 {
            send to client;
        }
    }
}
```

其实不管是多少个表的`连接`，本质上就是各个表的记录在符合过滤条件下的自由组合。

### 表的别名

我们前边曾经为列命名过别名，比如说这样：

```sql
mysql> SELECT number AS xuehao FROM student_info;
+----------+
| xuehao   |
+----------+
| 20180104 |
| 20180102 |
| 20180101 |
| 20180103 |
| 20180105 |
| 20180106 |
+----------+
6 rows in set (0.00 sec)

mysql>
```

我们可以把列的别名用在`ORDER BY`、`GROUP BY`等子句上，比如这样：

```sql
mysql> SELECT number AS xuehao FROM student_info ORDER BY xuehao DESC;
+----------+
| xuehao   |
+----------+
| 20180106 |
| 20180105 |
| 20180104 |
| 20180103 |
| 20180102 |
| 20180101 |
+----------+
6 rows in set (0.00 sec)

mysql>
```

与列的别名类似，我们也可以为表来定义别名，格式与定义列的别名一致，都是用空白字符或者`AS`隔开，这个在表名特别长的情况下可以让语句表达更清晰一些，比如这样：

```sql
mysql> SELECT s1.number, s1.name, s1.major, s2.subject, s2.score FROM student_info AS s1 INNER JOIN student_score AS s2 WHERE s1.number = s2.number;
+----------+-----------+--------------------------+-----------------------------+-------+
| number   | name      | major                    | subject                     | score |
+----------+-----------+--------------------------+-----------------------------+-------+
| 20180101 | 杜子腾    | 计算机科学与工程         | 母猪的产后护理              |    78 |
| 20180101 | 杜子腾    | 计算机科学与工程         | 论萨达姆的战争准备          |    88 |
| 20180102 | 杜琦燕    | 计算机科学与工程         | 母猪的产后护理              |   100 |
| 20180102 | 杜琦燕    | 计算机科学与工程         | 论萨达姆的战争准备          |    98 |
| 20180103 | 范统      | 软件工程                 | 母猪的产后护理              |    59 |
| 20180103 | 范统      | 软件工程                 | 论萨达姆的战争准备          |    61 |
| 20180104 | 史珍香    | 软件工程                 | 母猪的产后护理              |    55 |
| 20180104 | 史珍香    | 软件工程                 | 论萨达姆的战争准备          |    46 |
+----------+-----------+--------------------------+-----------------------------+-------+
8 rows in set (0.00 sec)

mysql>
```

这个例子中，我们在`FROM`子句中给`student_info`定义了一个别名`s1`，`student_score`定义了一个别名`s2`，那么在整个查询语句的其他地方就可以引用这个别名来替代该表本身的名字了。

### 自连接

我们上边说的都是多个不同的表之间的连接，其实同一个表也可以进行连接。比方说我们可以对两个`t1`表来生成`笛卡尔积`，就像这样：

```sql
mysql> SELECT * FROM t1, t1;
ERROR 1066 (42000): Not unique table/alias: 't1'
mysql>
```

咦，报了个错，这是因为设计MySQL的大叔不允许`FROM`子句中出现相同的表名。我们这里需要的是两张一模一样的`t1`表进行连接，为了把两个一样的表区分一下，需要为表定义别名。比如这样：

```css
mysql> SELECT * FROM t1 AS table1, t1 AS table2;
+------+------+------+------+
| m1   | n1   | m1   | n1   |
+------+------+------+------+
|    1 | a    |    1 | a    |
|    2 | b    |    1 | a    |
|    3 | c    |    1 | a    |
|    1 | a    |    2 | b    |
|    2 | b    |    2 | b    |
|    3 | c    |    2 | b    |
|    1 | a    |    3 | c    |
|    2 | b    |    3 | c    |
|    3 | c    |    3 | c    |
+------+------+------+------+
9 rows in set (0.00 sec)

mysql>
```

这里相当于我们为`t1`表定义了两个副本，一个是`table1`，另一个是`table2`，这里的连接过程就不赘述了，大家把它们认为是不同的表就好了。由于被连接的表其实是源自同一个表，所以这种连接也称为`自连接`。我们看一下这个`自连接`的现实意义，比方说我们想查看与`'史珍香'`相同专业的学生有哪些，可以这么写：

```sql
mysql> SELECT s2.number, s2.name, s2.major FROM student_info AS s1 INNER JOIN student_info AS s2 WHERE s1.major = s2.major AND s1.name = '史珍香' ;
+----------+-----------+--------------+
| number   | name      | major        |
+----------+-----------+--------------+
| 20180103 | 范统      | 软件工程     |
| 20180104 | 史珍香    | 软件工程     |
+----------+-----------+--------------+
2 rows in set (0.01 sec)

mysql>
```

`s1`、`s2`都可以看作是`student_info`表的一份副本，我们可以这样理解这个查询：

- 根据`s1.name = '史珍香'`搜索条件过滤`s1`表，可以得到该同学的基本信息：

  ```diff
  +----------+-----------+------+--------------------+-----------------+--------------+-----------------+
  | number   | name      | sex  | id_number          | department      | major        | enrollment_time |
  +----------+-----------+------+--------------------+-----------------+--------------+-----------------+
  | 20180104 | 史珍香    | 女   | 141992199701078600 | 计算机学院      | 软件工程     | 2018-09-01      |
  +----------+-----------+------+--------------------+-----------------+--------------+-----------------+
  ```

- 因为通过查询`s1`表，得到了`'史珍香'`所在的专业其实是`'软件工程'`，接下来就应该查询`s2`表了，查询`s2`表的时候的过滤条件`s1.major = s2.major`就相当于`s2.major = '软件工程'`，于是查询到2条记录：

  ```diff
  +----------+-----------+------+--------------------+-----------------+--------------+-----------------+
  | number   | name      | sex  | id_number          | department      | major        | enrollment_time |
  +----------+-----------+------+--------------------+-----------------+--------------+-----------------+
  | 20180103 | 范统      | 男   | 17156319980116959X | 计算机学院      | 软件工程     | 2018-09-01      |
  | 20180104 | 史珍香    | 女   | 141992199701078600 | 计算机学院      | 软件工程     | 2018-09-01      |
  +----------+-----------+------+--------------------+-----------------+--------------+-----------------+
  ```

  而我们只需要`s2`表的`number`、`name`、`major`这3个列的数据，所以最终的结果就长这样：

  ```diff
  +----------+-----------+--------------+
  | number   | name      | major        |
  +----------+-----------+--------------+
  | 20180103 | 范统      | 软件工程     |
  | 20180104 | 史珍香    | 软件工程     |
  +----------+-----------+--------------+
  ```

### 连接查询与子查询的转换

有的查询需求既可以使用连接查询解决，也可以使用子查询解决，比如

```sql
SELECT * FROM student_score WHERE number IN (SELECT number FROM student_info WHERE major = '计算机科学与工程');
```

这个子查询就可以被替换：

```vbnet
SELECT s2.* FROM student_info AS s1 INNER JOIN student_score AS s2 WHERE s1.number = s2.number AND s1.major = '计算机科学与工程';
```

大家在实际使用时可以按照自己的习惯来书写查询语句。

```!
小贴士：

MySQL服务器在内部可能将子查询转换为连接查询来处理，当然也可能用别的方式来处理，不过对于我们刚入门的小白来说，这些都不重
```

## 组合查询

我们前边说的都是单条查询语句，其实多条查询语句产生的结果集查也可以被合并成一个大的结果集，这种将多条查询语句产生的结果集合并起来的查询方式称为`合并查询`，或者`组合查询`。

### 涉及单表的组合查询

比方说我们有下边两个查询语句：

```sql
mysql> SELECT m1 FROM t1 WHERE m1 < 2;
+------+
| m1   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)

mysql> SELECT m1 FROM t1 WHERE m1 > 2;
+------+
| m1   |
+------+
|    3 |
+------+
1 row in set (0.00 sec)

mysql>
```

如果我们想把上边两个查询语句的结果集合并到一个大的结果集中，最简单的方式当然是使用`OR`操作符把两个查询语句中的搜索条件连起来，就像这样：

```sql
mysql> SELECT m1 FROM t1 WHERE m1 < 2 OR m1 > 2;
+------+
| m1   |
+------+
|    1 |
|    3 |
+------+
2 rows in set (0.00 sec)

mysql>
```

除了这种方式，我们还可以使用`UNION`来将两个查询语句连在一起，就像这样：

```sql
mysql> SELECT m1 FROM t1 WHERE m1 < 2 UNION SELECT m1 FROM t1 WHERE m1 > 2;
+------+
| m1   |
+------+
|    1 |
|    3 |
+------+
2 rows in set (0.01 sec)

mysql>
```

多个查询语句也直接用`UNION`来连起来：

```sql
mysql> SELECT m1 FROM t1 WHERE m1 < 2 UNION SELECT m1 FROM t1 WHERE m1 > 2 UNION SELECT m1 FROM t1 WHERE m1 = 2;
+------+
| m1   |
+------+
|    1 |
|    3 |
|    2 |
+------+
3 rows in set (0.00 sec)

mysql>
```

当然，并不是说使用`UNION`连接起来的各个查询语句的查询列表处只能有一个表达式，有多个表达式也是可以的，只要数量相同就可以了，比如下边这个使用`UNION`连接起来的各个查询语句的查询列表处都有2个表达式：

```sql
mysql> SELECT m1, n1 FROM t1 WHERE m1 < 2 UNION SELECT m1, n1 FROM t1 WHERE m1 > 2;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    3 | c    |
+------+------+
2 rows in set (0.00 sec)

mysql>
```

不过这里需要注意一点，设计`MySQL`的大叔建议：使用UNION连接起来的各个查询语句的查询列表中位置相同的表达式的类型应该是相同的。当然这不是硬性要求，如果不匹配的话，`MySQL`将会自动的进行类型转换，比方说下边这个组合查询语句：

```sql
mysql> SELECT m1, n1 FROM t1 WHERE m1 < 2 UNION SELECT n1, m1 FROM t1 WHERE m1 > 2;
+------+------+
| m1   | n1   |
+------+------+
| 1    | a    |
| c    | 3    |
+------+------+
2 rows in set (0.01 sec)

mysql>
```

使用`UNION`连接起来的两个查询中，第一个语句的查询列表是`m1, n1`，第二个查询语句的查询列表是`n1, m1`，我们应该注意两点：

- 第一个查询的查询列表处的`m1`和第二个查询的查询列表的`n1`对应，第一个查询的查询列表处的`n1`和第二个查询的查询列表的`m1`对应，`m1`和`n1`虽然类型不同，但`MySQL`会帮助我们自动进行必要的类型转换。
- 这几个查询语句的结果集都可以被合并到一个大的结果集中，但是这个大的结果集总是要有展示一下列名的吧，所以就规定组合查询的结果集中显示的列名将以第一个查询中的列名为准，上边的例子就采用了第一个查询中的`m1, n1`作为结果集的列名。

### 涉及不同表的组合查询

当然，如果只在同一个表中进行组合查询，貌似体现不出组合查询的强大，很多情况下组合查询还是用在涉及不同表的查询语句中的，比方说下边这两个查询：

```sql
mysql> SELECT m1, n1 FROM t1 WHERE m1 < 2;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
+------+------+
1 row in set (0.00 sec)

mysql> SELECT m2, n2 FROM t2 WHERE m2 > 2;
+------+------+
| m2   | n2   |
+------+------+
|    3 | c    |
|    4 | d    |
+------+------+
2 rows in set (0.00 sec)

mysql>
```

第一个查询是从`t1`表中查询`m1, n1`这两个列的数据，第二个查询是从`t2`表中查询`m2, n2`这两个列的数据。我们可以使用`UNION`直接将这两个查询语句拼接到一起：

```sql
mysql> SELECT m1, n1 FROM t1 WHERE m1 < 2 UNION SELECT m2, n2 FROM t2 WHERE m2 > 2;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    3 | c    |
|    4 | d    |
+------+------+
3 rows in set (0.01 sec)

mysql>
```

### 包含或去除重复的行

我们看下边这两个查询：

```sql
mysql> SELECT m1, n1 FROM t1;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
+------+------+
3 rows in set (0.00 sec)

mysql> SELECT m2, n2 FROM t2;
+------+------+
| m2   | n2   |
+------+------+
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
3 rows in set (0.00 sec)

mysql>
```

很显然，`t1`表里有3条记录，`t2`表里有3条记录，我们使用`UNION`把它们合并起来看一下：

```sql
mysql> SELECT m1, n1 FROM t1 UNION SELECT m2, n2 FROM t2;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
4 rows in set (0.00 sec)

mysql>
```

为什么合并后的结果只剩下了4条记录呢？因为使用`UNION`来合并多个查询的记录会默认过滤掉重复的记录。由于`t1`表和`t2`表都有`(2, b)、(3, c)`这两条记录，所以合并后的结果集就把他俩去重了。如果我们想要保留重复记录，可以使用`UNION ALL`来连接多个查询：

```sql
mysql> SELECT m1, n1 FROM t1 UNION ALL SELECT m2, n2 FROM t2;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
6 rows in set (0.00 sec)

mysql>
```

### 组合查询中的ORDER BY和LIMIT子句

`组合查询`会把各个查询的结果汇总到一块，如果我们相对最终的结果集进行排序或者只保留几行的话，可以在组合查询的语句末尾加上`ORDER BY`和`LIMIT`子句，就像这样：

```sql
mysql> SELECT m1, n1 FROM t1 UNION SELECT m2, n2 FROM t2 ORDER BY m1 DESC LIMIT 2;
+------+------+
| m1   | n1   |
+------+------+
|    4 | d    |
|    3 | c    |
+------+------+
2 rows in set (0.00 sec)

mysql>
```

如果我们能为各个小的查询语句加上括号`()`那就更清晰了，就像这样：

```sql
mysql> (SELECT m1, n1 FROM t1) UNION (SELECT m2, n2 FROM t2) ORDER BY m1 DESC LIMIT 2;
+------+------+
| m1   | n1   |
+------+------+
|    4 | d    |
|    3 | c    |
+------+------+
2 rows in set (0.01 sec)

mysql>
```

这里需要注意的一点是，由于最后的结果集展示的列名是第一个查询中给定的列名，所以`ORDER BY`子句中指定的排序列也必须是第一个查询中给定的列名（别名也可以）。

这里突发一个奇想，如果我们只想单独为各个小的查询排序，而不为最终的汇总的结果集排序行不行呢？先试试：

```sql
mysql> (SELECT m1, n1 FROM t1 ORDER BY m1 DESC) UNION (SELECT m2, n2 FROM t2 ORDER BY m2 DESC);
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
4 rows in set (0.00 sec)

mysql>
```

从结果来看，我们为各个小查询加入的`ORDER BY`子句好像并没有起作用，这是因为设计`MySQL`的大叔规定组合查询并不保证最后汇总起来的大结果集中的顺序是按照各个小查询的结果集中的顺序排序的，也就是说我们在各个小查询中加入`ORDER BY`子句的作用和没加一样～ 不过如果我们只是单纯的想从各个小的查询中获取有限条排序好的记录加入最终的汇总，那是可以滴，比如这样：

```sql
mysql> (SELECT m1, n1 FROM t1 ORDER BY m1 DESC LIMIT 1) UNION (SELECT m2, n2 FROM t2 ORDER BY m2 DESC LIMIT 1);
+------+------+
| m1   | n1   |
+------+------+
|    3 | c    |
|    4 | d    |
+------+------+
2 rows in set (0.00 sec)

mysql>
```

如图所示，最终结果集中的`(3, 'c')`其实就是查询`(SELECT m1, n1 FROM t1 ORDER BY m1 DESC LIMIT 1)`的结果，`(4, 'd')`其实就是查询`(SELECT m2, n2 FROM t2 ORDER BY m2 DESC LIMIT 1)`的结果。

## 数据的插入、删除和更新

前边介绍了让人眼花缭乱的查询方式，包括简单查询、子查询、连接查询、组合查询以及各种查询细节，可别忘了表里先得有数据之后查询才能有意义啊！之前我们只是简单介绍了数据的插入语句，本章我们将详细唠叨各种对表中数据的操作，包括插入数据、删除数据和更新数据。

### 准备工作

本集中要唠叨的是对表中数据的操作，首先需要确定用哪个表来演示这些操作，本着勤俭节约的精神，我们还是复用之前用过的`first_table`表，只不过这个表快被玩坏了，我们把它删掉重建一个，一切重新开始：

```sql
mysql> DROP TABLE first_table;
Query OK, 0 rows affected (0.02 sec)

mysql>

mysql> CREATE TABLE first_table (
    ->     first_column INT,
    ->     second_column VARCHAR(100)
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql>
```

对于`first_table`表来说，我们保留了两个列，一个是`INT`类型的`first_column`列，另一个是`VARCHAR(100)`类型的`second_column`列。

### 插入数据

在关系型数据库中，数据一般都是以`记录`(或者说`行`)为单位被插入到表中的，具体的插入形式且看我们慢慢道来。

#### 插入完整的记录

在插入完整的一条记录时，需要我们指定要插入表的名称和该条记录中全部列的具体数据，完整的语法是这样：

```sql
INSERT INTO 表名 VALUES(列1的值，列2的值, ..., 列n的值);
```

比如`first_table`里有两个列，分别是`first_column`和`second_column`，如果我们想要插入完整的记录的话，`VAULES()`中必须依次填入`first_column`列和`second_column`列的值，比如这样：

```sql
mysql> INSERT INTO first_table VALUES(1, 'aaa');
Query OK, 1 row affected (0.00 sec)

mysql>
```

可以看到执行结果是`Query OK, 1 row affected (0.01 sec)`，表明成功的插入了一行。然后再用`SELECT`语句看看表中的数据：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | aaa           |
+--------------+---------------+
1 row in set (0.00 sec)

mysql>
```

现在的`first_table`中就有了一条记录了。在使用这种插入一条完整记录的语法时必须注意，VALUES语句中必须给出表中所有列的值，缺一个都不行，如果我们不知道向某个列填什么值，可以使用填入`NULL`（前提是该列没有声明`NOT NULL`属性），就像这样：

```sql
mysql> INSERT INTO first_table VALUES(2, NULL);
Query OK, 1 row affected (0.01 sec)

mysql>

mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | aaa           |
|            2 | NULL          |
+--------------+---------------+
2 rows in set (0.00 sec)

mysql>
```

上述的这种插入方式VALUE列表中参数的顺序与表中各个列的顺序是一一对应的，其实我们也可以在书写插入语句时自定义一下列的顺序，就像这样：

```sql
mysql> INSERT INTO first_table(first_column, second_column) VALUES (3, 'ccc');
Query OK, 1 row affected (0.00 sec)

mysql>
```

在这个语句中，我们显式的指定了列的插入顺序是`(first_column, second_column)`，对应于`VALUES`列表中的值的顺序，也就是说`first_column`与值`3`对应，`second_column`与值`'ccc'`对应。之后即使`first_table`表中列的结构改变了，这个语句仍然能继续使用。我们也可以随意指定列的插入顺序，比如这样：

```sql
mysql> INSERT INTO first_table(second_column, first_column) VALUES ('ddd', 4);
Query OK, 1 row affected (0.01 sec)

mysql>
```

我们把`second_column`放在了`first_column`之前，所以`VALUES`列表中的值也需要改变顺序，来看一下插入效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | aaa           |
|            2 | NULL          |
|            3 | ccc           |
|            4 | ddd           |
+--------------+---------------+
4 rows in set (0.00 sec)

mysql>
```

#### 插入记录的一部分

我们在插入记录的时候，某些列的值可以被省略，但是这个列必须满足下边列出的某个条件之一：

- 该列允许存储NULL值
- 该列有DEFAULT属性，给出了默认值

我们定义的`first_table`表中的两个字段都允许存放`NULL`值，所以在插入数据的时候可以省略部分列的值。在`INSERT`语句中没有显式指定的列的值将被设置为`NULL`，比如这样写：

```scss
mysql> INSERT INTO first_table(first_column) VALUES(5);
Query OK, 1 row affected (0.00 sec)

mysql> INSERT INTO first_table(second_column) VALUES('fff');
Query OK, 1 row affected (0.00 sec)

mysql>
```

第一条插入语句我们只指定了`first_column`列的值是`5`，而没有指定`second_column`的值，所以`second_column`的值就是`NULL`；第二条插入语句我们只指定了`second_column`的值是`'ddd'`，而没有指定`first_column`的值，所以`first_column`的值就是`NULL`，也表示没有数据～ 看一下现在表中的数据：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | aaa           |
|            2 | NULL          |
|            3 | ccc           |
|            4 | ddd           |
|            5 | NULL          |
|         NULL | fff           |
+--------------+---------------+
6 rows in set (0.00 sec)

mysql>
```

我们再次强调一下，INSERT语句中指定的列顺序可以改变，但是一定要和`VALUES`列表中的值一一对应起来。

#### 批量插入记录

每插入一条记录就写一条`INSERT`语句也不是不行，但是对人来说太烦了，而且每插入一条记录就提交一个请求给服务器远没有一次把所有待插入的记录全部提交给服务器效率高，所以`MySQL`为我们提供了批量插入的语句，就是直接在`VALUES`后多加几组值，每组值用小括号`()`扩起来，各个组之间用逗号分隔就好了，就像这样：

```scss
mysql> INSERT INTO first_table(first_column, second_column) VALUES(7, 'ggg'), (8, 'hhh');
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql>
```

我们在这个INSERT语句中插入了`(7, 'ggg')`、`(8, 'hhh')`这么两条记录，直接把它们放到`VALUES`后边用逗号分开就好了，我们看一下插入效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | aaa           |
|            2 | NULL          |
|            3 | ccc           |
|            4 | ddd           |
|            5 | NULL          |
|         NULL | fff           |
|            7 | ggg           |
|            8 | hhh           |
+--------------+---------------+
8 rows in set (0.00 sec)

mysql>
```

#### 将某个查询的结果集插入表中

上边的插入语句都是我们显式的将记录的值放在`VALUES`后边，其实我们也可以将某个查询的结果集作为数据源插入到表中。我们先新建一个`second_table`表：

```sql
mysql> CREATE TABLE second_table (
    ->     s VARCHAR(200),
    ->     i INT
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql>
```

这个表有两个列，一个是`VARCHAR`类型的`s`列，另一个是`INT`类型的`i`列。现在这个`second_table`表中是没有数据的，我们想把`first_column`表中的一些数据插入到`second_table`表的话可以这么写：

```sql
mysql> INSERT INTO second_table(s, i) SELECT second_column, first_column FROM first_table WHERE first_column < 5;
Query OK, 4 rows affected (0.00 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql>
```

我们可以把这条INSERT语句分成两部分来理解：

1. 先执行查询语句。

   ```sql
   SELECT second_column, first_column FROM first_table WHERE first_column < 5;
   ```

   这条语句的结果集是

   ```sql
   +---------------+--------------+
   | second_column | first_column |
   +---------------+--------------+
   | aaa           |            1 |
   | NULL          |            2 |
   | ccc           |            3 |
   | ddd           |            4 |
   +---------------+--------------+
   ```

2. 把查询语句得到的结果集插入到指定的表中。

   把第1步中的到的结果集中的记录批量插入到`second_table`表中，得到的结果就是：

   ```sql
   mysql> SELECT * FROM second_table;
   +------+------+
   | s    | i    |
   +------+------+
   | aaa  |    1 |
   | NULL |    2 |
   | ccc  |    3 |
   | ddd  |    4 |
   +------+------+
   4 rows in set (0.00 sec)
   
   mysql>
   ```

在将某个查询的结果集插入到表中时需要注意，INSERT语句指定的列要和查询列表中的表达式一一对应。比方说上边的INSERT语句指定的列是`s, i`，对应于查询语句中的`second_column, first_column`。

#### INSERT IGNORE

对于一些是主键或者具有`UNIQUE`约束的列或者列组合来说，它们不允许重复值的出现，比如我们把`first_table`的`first_column`列添加一个`UNIQUE`约束：

```sql
mysql> ALTER TABLE first_table MODIFY COLUMN first_column INT UNIQUE;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql>
```

因为`first_column`列有了`UNIQUE`约束，所以如果待插入记录的`first_column`列值与已有的值重复的话就会报错，比如这样：

```scss
mysql> INSERT INTO first_table(first_column, second_column) VALUES(1, '哇哈哈');
ERROR 1062 (23000): Duplicate entry '1' for key 'first_column'
mysql>
```

可是这里有一个问题：我们在插入记录之前又不知道表里边有没有主键或者具有`UNIQUE`约束的列或者列组合重复的记录，所以我们迫切的需要这样的一个功能：对于那些是主键或者具有UNIQUE约束的列或者列组合来说，如果表中已存在的记录中没有与待插入记录在这些列或者列组合上重复的值，那么就把待插入记录插到表中，否则忽略此次插入操作。设计`MySQL`的大叔给我们提供了`INSERT IGNORE`的语法来实现这个功能：

```scss
mysql> INSERT IGNORE INTO first_table(first_column, second_column) VALUES(1, '哇哈哈') ;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql>
```

我们只是简单的在`INSERT`后边加了个`IGNORE`单词便不再报错了！对于批量插入的情况，`INSERT IGNORE`同样适用，比如这样：

```scss
mysql> INSERT IGNORE INTO first_table(first_column, second_column) VALUES(1, '哇哈哈'), (9, 'iii');
Query OK, 1 row affected, 1 warning (0.00 sec)
Records: 2  Duplicates: 1  Warnings: 1

mysql>
```

这个批量插入的语句中我们想插入`(1, '哇哈哈')`和`(9, 'iii')`这两条记录，因为`first_column`列值为`1`的记录已经在表中存在，所以这个记录会被忽略，而`(9, 'iii')`这条记录被插入成功，看插入效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | aaa           |
|            2 | NULL          |
|            3 | ccc           |
|            4 | ddd           |
|            5 | NULL          |
|         NULL | fff           |
|            7 | ggg           |
|            8 | hhh           |
|            9 | iii           |
+--------------+---------------+
9 rows in set (0.01 sec)

mysql>
```

#### INSERT ON DUPLICATE KEY UPDATE

对于主键或者有唯一性约束的列或列组合来说，新插入的记录如果和表中已存在的记录重复的话，我们可以选择的策略不仅仅是忽略该条记录的插入，也可以选择更新这条重复的旧记录。比如我们想在`first_table`表中插入一条记录，内容是`(1, '哇哈哈')`，我们想要的效果是：对于那些是主键或者具有UNIQUE约束的列或者列组合来说，如果表中已存在的记录中没有与待插入记录在这些列或者列组合上重复的值，那么就把待插入记录插到表中，否则按照规定去更新那条重复的记录中某些列的值。设计`MySQL`的大叔给我们提供了`INSERT ... ON DUPLICATE KEY UPDATE ...`的语法来实现这个功能：

```sql
mysql> INSERT INTO first_table (first_column, second_column) VALUES(1, '哇哈哈') ON DUPLICATE KEY UPDATE second_column = '雪碧';
Query OK, 2 rows affected (0.00 sec)

mysql>
```

这个语句的意思就是，对于要插入的数据`(1, '哇哈哈')`来说，如果`first_table`表中已经存在`first_column`的列值为`1`的记录（因为`first_column`列具有`UNIQUE`约束），那么就把该记录的`second_column`列更新为`'雪碧'`，看一下效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | 雪碧          |
|            2 | NULL          |
|            3 | ccc           |
|            4 | ddd           |
|            5 | NULL          |
|         NULL | fff           |
|            7 | ggg           |
|            8 | hhh           |
|            9 | iii           |
+--------------+---------------+
9 rows in set (0.00 sec)

mysql>
```

对于那些是主键或者具有UNIQUE约束的列或者列组合来说，如果表中已存在的记录中有与待插入记录在这些列或者列组合上重复的值，我们可以使用`VALUES(列名)`的形式来引用待插入记录中对应列的值，比方说下边这个INSERT语句：

```sql
mysql> INSERT INTO first_table (first_column, second_column) VALUES(1, '哇哈哈') ON DUPLICATE KEY UPDATE second_column = VALUES(second_column);
Query OK, 2 rows affected (0.00 sec)

mysql>
```

其中的`VALUES(second_column)`就代表着待插入记录中`second_column`的值，本例中就是`'哇哈哈'`。有的同学就呵呵了，我直接写成下边这种形式不好么：

```sql
INSERT INTO first_table (first_column, second_column) VALUES(1, '哇哈哈') ON DUPLICATE KEY UPDATE second_column = '哇哈哈';
```

是的，没有任何问题，但是在批量插入大量记录的时候该咋办呢？此时`VALUES(second_column)`就 派上了大用场：

```sql
mysql> INSERT INTO first_table (first_column, second_column) VALUES(2, '红牛'), (3, '橙汁儿') ON DUPLICATE KEY UPDATE second_column = VALUES(second_column);
Query OK, 4 rows affected (0.00 sec)
Records: 2  Duplicates: 2  Warnings: 0

mysql>
```

我们准备批量插入两条记录`(2, '红牛')`和`(3, '橙汁儿')`，在遇到重复记录时把该重复记录的`second_column`列更新成待插入记录中`second_column`列的值就好了，所以效果是这样：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | 哇哈哈        |
|            2 | 红牛          |
|            3 | 橙汁儿        |
|            4 | ddd           |
|            5 | NULL          |
|         NULL | fff           |
|            7 | ggg           |
|            8 | hhh           |
|            9 | iii           |
+--------------+---------------+
9 rows in set (0.00 sec)

mysql>
```

### 删除数据

如果某些记录我们不想要了，那可以使用下边的语句把它们给删除掉：

```sql
DELETE FROM 表名 [WHERE 表达式];
```

我们把`first_table`中`first_column`的值大于`4`的记录都删掉看看：

```sql
mysql> DELETE FROM first_table WHERE first_column > 4;
Query OK, 4 rows affected (0.00 sec)

mysql>
```

其中的`Query OK, 4 rows affected (0.00 sec)`表明成功的删除了4条记录，然后看一下删除效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | 哇哈哈        |
|            2 | 红牛          |
|            3 | 橙汁儿        |
|            4 | ddd           |
|         NULL | fff           |
+--------------+---------------+
5 rows in set (0.00 sec)

mysql>
```

`first_table`表中`first_column`列大于`4`的记录就都不见了哈～ 当然删除语句的`WHERE`子句是可选的，如果不加`WHERE`子句的话，意味着删除表中所有数据，比如我们想清除`second_table`表中的所有数据，可以这么写：

```sql
mysql> DELETE FROM second_table;
Query OK, 4 rows affected (0.01 sec)

mysql>
```

不过在使用删除语句需要特别特别注意：虽然删除语句的WHERE条件是可选的，但是如果不加WHERE条件的话将删除所有的记录，这是玩火的行为！超级危险！十分危险！请慎重使用。

另外，我们也可以使用`LIMIT`子句来限制想要删除掉的记录数量，使用`ORDER BY`子句来指定符合条件的记录的删除顺序，比方说这样：

```sql
mysql> DELETE FROM first_table ORDER BY first_column DESC LIMIT 1;
Query OK, 1 row affected (0.00 sec)

mysql>
```

上述语句就是想删除掉`first_column`列值最大的那条记录，我们看一下删除后的效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | 哇哈哈        |
|            2 | 红牛          |
|            3 | 橙汁儿        |
|         NULL | fff           |
+--------------+---------------+
4 rows in set (0.00 sec)

mysql>
```

可以看到`first_column`列值最大的那条记录，也就是`first_column`列值为`4`的那条记录已经被删除掉了。

### 更新数据

我们有时候对于某些记录的某些列的值不满意，需要去修改它们，修改记录的语法就是这样：

```ini
UPDATE 表名 SET 列1=值1, 列2=值2, ...,  列n=值n [WHERE 布尔表达式];
```

我们在`UPDATE`单词后边指定要更新的表，然后把你想更新的列的名称和该列更新后的值写到`SET`单词后边，如果想更新多个列的话，它们之间用逗号`,`分隔开。如果我们不指定`WHERE`子句，那么表中所有的记录都会被更新，否则的话只有符合`WHERE`子句中的条件的记录才可以被更新。比如我们把`first_table`表中`first_column`的值是`NULL`的记录的`first_column`的值更新为`5`，`second_column`的值更新为`'乳娃娃'`，可以这么写：

```sql
mysql> UPDATE first_table SET first_column = 5, second_column = '乳娃娃' WHERE first_column IS NULL;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql>
```

`Query OK, 1 row affected (0.01 sec)`就表明成功更新了1行数据。`Rows matched: 1`表示符合`WHERE`条件的记录一共有1条，`Changed: 1`表示有1条记录的内容发生了变化。我们看一下修改后的效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | 哇哈哈        |
|            2 | 红牛          |
|            3 | 橙汁儿        |
|            5 | 乳娃娃        |
+--------------+---------------+
4 rows in set (0.00 sec)

mysql>
```

这里再次强调一下：虽然更新语句的WHERE子句是可选的，但是如果不加WHERE子句的话将更新表中所有的记录，这是玩火的行为！超级危险！十分危险！请慎重使用。

另外，我们也可以使用`LIMIT`子句来限制想要更新的记录数量，使用`ORDER BY`子句来指定符合条件的记录的更新顺序，比方说这样：

```sql
mysql> UPDATE first_table SET second_column='爽歪歪' ORDER BY first_column DESC LIMIT 1;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql>
```

上述语句就是想删更新`first_column`列值最大的那条记录，我们看一下更新后的效果：

```sql
mysql> SELECT * FROM first_table;
+--------------+---------------+
| first_column | second_column |
+--------------+---------------+
|            1 | 哇哈哈        |
|            2 | 红牛          |
|            3 | 橙汁儿        |
|            5 | 爽歪歪        |
+--------------+---------------+
4 rows in set (0.00 sec)

mysql>
```

可以看到`first_column`列值最大的那条记录，也就是`first_column`列值为`5`的那条记录的`second_column`列的值已经被更新为`'爽歪歪'`了。

## 视图

### 创建视图

我们前边唠叨过如何书写连接查询语句，比方说下边这个语句：

```sql
mysql> SELECT s1.number, s1.name, s1.major, s2.subject, s2.score FROM student_info AS s1 INNER JOIN student_score AS s2 WHERE s1.number = s2.number AND s1.sex = '男';
+----------+-----------+--------------------------+-----------------------------+-------+
| number   | name      | major                    | subject                     | score |
+----------+-----------+--------------------------+-----------------------------+-------+
| 20180101 | 杜子腾    | 计算机科学与工程         | 母猪的产后护理              |    78 |
| 20180101 | 杜子腾    | 计算机科学与工程         | 论萨达姆的战争准备          |    88 |
| 20180103 | 范统      | 软件工程                 | 母猪的产后护理              |    59 |
| 20180103 | 范统      | 软件工程                 | 论萨达姆的战争准备          |    61 |
+----------+-----------+--------------------------+-----------------------------+-------+
4 rows in set (0.00 sec)

mysql>
```

我们查询出了一些男学生的基本信息和成绩信息，如果下次还想得到这些信息，我们就不得不把这个又臭又长的查询语句再敲一遍，不过设计`MySQL`的大叔们很贴心的为我们提供了一个称之为`视图`(英文名`VIEW`)的东东来帮助我们以很容易的方式来复用这些查询语句。

我们可以把`视图`理解为一个查询语句的别名，创建`视图`的语句如下：

```sql
CREATE VIEW 视图名 AS 查询语句
```

比如我们想根据上边那个又臭又长的查询语句来创建一个`视图`可以这么写：

```sql
mysql> CREATE VIEW male_student_view AS SELECT s1.number, s1.name, s1.major, s2.subject, s2.score FROM student_info AS s1 INNER JOIN student_score AS s2 WHERE s1.number = s2.number AND s1.sex = '男';
Query OK, 0 rows affected (0.02 sec)

mysql>
```

这样，这个名称为`male_student_view`的视图就代表了那一串又臭又长的查询语句了。

### 使用视图

`视图`也可以被称为`虚拟表`，因为我们可以对`视图`进行一些类似表的增删改查操作，只不过我们对视图的相关操作都会被映射到那个又臭又长的查询语句对应的底层的表上。那一串又臭又长的查询语句的查询列表可以被当作`视图`的`虚拟列`，比方说`male_student_view`这个视图对应的查询语句中的查询列表是`number`、`name`、`major`、`subject`、`score`，它们就可以被当作`male_student_view`视图的`虚拟列`。

我们平时怎么从真实表中查询信息，就可以怎么从`视图`中查询信息，比如这么写：

```sql
mysql> SELECT * FROM male_student_view;
+----------+-----------+--------------------------+-----------------------------+-------+
| number   | name      | major                    | subject                     | score |
+----------+-----------+--------------------------+-----------------------------+-------+
| 20180101 | 杜子腾    | 计算机科学与工程         | 母猪的产后护理              |    78 |
| 20180101 | 杜子腾    | 计算机科学与工程         | 论萨达姆的战争准备          |    88 |
| 20180103 | 范统      | 软件工程                 | 母猪的产后护理              |    59 |
| 20180103 | 范统      | 软件工程                 | 论萨达姆的战争准备          |    61 |
+----------+-----------+--------------------------+-----------------------------+-------+
4 rows in set (0.00 sec)

mysql>
```

这个查询语句的查询列表是`*`，这也就意味着`male_student_view`视图所代表的查询语句的结果集将作为本次查询的结果集。从这个例子中我们也可以看到，我们不再需要使用那条又臭又长的连接查询语句了，只需要从它对应的`视图`中查询即可。

当然，更复杂的一些查询语句也可以作用于视图，比方说这个语句：

```sql
mysql> SELECT subject, AVG(score) FROM male_student_view WHERE score > 60 GROUP BY subject HAVING AVG(score) > 75 LIMIT 1;
+-----------------------+------------+
| subject               | AVG(score) |
+-----------------------+------------+
| 母猪的产后护理        |    78.0000 |
+-----------------------+------------+
1 row in set (0.00 sec)

mysql>
```

我们再次强调一遍，`视图`其实就相当于是某个查询语句的别名！创建视图的时候并不会把那个又臭又长的查询语句的结果集维护在硬盘或者内存里！在对视图进行查询时，MySQL服务器将会帮助我们把对视图的查询语句转换为对底层表的查询语句然后再执行，比如说上边这个查询语句其实可以被转换成下边这个查询语句去执行：

```sql
SELECT subject, AVG(score) FROM student_info AS s1 INNER JOIN student_score AS s2 WHERE s1.number = s2.number AND s1.sex = '男' AND score > 60 GROUP BY subject HAVING AVG(score) > 75;
```

当然视图还可以参与一些更复杂的查询，比如子查询、连接查询什么的。有一点比较有趣的是，在书写查询语句时，视图还可以和真实表一起使用，比如这样：

```sql
mysql> SELECT * FROM male_student_view WHERE number IN (SELECT number FROM student_info WHERE major = '计算机科学与工程');
+----------+-----------+--------------------------+-----------------------------+-------+
| number   | name      | major                    | subject                     | score |
+----------+-----------+--------------------------+-----------------------------+-------+
| 20180101 | 杜子腾    | 计算机科学与工程         | 母猪的产后护理              |    78 |
| 20180101 | 杜子腾    | 计算机科学与工程         | 论萨达姆的战争准备          |    88 |
+----------+-----------+--------------------------+-----------------------------+-------+
2 rows in set (0.00 sec)

mysql>
```

所以在使用层面，我们完全可以把`视图`当作一个表去使用，但是它的实现原理却是在执行语句时转换为对底层表的操作。使用视图的好处也是显而易见的，视图可以简化语句的书写，避免了每次都要写一遍又臭又长的语句，而且对视图的操作更加直观，使用者也不用去考虑它的底层实现细节。

#### 利用视图来创建新视图

我们前边说视图是某个查询语句的别名，其实这个查询语句不仅可以从真实表中查询数据，也可以从另一个视图中查询数据，只要是个合法的查询语句就好了。比方说我们利用`male_student_view`视图来创建另一个新视图可以这么写：

```sql
mysql> CREATE VIEW by_view AS SELECT number, name, score FROM male_student_view;
Query OK, 0 rows affected (0.02 sec)

mysql>
```

我们使用一下这个依赖另一个视图而生成的新视图：

```sql
mysql> SELECT * FROM by_view;
+----------+-----------+-------+
| number   | name      | score |
+----------+-----------+-------+
| 20180101 | 杜子腾    |    78 |
| 20180101 | 杜子腾    |    88 |
| 20180103 | 范统      |    59 |
| 20180103 | 范统      |    61 |
+----------+-----------+-------+
4 rows in set (0.00 sec)

mysql>
```

在对这种依赖其他的视图而生成的新视图进行查询时，查询语句会先被转换成对它依赖的视图的查询，再转换成对底层表的查询。

#### 创建视图时指定自定义列名

我们前边说过`视图`的`虚拟列`其实是这个视图对应的查询语句的查询列表，我们也可以在创建视图的时候为它的`虚拟列`自定义列名，这些自定义列名写到视图名后边，用逗号`,`分隔就好了，不过需要注意的是，自定义列名一定要和查询列表中的表达式一一对应。比如我们新创建一个自定义列名的视图：

```sql
mysql> CREATE VIEW student_info_view(no, n, m) AS SELECT number, name, major FROM student_info;
Query OK, 0 rows affected (0.02 sec)

mysql>
```

我们的自定义列名列表是`no, n, m`，分别对应查询列表中的`number, name, major`。有了自定义列名之后，我们之后对视图的查询语句都要基于这些自定义列名，比如我们可以这么写查询语句：

```sql
mysql> SELECT no, n, m FROM student_info_view;
+----------+-----------+--------------------------+
| no       | n         | m                        |
+----------+-----------+--------------------------+
| 20180101 | 杜子腾    | 计算机科学与工程         |
| 20180102 | 杜琦燕    | 计算机科学与工程         |
| 20180103 | 范统      | 软件工程                 |
| 20180104 | 史珍香    | 软件工程                 |
| 20180105 | 范剑      | 飞行器设计               |
| 20180106 | 朱逸群    | 电子信息                 |
+----------+-----------+--------------------------+
6 rows in set (0.00 sec)

mysql>
```

如果仍旧使用与视图对应的查询语句的查询列表中的列名就会报错，比如这样：

```sql
mysql> SELECT number, name, major FROM student_info_view;
ERROR 1054 (42S22): Unknown column 'number' in 'field list'
mysql>
```

### 查看和删除视图

#### 查看有哪些视图

我们创建视图时默认是将其放在当前数据库下的，如果我们想查看当前数据库中有哪些视图的话，其实和查看有哪些表的命令是一样的：

```sql
mysql> SHOW TABLES;
+---------------------+
| Tables_in_xiaohaizi |
+---------------------+
| by_view             |
| first_table         |
| male_student_view   |
| second_table        |
| student_info        |
| student_info_view   |
| student_score       |
| t                   |
| t1                  |
| t2                  |
| t3                  |
| zero_table          |
+---------------------+
12 rows in set (0.00 sec)

mysql>
```

可以看到，我们创建的几个视图，包括`by_view`、`male_student_view`、`student_info_view`就都显示出来了。需要注意的是，因为视图是一张`虚拟表`，所以新创建的视图的名称不能和当前数据库中的其他视图或者表的名称冲突！

### 查看视图的定义

视图是一张`虚拟表`，用来查看视图结构的语句和用来查看表结构的语句比较类似，是这样的：

```sql
SHOW CREATE VIEW 视图名;
```

比如我们来查看一下`student_info_view`视图的结构可以这样写：

```markdown
mysql> SHOW CREATE VIEW student_info_view\G
*************************** 1. row ***************************
                View: student_info_view
         Create View: CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `student_info_view` AS select `student_info`.`number` AS `no`,`student_info`.`name` AS `n`,`student_info`.`major` AS `m` from `student_info`
character_set_client: utf8
collation_connection: utf8_general_ci
1 row in set (0.00 sec)

mysql>
小贴士：

注意到我们查询出来的视图结构中多了很多信息，比方说ALGORITHM=UNDEFINED、DEFINER=`root`@`localhost`、SQL SECURITY DEFINER等等等等，这些信息我们目前不关心，大家主动跳过就好了，等各位羽翼丰满了之后可以到MySQL文档中查看这些信息都代表啥意思。
```

#### 可更新的视图

我们前边唠叨的都是对视图的查询操作，其实有些视图是可更新的，也就是在视图上执行`INSERT`、`DELETE`、`UPDATE`语句。对视图执行INSERT、DELETE、UPDATE语句的本质上是对该视图对应的底层表中的数据进行增、删、改操作。比方说视图`student_info_view`的底层表是`student_info`，所以如果我们对`student_info_view`执行INSERT、DELETE、UPDATE语句就相当于对`student_info`表进行INSERT、DELETE、UPDATE语句，比方说我们执行这个语句：

```sql
mysql> UPDATE student_info_view SET n = '111' WHERE no = 20180101;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql>
```

我们再到`student_info`表中看一下这个学生的名称是否被改了：

```sql
mysql> SELECT name FROM student_info WHERE number = 20180101;
+------+
| name |
+------+
| 111  |
+------+
1 row in set (0.00 sec)

mysql>
```

名称的确被更改成功了！

不过并不是可以在所有的视图上执行更新语句的，在生成视图的时候使用了下边这些语句的都不能进行更新：

- 聚集函数（比如SUM(), MIN(), MAX(), COUNT()等等）
- DISTINCT
- GROUP BY
- HAVING
- UNION 或者 UNION ALL
- 某些子查询
- 某些连接查询
- 等等等等

我们这里对这些限制条件并不准备展开来说，因为这会引入更多复杂的东西，对于作为小白的我们来说，一般我们只在查询语句里使用视图，而不在INSERT、DELETE、UPDATE语句里使用视图！这里介绍对可更新的视图只是为了语法的完整性，并不是建议大家在实际使用过程中使用此功能。

#### 删除视图

如果某个视图我们不想要了，可以使用这个语句来删除掉它：

```sql
DROP VIEW 视图名
```

比如我们把`by_view`视图删掉可以这么写：

```sql
mysql> DROP VIEW by_view;
Query OK, 0 rows affected (0.00 sec)

mysql>
```

然后再查看当前数据库中的表和视图：

```sql
mysql> SHOW TABLES;
+---------------------+
| Tables_in_xiaohaizi |
+---------------------+
| first_table         |
| male_student_view   |
| second_table        |
| student_info        |
| student_info_view   |
| student_score       |
| t                   |
| t1                  |
| t2                  |
| t3                  |
| zero_table          |
+---------------------+
11 rows in set (0.00 sec)

mysql>
```

这个`by_view`视图就不见了！

## 自定义变量和语句结束分隔符

标签： MySQL是怎样使用的新版

------

### 存储程序

有时候为了完成一个常用的功能需要执行许多条语句，每次都在客户端里一条一条的去输入这么多语句是很烦的。设计`MySQL`的大叔非常贴心的给我们提供了一种称之为`存储程序`的东东，这个所谓的`存储程序`可以封装一些语句，然后给用户提供一种简单的方式来调用这个存储程序，从而间接地执行这些语句。根据调用方式的不同，我们可以把`存储程序`分为`存储例程`、`触发器`和`事件`这几种类型。其中，`存储例程`又可以被细分为`存储函数`和`存储过程`。我们画个图表示一下：

![image-20230314002606337](..\images\stroe.png)

别看出现了很多陌生的概念，别怕，我们后边会各个击破的。不过在正式介绍`存储程序`之前，我们需要先了解一下`MySQL`中的自定义变量和语句结束分隔符的概念。

### 自定义变量简介

生活中我们经常会遇到一些固定不变的值，比如数字`100`、字符串`'你好呀'`，我们把这些值固定不变的东东称之为`常量`。可是有时候为了方便，我们会使用某一个符号来代表一个值，它代表的值是可以变化的。比方说我们规定符号`a`代表数字`1`，之后我们又可以让符号`a`代表数字`2`，我们把这种值可以发生变化的东东称之为`变量`，其中符号`a`就称为这个变量的`变量名`。在`MySQL`中，我们可以通过`SET`语句来自定义一些我们自己的变量，比方说这样：

```ini
mysql> SET @a = 1;
Query OK, 0 rows affected (0.00 sec)

mysql>
```

上边的语句就表明我们定义了一个称之为`a`的变量，并且把整数`1`赋值给了这个变量。不过大家需要注意一下，设计MySQL的大叔规定，在我们的自定义变量前边必须加一个`@`符号（虽然有点儿怪，但这就是人家规定的，大家遵守就好了）。

如果我们之后想查看这个变量的值的话，使用`SELECT`语句就好了，不过仍然需要在变量名称前加一个`@`符号：

```sql
mysql> SELECT @a;
+------+
| @a   |
+------+
|    1 |
+------+
1 row in set (0.00 sec)

mysql>
```

同一个变量也可以存储存储不同类型的值，比方说我们再把一个字符串值赋值给变量`a`：

```sql
mysql> SET @a = '哈哈哈';
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT @a;
+-----------+
| @a        |
+-----------+
| 哈哈哈    |
+-----------+
1 row in set (0.00 sec)

mysql>
```

除了把一个常量赋值给一个变量以外，我们还可以把一个变量赋值给另一个变量：

```sql
mysql> SET @b = @a;
Query OK, 0 rows affected (0.00 sec)

mysql> select @b;
+-----------+
| @b        |
+-----------+
| 哈哈哈    |
+-----------+
1 row in set (0.00 sec)

mysql>
```

这样变量`a`和`b`就有了相同的值`'哇哈哈'`！

我们还可以将某个查询的结果赋值给一个变量，前提是这个查询的结果只有一个值：

```sql
mysql> SET @a = (SELECT m1 FROM t1 LIMIT 1);
Query OK, 0 rows affected (0.00 sec)

mysql>
```

还可以用另一种形式的语句来将查询的结果赋值给一个变量：

```sql
mysql> SELECT n1 FROM t1 LIMIT 1 INTO @b;
Query OK, 1 row affected (0.00 sec)

mysql>
```

因为语句`SELECT m1 FROM t1 LIMIT 1`和`SELECT n1 FROM t1 LIMIT 1`的查询结果都只有一个值，所以它们可以直接赋值给变量`a`或者`b`。我们查看一下这两个变量的值：

```sql
mysql> SELECT @a, @b;
+------+------+
| @a   | @b   |
+------+------+
|    1 | a    |
+------+------+
1 row in set (0.00 sec)

mysql>
```

如果我们的查询结果是一条记录，该记录中有多个列的值的话，我们想把这几个值分别赋值到不同的变量中，只能使用`INTO`语句了：

```sql
mysql> SELECT m1, n1 FROM t1 LIMIT 1 INTO @a, @b;
Query OK, 1 row affected (0.00 sec)

mysql>
```

这条查询语句的结果集中只包含一条记录，我们把这条记录的`m1`列的值赋值到了变量`a`中，`n1`列的值赋值到了变量`b`中。

### 语句结束分隔符

在`MySQL`客户端的交互界面处，当我们完成键盘输入并按下回车键时，`MySQL`客户端会检测我们输入的内容中是否包含`;`、`\g`或者`\G`这三个符号之一，如果有的话，会把我们输入的内容发送到服务器。这样一来，如果我们想一次性给服务器发送多条的话，就需要把这些语句写到一行中，比如这样：

```sql
mysql> SELECT * FROM t1 LIMIT 1;SELECT * FROM t2 LIMIT 1;SELECT * FROM t3 LIMIT 1;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
+------+------+
1 row in set (0.00 sec)

+------+------+
| m2   | n2   |
+------+------+
|    2 | b    |
+------+------+
1 row in set (0.00 sec)

+------+------+
| m3   | n3   |
+------+------+
|    3 | c    |
+------+------+
1 row in set (0.00 sec)

mysql>
```

造成这一不便的原因在于，`MySQL`客户端检测输入结束用的符号和分隔各个语句的符号是一样的！其实我们也可以用`delimiter`命令来自定义`MySQL`的检测语句输入结束的符号，也就是所谓的`语句结束分隔符`，比如这样：

```sql
mysql> delimiter $
mysql> SELECT * FROM t1 LIMIT 1;
    -> SELECT * FROM t2 LIMIT 1;
    -> SELECT * FROM t3 LIMIT 1;
    -> $
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
+------+------+
1 row in set (0.00 sec)

+------+------+
| m2   | n2   |
+------+------+
|    2 | b    |
+------+------+
1 row in set (0.00 sec)

+------+------+
| m3   | n3   |
+------+------+
|    3 | c    |
+------+------+
1 row in set (0.00 sec)

mysql>
```

`delimiter $`命令意味着修改语句结束分隔符为`$`，也就是说之后`MySQL`客户端检测用户语句输入结束的符号为`$`。上边例子中我们虽然连续输入了3个以分号`;`结尾的查询语句并且按了回车键，但是输入的内容并没有被提交，直到敲下`$`符号并回车，`MySQL`客户端才会将我们输入的内容提交到服务器，此时我们输入的内容里已经包含了3个独立的查询语句了，所以返回了3个结果集。

我们也可以将`语句结束分隔符`重新定义为`$`以外的其他包含单个或多个字符的字符串，比方说这样：

```sql
mysql> delimiter EOF
mysql> SELECT * FROM t1 LIMIT 1;
    -> SELECT * FROM t2 LIMIT 1;
    -> SELECT * FROM t3 LIMIT 1;
    -> EOF
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
+------+------+
1 row in set (0.00 sec)

+------+------+
| m2   | n2   |
+------+------+
|    2 | b    |
+------+------+
1 row in set (0.00 sec)

+------+------+
| m3   | n3   |
+------+------+
|    3 | c    |
+------+------+
1 row in set (0.00 sec)

mysql>
```

我们这里采用了`EOF`作为`MySQL`客户端检测输入结束的符号，是不是很easy啊！当然，这个只是为了方便我们一次性输入多个语句，在输入完成之后最好还是改回我们常用的分号`;`吧：

```ini
mysql> delimiter ;
小贴士：

我们应该避免使用反斜杠（\）字符作为语句结束分隔符，因为这是MySQL的转义字符。
```

## 存储函数和存储过程

标签： MySQL是怎样使用的新版

------

我们前边说可以将某个常用功能对应的的一些语句封装成一个所谓的`存储程序`，之后只要调用这个存储程序就可以完成这个常用功能，省去了我们每次都要写好多语句的麻烦。`存储程序`可以被分为`存储例程`、`触发器`、`事件`这几种类型，其中`存储例程`需要我们去手动调用，而`触发器`和`事件`都是`MySQL`服务器在特定条件下自己调用的。本章我们就来看一下`存储例程`的各种细节，而`存储例程`又可以分为`存储函数`和`存储过程`，下边我们详细唠叨这两个家伙。

```!
小贴士：

各位小伙伴有没有被绕晕，我们前边画过一副这些名词儿的关系图，有被绕晕的话赶紧去看看治疗一下呗～
```

### 存储函数

#### 创建存储函数

`存储函数`其实就是一种`函数`，只不过在这个函数里可以执行`MySQL`的语句而已。`函数`的概念大家都应该不陌生，它可以把处理某个问题的过程封装起来，之后我们直接调用函数就可以去解决这个问题了，简单方便又环保。`MySQL`中定义`存储函数`的语句如下：

```sql
CREATE FUNCTION 存储函数名称([参数列表])
RETURNS 返回值类型
BEGIN
    函数体内容
END
```

从这里我们可以看出，定义一个`存储函数`需要指定函数名称、参数列表、返回值类型以及函数体内容。如果该函数不需要参数，那参数列表可以被省略，函数体内容可以包括一条或多条语句，每条语句都要以分号`;`结尾。上边语句中的制表符和换行仅仅是为了好看，如果你觉得烦，完全可以把存储函数的定义都写在一行里，用一个或多个空格把上述几个部分分隔开就好！ 光看定义理解的不深刻，我们先写一个`存储函数`开开眼：

```sql
mysql> delimiter $
mysql> CREATE FUNCTION avg_score(s VARCHAR(100))
    -> RETURNS DOUBLE
    -> BEGIN
    ->     RETURN (SELECT AVG(score) FROM student_score WHERE subject = s);
    -> END $
Query OK, 0 rows affected (0.00 sec)

mysql> delimiter ;
```

我们定义了一个名叫`avg_score`的函数，它接收一个`VARCHAR(100)`类型的参数，声明的返回值类型是`DOUBLE`，需要注意的是，我们在`RETURN`语句后边写了一个`SELECT`语句，表明这个函数的返回结果就是根据这个查询语句产生的，也就是返回了指定科目的平均成绩。

#### 存储函数的调用

我们自定义的函数和系统内置函数的使用方式是一样的，都是在函数名后加小括号`()`表示函数调用，调用有参数的函数时可以把参数写到小括号里边。函数调用可以放到查询列表或者作为搜索条件，或者和别的操作数一起组成更复杂的表达式，我们现在来调用一下刚刚写好的这个名为`avg_score`的函数吧：

```sql
mysql> SELECT avg_score('母猪的产后护理');
+------------------------------------+
| avg_score('母猪的产后护理')        |
+------------------------------------+
|                                 73 |
+------------------------------------+
1 row in set (0.00 sec)

mysql> SELECT avg_score('论萨达姆的战争准备');
+------------------------------------------+
| avg_score('论萨达姆的战争准备')          |
+------------------------------------------+
|                                    73.25 |
+------------------------------------------+
1 row in set (0.00 sec)

mysql>
```

通过调用函数的方式而不是直接写查询语句的方式来获取某门科目的平均成绩看起来就简介多了。

#### 查看和删除存储函数

如果我们想查看我们已经定义了多少个存储函数，可以使用下边这个语句：

```sql
SHOW FUNCTION STATUS [LIKE 需要匹配的函数名]
```

由于这个命令得到的结果太多，我们就不演示了哈，大家可以自己试试。如果我们想查看某个函数的具体是怎么定义的，可以使用这个语句：

```sql
SHOW CREATE FUNCTION 函数名
```

比如这样：

```sql
mysql> SHOW CREATE FUNCTION avg_score\G
*************************** 1. row ***************************
            Function: avg_score
            sql_mode: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
     Create Function: CREATE DEFINER=`root`@`localhost` FUNCTION `avg_score`(s VARCHAR(100)) RETURNS double
BEGIN
        RETURN (SELECT AVG(score) FROM student_score WHERE subject = s);
    END
character_set_client: utf8
collation_connection: utf8_general_ci
  Database Collation: utf8_general_ci
1 row in set (0.01 sec)

mysql>
```

虽然展示出很多内容，但是我们只要聚焦于名叫`Create Function`的那部分信息，该部分信息展示了这个存储函数的定义语句是什么样的（可以看到`MySQL`服务器为我们自动添加了`DEFINER=`root`@`localhost``，大家可以把这个内容先忽略掉）。

如果想删除某个存储函数，使用这个语句：

```sql
DROP FUNCTION 函数名
```

比如我们来删掉`avg_score`这个函数：

```sql
mysql> DROP FUNCTION avg_score;
Query OK, 0 rows affected (0.00 sec)

mysql>
```

什么？你以为到这里`存储函数`就唠叨完了么？那怎么可能～ 到现在为止我们只是勾勒出一个`存储函数`的大致轮廓，下边我们来详细说一下`MySQL`定义函数体时支持的一些语句。

#### 函数体的定义

上边定义的`avg_score`的函数体里边只包含一条语句，如果只为了节省书写一条语句的时间而定义一个存储函数，其实也不是很值～ 其实存储函数的函数体中可以包含多条语句，并且支持一些特殊的语法来供我们使用，下边一起看看呗～

##### 在函数体中定义局部变量

我们在前边说过使用`SET`语句来自定义变量的方式，可以不用声明就为变量赋值。而在存储函数的函数体中使用变量前必须先声明这个变量，声明方式如下：

```ini
DECLARE 变量名1, 变量名2, ... 数据类型 [DEFAULT 默认值];
```

这些在函数体内声明的变量只在该函数体内有用，当存储函数执行完成后，就不能访问到这些变量了，所以这些变量也被称为`局部`变量。我们可以在一条语句中声明多个相同数据类型的变量。不过需要特别留心的是，函数体中的局部变量名不允许加`@`前缀，这一点和我们之前直接使用`SET`语句自定义变量的方式是截然不同的，特别注意一下。在声明了这个局部变量之后，才可以使用它，就像这样：

```sql
mysql> delimiter $;
mysql> CREATE FUNCTION var_demo()
-> RETURNS INT
-> BEGIN
->     DECLARE c INT;
->     SET c = 5;
->     RETURN c;
-> END $
Query OK, 0 rows affected (0.00 sec)

mysql> delimiter ;
```

我们定义了一个名叫`var_demo`而且不需要参数的函数，然后在函数体中声明了一个名称为`c`的`INT`类型的局部变量，之后我们调用`SET`语句为这个局部变量赋值了整数`5`，并且把局部变量`c`当作函数结果返回。我们调用一下这个函数：

```sql
mysql> select var_demo();
+------------+
| var_demo() |
+------------+
|          5 |
+------------+
1 row in set (0.00 sec)

mysql>
```

如果我们不对声明的局部变量赋值的话，它的默认值就是`NULL`，当然我们也可以通过`DEFAULT`子句来显式的指定局部变量的默认值，比如这样：

```sql
mysql> delimiter $
mysql> CREATE FUNCTION var_default_demo()
-> RETURNS INT
-> BEGIN
->     DECLARE c INT DEFAULT 1;
->     RETURN c;
-> END $
Query OK, 0 rows affected (0.00 sec)

mysql> delimiter ;
mysql>
```

在新创建的这个`var_default_demo`函数中，我们声明了一个局部变量`c`，并且指定了它的默认值为`1`，然后看一下该函数的调用结果：

```sql
mysql> SELECT var_default_demo();
+--------------------+
| var_default_demo() |
+--------------------+
|                  1 |
+--------------------+
1 row in set (0.00 sec)

mysql>
```

得到的结果是`1`，说明了我们指定的局部变量默认值生效了！另外，特别需要注意一下我们可以将某个查询语句的结果赋值给局部变量的情况，比如我们改写一下前边的`avg_score`函数：

```sql
CREATE FUNCTION avg_score(s VARCHAR(100))
RETURNS DOUBLE
BEGIN
    DECLARE a DOUBLE;
    SET a = (SELECT AVG(score) FROM student_score WHERE subject = s);
    return a;
END
```

我们先把一个查询语句的结果赋值给了局部变量`a`，然后再返回了这个变量。

```!
小贴士：

在存储函数的函数体中，DECLARE语句必须放到其他语句的前边。
```

##### 在函数体中使用自定义变量

除了局部变量外，也可以在函数体中使用我们之前用过的自定义变量，比方说这样：

```sql
mysql> delimiter $
mysql>
mysql> CREATE FUNCTION user_defined_var_demo()
    -> RETURNS INT
    -> BEGIN
    ->     SET @abc = 10;
    ->     return @abc;
    -> END $
Query OK, 0 rows affected (0.00 sec)

mysql>
mysql> delimiter ;
mysql>
```

我们定义了一个名叫`user_defined_var_demo`的存储函数，函数体内直接使用了自定义变量`abc`，我们来调用一下这个函数：

```sql
mysql> SELECT user_defined_var_demo();
+-------------------------+
| user_defined_var_demo() |
+-------------------------+
|                      10 |
+-------------------------+
1 row in set (0.01 sec)

mysql>
```

虽然现在存储函数执行完了，但是由于在该函数执行过程中为自定义变量`abc`赋值了，那么在该函数执行完之后我们仍然可以访问到该自定义变量的值，就像这样：

```sql
mysql> SELECT @abc;
+------+
| @abc |
+------+
|   10 |
+------+
1 row in set (0.00 sec)

mysql>
```

这一点和在函数体中使用`DECLARE`声明的局部变量有明显区别，大家注意一下。

##### 存储函数的参数

在定义存储函数的时候，可以指定多个参数，每个参数都要指定对应的数据类型，就像这样：

```
参数名 数据类型
```

比如我们上边编写的这个`avg_score`函数：

```sql
CREATE FUNCTION avg_score(s VARCHAR(100))
RETURNS DOUBLE
BEGIN
    RETURN (SELECT AVG(score) FROM student_score WHERE subject = s);
END
```

这个函数只需要一个类型为`VARCHAR(100)`参数，我们这里给这个参数起的名称是`s`，不过这个参数名不要和函数体语句中的其他变量名、列名啥的冲突，比如上边的例子中如果把变量名`s`改为为`subject`，它就与下边用到`WHERE`子句中的列名冲突了。

另外，函数参数不可以指定默认值，我们在调用函数的时候，必须显式的指定所有的参数，并且参数类型也一定要匹配，比方说我们在调用函数`avg_score`时，必须指定我们要查询的课程名，不然会报错的：

```scss
mysql> select avg_score();
ERROR 1318 (42000): Incorrect number of arguments for FUNCTION xiaohaizi.avg_score; expected 1, got 0
mysql>
```

##### 判断语句的编写

像其他的编程语言一样，在存储函数的函数体里也可以使用判断的语句，语法格式如下：

```ini
IF 表达式 THEN
    处理语句列表
[ELSEIF 表达式 THEN
    处理语句列表]
... # 这里可以有多个ELSEIF语句
[ELSE
    处理语句列表]
END IF;
```

其中`处理语句列表`中可以包含多条语句，每条语句以分号`;`结尾就好。

我们举一个包含`IF`语句的存储函数的例子：

```sql
mysql> delimiter $
mysql> CREATE FUNCTION condition_demo(i INT)
-> RETURNS VARCHAR(10)
-> BEGIN
->     DECLARE result VARCHAR(10);
->     IF i = 1 THEN
->         SET result = '结果是1';
->     ELSEIF i = 2 THEN
->         SET result = '结果是2';
->     ELSEIF i = 3 THEN
->         SET result = '结果是3';
->     ELSE
->         SET result = '非法参数';
->     END IF;
->     RETURN result;
-> END $
Query OK, 0 rows affected (0.00 sec)

mysql> delimiter ;
mysql>
```

在我们定义的函数`condition_demo`中，它接收一个`INT`类型的参数，这个函数的处理逻辑如下：

1. 如果这个参数的值是`1`，就把`result`变量的值设置为`'结果是1'`。
2. 否则如果这个这个参数的值是`2`，就把`result`变量的值设置为`'结果是2'`。
3. 否则如果这个这个参数的值是`3`，就把`result`变量的值设置为`'结果是3'`。
4. 否则就把`result`变量的值设置为`'非法参数'`。

当然了，我们举的这个例子还是比较白痴的啦，只是为了说明语法怎么用而已。我们现在调用一下这个函数：

```sql
mysql> SELECT condition_demo(2);
+-------------------+
| condition_demo(2) |
+-------------------+
| 结果是2           |
+-------------------+
1 row in set (0.00 sec)

mysql> SELECT condition_demo(5);
+-------------------+
| condition_demo(5) |
+-------------------+
| 非法参数          |
+-------------------+
1 row in set (0.00 sec)

mysql>
```

##### 循环语句的编写

除了判断语句，`MySQL`还支持循环语句的编写，不过提供了3种形式的循环语句，我们一一道来：

- `WHILE`循环语句：

  ```vbnet
  WHILE 表达式 DO
      处理语句列表
  END WHILE;
  ```

  这个语句的意思是：如果满足给定的表达式，则执行处理语句，否则退出循环。比如我们想定义一个计算从`1`到`n`这`n`个数的和（假设`n`大于`0`）的存储函数，可以这么写：

  ```sql
  mysql> delimiter $
  mysql> CREATE FUNCTION sum_all(n INT UNSIGNED)
  -> RETURNS INT
  -> BEGIN
  ->     DECLARE result INT DEFAULT 0;
  ->     DECLARE i INT DEFAULT 1;
  ->     WHILE i <= n DO
  ->         SET result = result + i;
  ->         SET i = i + 1;
  ->     END WHILE;
  ->     RETURN result;
  -> END $
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> delimiter ;
  mysql>
  ```

  在函数`sum_all`中，我们接收一个`INT UNSIGNED`类型的参数，声明了两个`INT`类型的变量`i`和`result`。我们先测试一下这个函数：

  ```sql
  mysql> SELECT sum_all(3);
  +------------+
  | sum_all(3) |
  +------------+
  |          6 |
  +------------+
  1 row in set (0.00 sec)
  
  mysql>
  ```

  分析一下这个结果是怎么产生的，初始的情况下`result`的值默认是`0`，`i`的值默认是`1`，给定的参数`n`的值是`3`。这个存储函数的运行过程就是：

  1. 先判断`i <= n`是否成立，也就是`1 <= 3`是否成立，显然成立，然后执行处理语句，将`result`的值设置为`1`（`result + i` = `0 + 1`），`i`的值设置为`2`（`i + 1` = `1 + 1`）。
  2. 再判断`i <= n`是否成立，也就是`2 <= 3`是否成立，显然成立，然后执行处理语句，将`result`的值设置为`3`（`result + i` = `1 + 2`），`i`的值设置为`3`（`i + 1` = `2 + 1`）。
  3. 再判断`i <= n`是否成立，也就是`3 <= 3`是否成立，显然成立，然后执行处理语句，将`result`的值设置为`6`（`result + i` = `3 + 3`），`i`的值设置为`4`（`i + 1` = `3 + 1`）。
  4. 再判断`i <= n`是否成立，也就是`4 <= 3`是否成立，显然不成立，退出循环。

  所以最后返回的`result`的值就是`6`，也就是`1`、`2`、`3`这三个数的和。

- `REPEAT`循环语句

  `REPEAT`循环语句和`WHILE`循环语句类似，只是形式上变了一下：

  ```scss
  REPEAT
      处理语句列表
  UNTIL 表达式 END REPEAT;
  ```

  先执行处理语句，再判断`表达式`是否成立，如果成立则退出循环，否则继续执行处理语句。与`WHILE`循环语句不同的一点是：WHILE循环语句先判断表达式的值，再执行处理语句，REPEAT循环语句先执行处理语句，再判断表达式的值，所以至少执行一次处理语句，所以如果`sum_all`函数用`REPEAT`循环改写，可以写成这样：

  ```sql
  CREATE FUNCTION sum_all(n INT UNSIGNED)
  RETURNS INT
  BEGIN
      DECLARE result INT DEFAULT 0;
      DECLARE i INT DEFAULT 1;
      REPEAT
          SET result = result + i;
          SET i = i + 1;
      UNTIL i > n END REPEAT;
      RETURN result;
  END
  ```

- `LOOP`循环语句

  这只是另一种形式的循环语句：

  ```vbnet
  LOOP
      处理语句列表
  END LOOP;
  ```

  不过这种循环语句有一点比较奇特，它没有判断循环终止的条件？那这个循环语句怎么停止下来呢？其实可以把循环终止的条件写到处理语句列表中然后使用`RETURN`语句直接让函数结束就可以达到停止循环的效果，比方说我们可以这样改写`sum_all`函数：

  ```sql
  CREATE FUNCTION sum_all(n INT UNSIGNED)
  RETURNS INT
  BEGIN
      DECLARE result INT DEFAULT 0;
      DECLARE i INT DEFAULT 1;
      LOOP
          IF i > n THEN
              RETURN result;
          END IF;
          SET result = result + i;
          SET i = i + 1;
      END LOOP;
  END
  ```

  如果我们仅仅想结束循环，而不是使用`RETURN`语句直接将函数返回，那么可以使用`LEAVE`语句。不过使用`LEAVE`时，需要先在`LOOP`语句前边放置一个所谓的`标记`，比方说我们使用`LEAVE`语句再改写`sum_all`函数：

  ```sql
  CREATE FUNCTION sum_all(n INT UNSIGNED)
  RETURNS INT
  BEGIN
      DECLARE result INT DEFAULT 0;
      DECLARE i INT DEFAULT 1;
      flag:LOOP
          IF i > n THEN
              LEAVE flag;
          END IF;
          SET result = result + i;
          SET i = i + 1;
      END LOOP flag;
      RETURN result;
  END
  ```

  可以看到，我们在`LOOP`语句前加了一个`flag:`这样的东东，相当于为这个循环打了一个名叫`flag`的标记，然后在对应的`END LOOP`语句后边也把这个标记名`flag`给写上了。在存储函数的函数体中使用`LEAVE flag`语句来结束`flag`这个标记所代表的循环。

  ```!
  小贴士：
  
  其实也可以在BEGIN ... END、REPEAT和WHILE这些语句上打标记，标记主要是为了在这些语句发生嵌套时可以跳到指定的语句中使用的。
  ```

### 存储过程

#### 创建存储过程

`存储函数`和`存储过程`都属于`存储例程`，都是对某些语句的一个封装。`存储函数`侧重于执行这些语句并返回一个值，而`存储过程`更侧重于单纯的去执行这些语句。先看一下`存储过程`的定义语句：

```sql
CREATE PROCEDURE 存储过程名称([参数列表])
BEGIN
    需要执行的语句
END
```

与`存储函数`最直观的不同点就是，`存储过程`的定义不需要声明`返回值类型`。我们先定义一个`存储过程`看看：

```sql
mysql> delimiter $
mysql> CREATE PROCEDURE t1_operation(
    ->     m1_value INT,
    ->     n1_value CHAR(1)
    -> )
    -> BEGIN
    ->     SELECT * FROM t1;
    ->     INSERT INTO t1(m1, n1) VALUES(m1_value, n1_value);
    ->     SELECT * FROM t1;
    -> END $
Query OK, 0 rows affected (0.00 sec)

mysql> delimiter ;
mysql>
```

我们建立了一个名叫`t1_operation`的存储过程，它接收两个参数，一个是`INT`类型的，一个是`CHAR(1)`类型的。这个存储过程做了3件事儿，一件是查询一下`t1`表中的数据，第二件是根据接收的参数来向`t1`表中插入一条语句，第三件是再次查询一下`t1`表中的数据。

#### 存储过程的调用

`存储函数`执行语句并返回一个值，所以常用在表达式中。`存储过程`偏向于执行某些语句，并不能用在表达式中，我们需要显式的使用`CALL`语句来调用一个`存储过程`：

```ini
CALL 存储过程([参数列表]);
```

比方说我们调用一下`t1_operation`存储过程可以这么写：

```sql
mysql> CALL t1_operation(4, 'd');
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
+------+------+
3 rows in set (0.00 sec)

+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
4 rows in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

mysql>
```

从执行结果中可以看到，存储过程在执行中产生的所有结果集，全部将会被显示到客户端。

```!
小贴士：

只有查询语句才会产生结果集，其他语句是不产生结果集的。
```

#### 查看和删除存储过程

与`存储函数`类似，`存储过程`也有相似的查看和删除语句，我们下边只列举一下相关语句，就不举例子了。

查看当前数据库中创建的`存储过程`都有哪些的语句：

```sql
SHOW PROCEDURE STATUS [LIKE 需要匹配的存储过程名称]
```

查看某个`存储过程`具体是怎么定义的语句：

```sql
SHOW CREATE PROCEDURE 存储过程名称
```

删除`存储过程`的语句：

```sql
DROP PROCEDURE 存储过程名称
```

#### 存储过程中的语句

上边在唠叨`存储函数`中使用到的各种语句，包括变量的使用、判断、循环结构都可以被用在`存储过程`中，这里就不再赘述了。

#### 存储过程的参数前缀

比`存储函数`强大的一点是，`存储过程`在定义参数的时候可以选择添加一些前缀，就像是这个样子：

```sql
参数类型 [IN | OUT | INOUT] 参数名 数据类型
```

可以看到可选的前缀有下边3种：

| 前缀    | 实际参数是否必须是变量 | 描述                                                         |
| ------- | ---------------------- | ------------------------------------------------------------ |
| `IN`    | 否                     | 用于调用者向存储过程传递数据，如果IN参数在过程中被修改，调用者不可见。 |
| `OUT`   | 是                     | 用于把存储过程运行过程中产生的数据赋值给OUT参数，存储过程执行结束后，调用者可以访问到OUT参数。 |
| `INOUT` | 是                     | 综合`IN`和`OUT`的特点，既可以用于调用者向存储过程传递数据，也可以用于存放存储过程中产生的数据以供调用者使用。 |



这么直接描述有些生硬哈，我们来举例子分别仔细分析一下：

- `IN`参数

  先定义一个参数前缀是`IN`的存储过程`p_in`：

  ```shell
  mysql> delimiter $
  mysql> CREATE PROCEDURE p_in (
  ->     IN arg INT
  -> )
  -> BEGIN
  ->     SELECT arg;
  ->     SET arg = 123;
  -> END $
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> delimiter ;
  mysql>
  ```

  这个`p_in`存储过程只有一个参数`arg`，它的前缀是`IN`。这个存储过程实际执行两个语句，第一个语句是用来读取参数`arg`的值，第二个语句是给参数`arg`赋值。我们调用一下`p_in`：

  ```sql
  mysql> SET @a = 1;
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> CALL p_in(@a);
  +------+
  | arg  |
  +------+
  |    1 |
  +------+
  1 row in set (0.00 sec)
  
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> SELECT @a;
  +------+
  | @a   |
  +------+
  |    1 |
  +------+
  1 row in set (0.00 sec)
  
  mysql>
  ```

  我们定义了一个变量`a`并把整数`1`赋值赋值给它，因为它是在客户端定义的，所以需要加`@`前缀，然后把它当作参数传给`p_in`存储过程。从结果中可以看出，第一个读取语句被成功执行，虽然第二个语句没有报错，但是在存储过程执行完毕后，再次查看变量`a`的值却并没有改变，这也就是说：IN参数只能被用于读取，对它赋值是不会被调用者看到的。

  另外，因为我们只是想在存储过程执行中使用IN参数，并不需要把执行过程中产生的数据存储到它里边，所以其实在调用存储过程时，将常量作为参数也是可以的，比如这样：

  ```sql
  mysql> CALL p_in(1);
  +------+
  | arg  |
  +------+
  |    1 |
  +------+
  1 row in set (0.00 sec)
  
  Query OK, 0 rows affected (0.00 sec)
  
  mysql>
  ```

- `OUT`参数

  先定义一个前缀是`OUT`的存储过程`p_out`：

  ```shell
  mysql> delimiter $
  mysql> CREATE PROCEDURE p_out (
  ->     OUT arg INT
  -> )
  -> BEGIN
  ->     SELECT arg;
  ->     SET arg = 123;
  -> END $
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> delimiter ;
  mysql>
  ```

  这个`p_out`存储过程只有一个参数`arg`，它的前缀是`OUT`，`p_out`存储过程也有两个语句，一个用于读取参数`arg`的值，另一个用于为参数`arg`赋值，我们调用一下`p_out`：

  ```sql
  mysql> SET @b = 2;
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> CALL p_out(@b);
  +------+
  | arg  |
  +------+
  | NULL |
  +------+
  1 row in set (0.00 sec)
  
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> SELECT @b;
  +------+
  | @b   |
  +------+
  |  123 |
  +------+
  1 row in set (0.00 sec)
  
  mysql>
  ```

  我们定义了一个变量`b`并把整数`2`赋值赋值给它，然后把它当作参数传给`p_out`存储过程。从结果中可以看出，第一个读取语句并没有获取到参数的值，也就是说OUT参数的值默认为`NULL`。在存储过程执行完毕之后，再次读取变量`b`的值，发现它的值已经被设置成`123`，说明在过程中对该变量的赋值对调用者是可见的！这也就是说：OUT参数只能用于赋值，对它赋值是可以被调用者看到的。

  另外，由于`OUT`参数只是为了用于将存储过程执行过程中产生的数据赋值给它后交给调用者查看，那么在调用存储过程时，实际的参数就不允许是常量！

- `INOUT`参数

  知道了`IN`参数和`OUT`参数的意思，`INOUT`参数也就明白了，这种参数既可以在存储过程中被读取，也可以被赋值后被调用者看到，所以要求在调用存储过程时实际的参数必须是一个变量，不然还怎么赋值啊！`INOUT`参数类型就不具体举例子了，大家可以自己试试哈～

需要注意的是，如果我们不写明参数前缀的话，默认的前缀是IN！

由于存储过程可以传入多个`OUT`或者`INOUT`类型的参数，所以我们可以在一个存储过程中获得多个结果，比如这样：

```sql
mysql> delimiter $
mysql> CREATE PROCEDURE get_score_data(
    ->     OUT max_score DOUBLE,
    ->     OUT min_score DOUBLE,
    ->     OUT avg_score DOUBLE,
    ->     s VARCHAR(100)
    -> )
    -> BEGIN
    ->     SELECT MAX(score), MIN(score), AVG(score) FROM student_score WHERE subject = s INTO max_score, min_score, avg_score;
    -> END $
Query OK, 0 rows affected (0.02 sec)

mysql> delimiter ;
mysql>
```

我们定义的这个`get_score_data`存储过程接受4个参数，前三个参数都是`OUT`参数，第四个参数没写前缀，默认就是`IN`参数。存储过程的内容是将指定学科的最高分、最低分、平均分分别赋值给三个`OUT`参数。在这个存储过程执行完之后，我们可以通过访问这几个`OUT`参数来获得相应的最高分、最低分以及平均分：

```less
mysql> CALL get_score_data(@a, @b, @c, '母猪的产后护理');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT @a, @b, @c;
+------+------+------+
| @a   | @b   | @c   |
+------+------+------+
|  100 |   55 |   73 |
+------+------+------+
1 row in set (0.00 sec)

mysql>
```

#### 存储过程和存储函数的不同点

`存储过程`和`存储函数`非常类似，我们列举几个它们的不同点以加深大家的对这两者区别的印象：

- 存储函数在定义时需要显式用`RETURNS`语句标明返回的数据类型，而且在函数体中必须使用`RETURN`语句来显式指定返回的值，存储过程不需要。
- 存储函数只支持`IN`参数，而存储过程支持`IN`参数、`OUT`参数、和`INOUT`参数。
- 存储函数只能返回一个值，而存储过程可以通过设置多个`OUT`参数或者`INOUT`参数来返回多个结果。
- 存储函数执行过程中产生的结果集并不会被显示到客户端，而存储过程执行过程中产生的结果集会被显示到客户端。
- 存储函数直接在表达式中调用，而存储过程只能通过`CALL`语句来显式调用。

## 游标的使用

标签： MySQL是怎样使用的新版

------

### 游标简介

截止到现在为止，我们只能使用`SELECT ... INTO ...`语句将一条记录的各个列值赋值到多个变量里，比如在前边的`get_score_data`存储过程里有这样的语句：

```sql
SELECT MAX(score), MIN(score), AVG(score) FROM student_score WHERE subject = s INTO max_score, min_score, avg_score;
```

但是如果某个查询语句的结果集中有多条记录的话，我们就无法把它们赋值给某些变量了～ 所以为了方便我们去访问这些有多条记录的结果集，`MySQL`中引入了`游标`的概念。

我们下边以对`t1`表的查询为例来介绍一下`游标`，比如我们有这样一个查询：

```sql
mysql> SELECT m1, n1 FROM t1;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
4 rows in set (0.00 sec)

mysql>
```

这个`SELECT m1, n1 FROM t1`查询语句对应的结果集有4条记录，`游标`其实就是用来标记结果集中我们正在访问的某一条记录。初始状态下它标记结果集中的第一条记录，就像这样：

![image_1c84n8uvc59177b1bp8niq17a91m.png-8.8kB](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/9/2/16cf10d2c56518db~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

我们可以根据这个`游标`取出它对应记录的信息，随后再移动游标，让它执向下一条记录。`游标`既可以用在存储函数中，也可以用在存储过程中，我们下边以存储过程为例来说明`游标`的使用方式，它的使用大致分成这么四个步骤：

1. 创建游标
2. 打开游标
3. 通过游标访问记录
4. 关闭游标

下边来详细介绍这几个步骤的详细情况。

### 创建游标

在创建游标的时候，需要指定一下与`游标`关联的查询语句，语法如下：

```sql
DECLARE 游标名称 CURSOR FOR 查询语句;
```

我们定义一个存储过程试一试：

```sql
CREATE PROCEDURE cursor_demo()
BEGIN
    DECLARE t1_record_cursor CURSOR FOR SELECT m1, n1 FROM t1;
END
```

这样名叫`t1_record_cursor`的游标就创建成功了。

```!
小贴士：

如果存储程序中也有声明局部变量的语句，创建游标的语句一定要放在局部变量声明后头。
```

### 打开和关闭游标

在创建完游标之后，我们需要手动打开和关闭游标，语法也简单：

```ini
OPEN 游标名称;

CLOSE 游标名称;
```

`打开游标`意味着执行查询语句，创建一个该查询语句得到的结果集关联起来的`游标`，`关闭游标`意味着会释放该游标相关的资源，所以一旦我们使用完了`游标`，就要把它关闭掉。当然如果我们不显式的使用`CLOSE`语句关闭游标的话，在该存储过程的`END`语句执行完之后会自动关闭的。

我们再来修改一下上边的`cursor_demo`存储过程：

```sql
CREATE PROCEDURE cursor_demo()
BEGIN
    DECLARE t1_record_cursor CURSOR FOR SELECT m1, n1 FROM t1;

    OPEN t1_record_cursor;

    CLOSE t1_record_cursor;
END
```

### 使用游标获取记录

在知道怎么打开和关闭游标之后，我们正式来唠叨一下如何使用游标来获取结果集中的记录，获取记录的语句长这样：

```sql
FETCH 游标名 INTO 变量1, 变量2, ... 变量n
```

这个语句的意思就是把指定游标对应记录的各列的值依次赋值给`INTO`后边的各个变量。我们来继续改写一下`cursor_demo`存储过程：

```sql
CREATE PROCEDURE cursor_demo()
BEGIN
    DECLARE m_value INT;
    DECLARE n_value CHAR(1);

    DECLARE t1_record_cursor CURSOR FOR SELECT m1, n1 FROM t1;

    OPEN t1_record_cursor;

    FETCH t1_record_cursor INTO m_value, n_value;
    SELECT m_value, n_value;

    CLOSE t1_record_cursor;
END $
```

我们来调用一下这个存储过程：

```sql
mysql> CALL cursor_demo();
+---------+---------+
| m_value | n_value |
+---------+---------+
|       1 | a       |
+---------+---------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

mysql>
```

额，奇怪，`t1`表里有4条记录，我们这里只取出了第一条？是的，如果想获取多条记录，那需要把 FETCH 语句放到循环语句中，我们再来修改一下`cursor_demo`存储过程：

```sql
CREATE PROCEDURE cursor_demo()
BEGIN
    DECLARE m_value INT;
    DECLARE n_value CHAR(1);
    DECLARE record_count INT;
    DECLARE i INT DEFAULT 0;

    DECLARE t1_record_cursor CURSOR FOR SELECT m1, n1 FROM t1;

    SELECT COUNT(*) FROM t1 INTO record_count;

    OPEN t1_record_cursor;

    WHILE i < record_count DO
        FETCH t1_record_cursor INTO m_value, n_value;
        SELECT m_value, n_value;
        SET i = i + 1;
    END WHILE;

    CLOSE t1_record_cursor;
END
```

这次我们又多使用了两个变量，`record_count`表示`t1`表中的记录行数，`i`表示当前游标对应的记录位置。每调用一次 FETCH 语句，游标就移动到下一条记录的位置。看一下调用效果：

```sql
mysql> CALL cursor_demo();
+---------+---------+
| m_value | n_value |
+---------+---------+
|       1 | a       |
+---------+---------+
1 row in set (0.00 sec)

+---------+---------+
| m_value | n_value |
+---------+---------+
|       2 | b       |
+---------+---------+
1 row in set (0.00 sec)

+---------+---------+
| m_value | n_value |
+---------+---------+
|       3 | c       |
+---------+---------+
1 row in set (0.00 sec)

+---------+---------+
| m_value | n_value |
+---------+---------+
|       4 | d       |
+---------+---------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)

mysql>
```

这回就把`t1`表中全部的记录就都遍历完了。

### 遍历结束时的执行策略

上边介绍的遍历方式需要我们首先获得查询语句结构集中记录的条数，也就是需要先执行下边这条语句：

```sql
SELECT COUNT(*) FROM t1 INTO record_count;
```

我们之所以要获取结果集中记录的条数，是因为我们需要一个结束循环的条件，当调用`FETCH`语句的次数与结果集中记录条数相等时就结束循环。

其实在`FETCH`语句获取不到记录的时候会触发一个事件，从而我们可以得知所有的记录都被获取过了，然后我们就可以去主动的停止循环。`MySQL`中响应这个事件的语句如下：

```vbnet
DECLARE CONTINUE HANDLER FOR NOT FOUND 处理语句;
```

只要我们在存储过程中写了这个语句，那么在`FETCH`语句获取不到记录的时候，服务器就会执行我们填写的处理语句。

```sql
!小贴士：

处理语句可以是简单的一条语句，也可以是由BEGIN ... END 包裹的多条语句。
```

我们接下来再来改写一下`cursor_demo`存储过程：

```sql
CREATE PROCEDURE cursor_demo()
BEGIN
    DECLARE m_value INT;
    DECLARE n_value CHAR(1);
    DECLARE not_done INT DEFAULT 1;

    DECLARE t1_record_cursor CURSOR FOR SELECT m1, n1 FROM t1;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET not_done = 0;

    OPEN t1_record_cursor;

    flag: LOOP
        FETCH t1_record_cursor INTO m_value, n_value;
        IF not_done = 0 THEN
            LEAVE flag;
        END IF;
        SELECT m_value, n_value, not_done;
    END LOOP flag;

    CLOSE t1_record_cursor;
END
```

我们声明了一个默认值为`1`的`not_done`变量和一个这样的语句：

```ini
DECLARE CONTINUE HANDLER FOR NOT FOUND SET not_done = 0;
```

`not_done`变量的值为`1`时表明遍历结果集的过程还没有结束，当`FETCH`语句无法获取更多记录时，就会触发一个事件，从而导致`MySQL`服务器主动调用上边的这个语句将`not_done`变量的值改为`0`。另外，我们把原先的`WHILE`语句替换成了`LOOP`语句，直接在`LOOP`语句的循环体中判断`not_done`变量的值，当它的值为`0`时就主动跳出循环。

让我们调用一下这个存储过程看一下效果：

```sql
mysql> call cursor_demo;
+---------+---------+----------+
| m_value | n_value | not_done |
+---------+---------+----------+
|       1 | a       |        1 |
+---------+---------+----------+
1 row in set (0.05 sec)

+---------+---------+----------+
| m_value | n_value | not_done |
+---------+---------+----------+
|       2 | b       |        1 |
+---------+---------+----------+
1 row in set (0.05 sec)

+---------+---------+----------+
| m_value | n_value | not_done |
+---------+---------+----------+
|       3 | c       |        1 |
+---------+---------+----------+
1 row in set (0.06 sec)

+---------+---------+----------+
| m_value | n_value | not_done |
+---------+---------+----------+
|       4 | d       |        1 |
+---------+---------+----------+
1 row in set (0.06 sec)

Query OK, 0 rows affected (0.07 sec)

mysql>
```

## 触发器和事件

标签： MySQL是怎样使用的新版

------

我们前边说过存储程序包括`存储例程`（`存储函数`与`存储过程`）、`触发器`和`事件`，其中`存储例程`是需要我们手动调用的，而`触发器`和`事件`是`MySQL`服务器在特定情况下自动调用的，接下来我们分别看一下`触发器`和`事件`。

### 触发器

我们使用`MySQL`的过程中可能会有下边这些需求：

- 在向`t1`表插入或更新数据之前对自动对数据进行校验，要求`m1`列的值必须在`1~10`之间，校验规则如下：
  - 如果插入的记录的`m1`列的值小于`1`，则按`1`插入。
  - 如果`m1`列的值大于`10`，则按`10`插入。
- 在向`t1`表中插入记录之后自动把这条记录插入到`t2`表。

也就是我们在对表中的记录做增、删、改操作前和后都可能需要让`MySQL`服务器自动执行一些额外的语句，这个就是所谓的`触发器`的应用场景。

#### 创建触发器

我们看一下定义`触发器`的语句：

```sql
CREATE TRIGGER 触发器名
{BEFORE|AFTER}
{INSERT|DELETE|UPDATE}
ON 表名
FOR EACH ROW
BEGIN
    触发器内容
END
小贴士：

由大括号`{}`包裹并且内部用竖线`|`分隔的语句表示必须在给定的选项中选取一个值，比如`{BEFORE|AFTER}`表示必须在`BEFORE`、`AFTER`这两个之间选取一个。
```

其中`{BEFORE|AFTER}`表示触发器内容执行的时机，它们的含义如下：

| 名称     | 描述                                           |
| -------- | ---------------------------------------------- |
| `BEFORE` | 表示在具体的语句执行之前就开始执行触发器的内容 |
| `AFTER`  | 表示在具体的语句执行之后才开始执行触发器的内容 |



`{INSERT|DELETE|UPDATE}`表示具体的语句，`MySQL`中目前只支持对`INSERT`、`DELETE`、`UPDATE`这三种类型的语句设置触发器。

`FOR EACH ROW BEGIN ... END`表示对具体语句影响的每一条记录都执行我们自定义的触发器内容：

- 对于`INSERT`语句来说，`FOR EACH ROW`影响的记录就是我们准备插入的那些新记录。
- 对于`DELETE`语句和`UPDATE`语句来说，`FOR EACH ROW`影响的记录就是符合`WHERE`条件的那些记录（如果语句中没有`WHERE`条件，那就是代表全部的记录）。

```!
小贴士：

如果触发器内容只包含一条语句，那也可以省略BEGN、END这两个词儿。
```

因为`MySQL`服务器会对某条语句影响的所有记录依次调用我们自定义的触发器内容，所以针对每一条受影响的记录，我们需要一种访问该记录中的内容的方式，`MySQL`提供了`NEW`和`OLD`两个单词来分别代表新记录和旧记录，它们在不同语句中的含义不同：

- 对于`INSERT`语句设置的触发器来说，`NEW`代表准备插入的记录，`OLD`无效。
- 对于`DELETE`语句设置的触发器来说，`OLD`代表删除前的记录，`NEW`无效。
- 对于`UPDATE`语句设置的触发器来说，`NEW`代表修改后的记录，`OLD`代表修改前的记录。

现在我们可以正式定义一个触发器了：

```sql
mysql> delimiter $
mysql> CREATE TRIGGER bi_t1
    -> BEFORE INSERT ON t1
    -> FOR EACH ROW
    -> BEGIN
    ->     IF NEW.m1 < 1 THEN
    ->         SET NEW.m1 = 1;
    ->     ELSEIF NEW.m1 > 10 THEN
    ->         SET NEW.m1 = 10;
    ->     END IF;
    -> END $
Query OK, 0 rows affected (0.02 sec)

mysql> delimiter ;
mysql>
```

我们对`t1`表定义了一个名叫`bi_t1`的`触发器`，它的意思就是在对`t1`表插入新记录之前，对准备插入的每一条记录都会执行`BEGIN ... END`之间的语句，`NEW.列名`表示当前待插入记录指定列的值。现在`t1`表中一共有4条记录：

```sql
mysql> SELECT * FROM t1;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
|    4 | d    |
+------+------+
4 rows in set (0.00 sec)

mysql>
```

我们现在执行一下插入语句并再次查看一下`t1`表的内容：

```sql
mysql> INSERT INTO t1(m1, n1) VALUES(5, 'e'), (100, 'z');
Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t1;
+------+------+
| m1   | n1   |
+------+------+
|    1 | a    |
|    2 | b    |
|    3 | c    |
|    4 | d    |
|    5 | e    |
|   10 | z    |
+------+------+
6 rows in set (0.00 sec)

mysql>
```

这个`INSERT`语句影响的记录有两条，分别是`(5, 'e')`和`(100, 'z')`，这两条记录将分别执行我们自定义的触发器内容。很显然`(5, 'e')`被成功的插入到了`t1`表中，而`(100, 'z')`插入到表中后却变成了`(10, 'z')`，这个就说明我们的`bi_t1`触发器生效了！

```!
小贴士：

我们上边定义的触发器名`bi_t1`的`bi`是`before insert`的首字母缩写，`t1`是表名。虽然对于触发器的命名并没有什么特殊的要求，但是习惯上还是建议大家把它定义我上边例子中的形式，也就是`bi_表名`、`bd_表名`、`bu_表名`、`ai_表名`、`ad_表名`、`au_表名`的形式。
```

上边只是举了一个对`INSERT`语句设置`BEFORE`触发器的例子，对`DELETE`和`UPDATE`操作设置`BEFORE`或者`AFTER`触发器的过程是类似的，就不赘述了。

#### 查看和删除触发器

查看当前数据库中定义的所有触发器的语句：

```ini
SHOW TRIGGERS;
```

查看某个具体的触发器的定义：

```sql
SHOW CREATE TRIGGER 触发器名;
```

删除触发器：

```sql
DROP TRIGGER 触发器名;
```

这几个命令太简单了，就不举例子了啊～

#### 触发器使用注意事项

- 触发器内容中不能有输出结果集的语句。

  比方说：

  ```sql
  mysql> delimiter $
  mysql> CREATE TRIGGER ai_t1
      -> AFTER INSERT ON t1
      -> FOR EACH ROW
      -> BEGIN
      ->     SELECT NEW.m1, NEW.n1;
      -> END $
  ERROR 1415 (0A000): Not allowed to return a result set from a trigger
  mysql>
  ```

  显示的`ERROR`的意思就是不允许在触发器内容中返回结果集！

- 触发器内容中NEW代表记录的列的值可以被更改，OLD代表记录的列的值无法更改。

  `NEW`代表新插入或着即将修改后的记录，修改它的列的值将影响INSERT和UPDATE语句执行后的结果，而`OLD`代表修改或删除之前的值，我们无法修改它。比方说如果我们非要这么写那就会报错的：

  ```sql
  mysql> delimiter $
  mysql> CREATE TRIGGER bu_t1
      -> BEFORE UPDATE ON t1
      -> FOR EACH ROW
      -> BEGIN
      ->     SET OLD.m1 = 1;
      -> END $
  ERROR 1362 (HY000): Updating of OLD row is not allowed in trigger
  mysql>
  ```

  可以看到提示的错误中显示在触发器中`OLD`代表的记录是不可被更改的。

- 在BEFORE触发器中，我们可以使用`SET NEW.列名 = 某个值`的形式来更改待插入记录或者待更新记录的某个列的值，但是这种操作不能在AFTER触发器中使用，因为在执行AFTER触发器的内容时记录已经被插入完成或者更新完成了。

  比方说如果我们非要这么写那就会报错的：

  ```sql
  mysql> delimiter $
  mysql>     CREATE TRIGGER ai_t1
      ->     AFTER INSERT ON t1
      ->     FOR EACH ROW
      ->     BEGIN
      ->         SET NEW.m1 = 1;
      ->     END $
  ERROR 1362 (HY000): Updating of NEW row is not allowed in after trigger
  mysql>
  ```

  可以看到提示的错误中显示在AFTER触发器中是不允许更改`NEW`代表的记录的。

- 如果我们的`BEFORE`触发器内容执行过程中遇到了错误，那这个触发器对应的具体语句将无法执行；如果具体的操作语句执行过程中遇到了错误，那与它对应的`AFTER`触发器的内容将无法执行。

  ```!
  小贴士：
  
  对于支持事务的表，不论是执行触发器内容还是具体操作语句过程中出现了错误，会把这个过程中所有的语句都回滚。当然，作为小白的我们并不知道啥是个事务，啥是个回滚，这些进阶内容都在《MySQL是怎样运行的：从根儿上理解MySQL》中呢～
  ```

### 事件

有时候我们想让`MySQL`服务器在某个时间点或者每隔一段时间自动地执行一些语句，这时候就需要去创建一个`事件`。

#### 创建事件

创建事件的语法如下：

```sql
CREATE EVENT 事件名
ON SCHEDULE
{
    AT 某个确定的时间点| 
    EVERY 期望的时间间隔 [STARTS datetime][END datetime]
}
DO
BEGIN
    具体的语句
END
```

`事件`支持两种类型的自动执行方式：

1. 在某个确定的时间点执行。

   比方说：

   ```sql
   CREATE EVENT insert_t1_event
   ON SCHEDULE
   AT '2019-09-04 15:48:54'
   DO
   BEGIN
       INSERT INTO t1(m1, n1) VALUES(6, 'f');
   END
   ```

   我们在这个`事件`中指定了执行时间是`'2019-09-04 15:48:54'`，除了直接填某个时间常量，我们也可以填写一些表达式：

   ```sql
   CREATE EVENT insert_t1
   ON SCHEDULE
   AT DATE_ADD(NOW(), INTERVAL 2 DAY)
   DO
   BEGIN
       INSERT INTO t1(m1, n1) VALUES(6, 'f');
   END
   ```

   其中的`DATE_ADD(NOW(), INTERVAL 2 DAY)`表示该事件将在当前时间的两天后执行。

2. 每隔一段时间执行一次。

   比方说：

   ```sql
   CREATE EVENT insert_t1
   ON SCHEDULE
   EVERY 1 HOUR
   DO
   BEGIN
       INSERT INTO t1(m1, n1) VALUES(6, 'f');
   END
   ```

   其中的`EVERY 1 HOUR`表示该事件将每隔1个小时执行一次。默认情况下，采用这种每隔一段时间执行一次的方式将从创建事件的事件开始，无限制的执行下去。我们也可以指定该事件开始执行时间和截止时间：

   ```sql
   CREATE EVENT insert_t1
   ON SCHEDULE
   EVERY 1 HOUR STARTS '2019-09-04 15:48:54' ENDS '2019-09-16 15:48:54'
   DO
   BEGIN
       INSERT INTO t1(m1, n1) VALUES(6, 'f');
   END
   ```

   如上所示，该事件将从'2019-09-04 15:48:54'开始直到'2019-09-16 15:48:54'为止，中间每隔1个小时执行一次。

   ```!
   小贴士：
   
   表示事件间隔的单位除了HOUR，还可以用YEAR、QUARTER、MONTH、DAY、HOUR、 MINUTE、WEEK、SECOND、YEAR_MONTH、DAY_HOUR、DAY_MINUTE、DAY_SECOND、HOUR_MINUTE、HOUR_SECOND、MINUTE_SECOND这些单位，根据具体需求选用我们需要的时间间隔单位。
   ```

在创建好`事件`之后我们就不用管了，到了指定时间，`MySQL`服务器会帮我们自动执行的。

#### 查看和删除事件

查看当前数据库中定义的所有事件的语句：

```ini
SHOW EVENTS;
```

查看某个具体的事件的定义：

```sql
SHOW CREATE EVENT 事件名;
```

删除事件：

```ini
DROP EVENT 事件名;
```

这几个命令太简单了，就不举例子了啊～

#### 事件使用注意事项

默认情况下，`MySQL`服务器并不会帮助我们执行事件，除非我们使用下边的语句手动开启该功能：

```ini
mysql> SET GLOBAL event_scheduler = ON;
Query OK, 0 rows affected (0.00 sec)

mysql>
小贴士：

event_scheduler其实是一个系统变量，它的值也可以在MySQL服务器启动的时候通过启动参数或者通过配置文件来设置event_scheduler的值。这些所谓
```
