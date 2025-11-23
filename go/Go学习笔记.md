# Go学习笔记



## Go概述

### 执行流程分析

```
.go=>go build =>可执行文件=》运行
.=>go run=>运行
```

区别

```
1)如果我们先编译生成了可执行文件，那么我们可以将该可执行文件拷贝到没有go开发环境的机器上，仍然可以运行。
2)如果我们是直接go run go源代码，那么如果要在另外一个机器上这么运行，也需要go开发环境，否则无法执行。
3)在编译时，编译器会将程序运行依赖的库文件包含在可执行文件中，所以，可执行文件变大了很多。
```

### Go程序开发的注意事项

```
1)Go源文件以"go"为扩展名。
2)Go应用程序的执行入口是main()函数。这个是和其它编程语言（比如java/c）
3)Go语言严格区分大小写。
4)Go方法由一条条语句构成，每个语句后不需要分号(Go语言会在每行后自动加分号)，这也体现出Golang的简洁性
5)Go编译器是一行行进行编译的，因此我们一行就写一条语句，不能把多条语句写在同一个，否则报错
6)go语言定义的变量或者import的包如果没有使用到，代码不能编译通过
7)大括号都是成对出现的，缺一不可
```

### Go语言的转义字符(escapechar)

```
常用的转义字符有如下:
1) \t:表示一个制表符，通常使用它可以排版。
2) \n：换行符
3) \\：一个\ 
4) \"：一个"
5) \r：一个回车fmt.Println("天龙八部雪山飞狐\r张飞"); 张飞八部雪山飞狐(从当前行最前面开始输出，覆盖以前内容)
```

### 规范的代码风格

```
注释
1) Go官方推荐使用行注释来注释整个方法和语句

正确的缩进和空白
1) 使用一次tab操作，实现缩进,默认整体向右边移动，时候用shift+tab整体向左移
2) 或者使用gofmt来进行格式化
3) 运算符两边习惯性各加一个空格。比如：2+4*5
4) Go语言的代码风格.{ 在一行
} 不允许
{
}
5) 一行最长不超过80个字符，超过的请使用换行展示，尽量保持格式优雅
```

## Go变量

### 变量的介绍

1) 变量表示内存中的一个存储区域

2) 该区域有自己的名称（变量名）和类型（数据类型）

Golang变量使用的三种方式

(1) 第一种：指定变量类型，声明后若不赋值，使用默认值

```go
var i int (int 默认值为 0)
```

(2) 第二种：根据值自行判定变量类型(类型推导)

```go
var num = 10.11
```

(3) 第三种：省略var,注意:=左侧的变量不应该是已经声明过的，否则会导致编译错误

```go
name := "tom"
```

4) 多变量声明

```go
1. var n1, n2, n3 int
2. var n1, n2, n3 = 10, 20, 30
3. n1, n2, n3 := 10, 20, 30
```

如何一次性声明多个全局变量

```go
1. var n1 = 10
var n2 = 20
var n3 = 30
2. var (
    n1 = 10
    n2 = 20
    n3 = 30
)
```

5) 该区域的数据值可以在同一类型范围内不断变化(重点)

```go
var i int = 10
i = 20 true
i = 10.1 false
```

6) 变量在同一个作用域(在一个函数或者在代码块)内不能重名

7) 变量=变量名+值+数据类型，这一点请大家注意，变量的三要素

8) Golang的变量如果没有赋初值，编译器会使用默认值,比如int默认值 0 string默认值为空串，小数默认为 0

#### 获取变量类型的几种方法

##### 1.使用 `fmt.Printf` 和 `%T`

```go
package main

import "fmt"

func main() {
    var items = []string{"a", "b", "c"}
    var count = 42
    var price = 19.99
    
    fmt.Printf("items type: %T\n", items) // []string
    fmt.Printf("count type: %T\n", count) // int
    fmt.Printf("price type: %T\n", price) // float64
}
```

##### 2.使用 `reflect` 包

```go
package main

import (
    "fmt"
    "reflect"
)

func main() {
    var name = "Alice"
    var age = 30
    var scores = []int{95, 87, 92}
    
    fmt.Println("name type:", reflect.TypeOf(name))    // string
    fmt.Println("age type:", reflect.TypeOf(age))      // int
    fmt.Println("scores type:", reflect.TypeOf(scores)) // []int
    
    // 获取类型名称
    fmt.Println("scores type name:", reflect.TypeOf(scores).String())
    
    // 获取类型的 Kind（基础类型）
    fmt.Println("scores kind:", reflect.TypeOf(scores).Kind()) // slice
}
```

##### 3.使用类型断言

```go
package main

import "fmt"

func printType(v interface{}) {
    switch v.(type) {
    case int:
        fmt.Println("Type: int")
    case string:
        fmt.Println("Type: string")
    case []int:
        fmt.Println("Type: []int")
    case float64:
        fmt.Println("Type: float64")
    default:
        fmt.Printf("Type: %T\n", v)
    }
}

func main() {
    printType(42)        // Type: int
    printType("hello")   // Type: string
    printType([]int{1,2,3}) // Type: []int
}
```

#### 格式化输出

##### 通用格式化占位符

| 占位符 | 说明             | 示例                        |
| :----- | :--------------- | :-------------------------- |
| `%v`   | 默认格式         | `fmt.Printf("%v", data)`    |
| `%+v`  | 结构体显示字段名 | `fmt.Printf("%+v", struct)` |
| `%#v`  | Go 语法表示      | `fmt.Printf("%#v", data)`   |
| `%T`   | 类型表示         | `fmt.Printf("%T", data)`    |
| `%%`   | 字面百分号       | `fmt.Printf("100%%")`       |

##### 综合示例

```go
package main

import "fmt"

type Product struct {
	Name     string
	Price    float64
	Quantity int
}

func main() {
	// 综合格式化示例
	product := Product{"Laptop", 999.99, 5}
	numbers := []int{1, 2, 3, 4, 5}

	// 表格形式输出
	fmt.Printf("产品信息:\n")
	fmt.Printf("名称: %-10s | 价格: $%8.2f | 数量: %3d\n",
		product.Name, product.Price, product.Quantity)

	// 数值格式化
	fmt.Printf("\n数值格式化:\n")
	fmt.Printf("十进制: %d, 十六进制: 0x%X, 二进制: %b\n", 255, 255, 255)

	// 字符串格式化
	fmt.Printf("\n字符串格式化:\n")
	fmt.Printf("原始: %s, 带引号: %q, 十六进制: %x\n", "Hello", "Hello", "Hello")

	// 切片格式化
	fmt.Printf("\n切片格式化:\n")
	fmt.Printf("默认: %v, 详细: %#v\n", numbers, numbers)

	// 百分比显示
	progress := 0.756
	fmt.Printf("\n进度: %.1f%%\n", progress*100) // 进度: 75.6%

	// 文件大小格式化
	fileSize := 1520435
	fmt.Printf("文件大小: %.2f MB\n", float64(fileSize)/(1024*1024))
  
  // 错误格式化
  err := fmt.Errorf("文件 %s 未找到", "test.txt")
	fmt.Printf("错误: %v\n", err)    // 错误: 文件 test.txt 未找到
	fmt.Printf("错误详细: %+v\n", err) // 错误详细: 文件 test.txt 未找到
  // 索引
  fmt.Printf("%[2]d %[1]d %[2]d\n", 10, 20) // 20 10 20
}

产品信息:
名称: Laptop     | 价格: $  999.99 | 数量:   5

数值格式化:
十进制: 255, 十六进制: 0xFF, 二进制: 11111111

字符串格式化:
原始: Hello, 带引号: "Hello", 十六进制: 48656c6c6f

切片格式化:
默认: [1 2 3 4 5], 详细: []int{1, 2, 3, 4, 5}

进度: 75.6%
文件大小: 1.45 MB
```

##### 格式化输出到不同目标

```go
package main

import (
	"fmt"
	"os"
	"strings"
)

func main() {

	name := "Bob"
	age := 25

	// 输出到标准输出
	fmt.Printf("Name: %s, Age: %d\n", name, age)

	// 输出到字符串
	s := fmt.Sprintf("Name: %s, Age: %d", name, age)
	fmt.Println("生成的字符串:", s)

	// 输出到文件
	file, _ := os.Create("output.txt")
	fmt.Fprintf(file, "Name: %s, Age: %d", name, age)

	// 输出到缓冲区
	var buf strings.Builder
	fmt.Fprintf(&buf, "Name: %s, Age: %d", name, age)
	fmt.Println("缓冲区内容:", buf.String())

}
Name: Bob, Age: 25
生成的字符串: Name: Bob, Age: 25
缓冲区内容: Name: Bob, Age: 25
文件output.txt内容：Name: Bob, Age: 25
```

### 数据类型的基本介绍

#### 整数类型

##### 整型的使用细节

1) Golang各整数类型分：有符号和无符号，int uint的大小和系统有关。

```
是根据操作系统平台而言的,

如果是64位操作系统, 这个int默认是int64
32位操作系统才是int32
```

2) Golang的整型默认声明为int型

3) 如何在程序查看某个变量的字节大小和数据类型（使用较多）

```go
	fmt.Println(a)
	fmt.Printf("a 类型 %T 字节数 %d", a, unsafe.Sizeof(a))
	fmt.Println()
	var b float64
	fmt.Printf("b 类型 %T 字节数 %d", b, unsafe.Sizeof(b))

a 类型 int 字节数 8
b 类型 float64 字节数 8b type is float64
```

4) Golang程序中整型变量在使用时，遵守保小不保大的原则，即：在保证程序正确运行下，尽量使用占用空间小的数据类型。【如：年龄】**byte 0~255 与uint8等价**

5) bit:计算机中的最小存储单位。byte:计算机中基本存储单元。[二进制再详细说]1byte=8bit

#### 小数类型(float)

```
单精度 float32 4字节
双精度 float64 8字节
```

1) Golang浮点类型有固定的范围和字段长度，不受具体OS(操作系统)的影响

2) Golang的浮点型默认声明为float64类型

3) 浮点型常量有两种表示形式

十进制数形式：如：5.12  .512(必须有小数点）=>0.512

```go
	var num1 = 2.34
	var num2 = .345
	fmt.Println(num1)
	fmt.Println(num2)

2.34
0.345
```

科学计数法形式:如：5.1234e2 = 5.12*10的2次方 5.12E-2 = 5.12/(10的2次方)

```go
	var num3 = 2e2
	var num4 = 2E2
	fmt.Println(num3)
	fmt.Println(num4)

200
200

	var num5 = 2e-2
	fmt.Println(num5)
0.02
```

4) 通常情况下，应该使用float64，因为它比float32更精确。[开发中，推荐使用float64]

#### 字符类型

```
Golang中没有专门的字符类型，如果要存储单个字符(字母)，一般使用byte来保存。
字符串就是一串固定长度的字符连接起来的字符序列。Go的字符串是由单个字节连接起来的。也就是说对于传统的字符串是由字符组成的，而Go的字符串不同，它是由字节组成的
```

```go
package main

import "fmt"

func main() {
	var a byte = 'a'
	fmt.Printf("%c\n", a)
	fmt.Printf("%d\n", a)

	var c int = '北'
	fmt.Printf("c:%c\n", c)
	fmt.Printf("c:%d\n", c)

}
a
97
c:北
c:21271
```

1) 如果我们保存的字符在ASCII表的,比如[0-1,a-z,A-Z..]直接可以保存到byte

2) 如果我们保存的字符对应码值大于255,这时我们可以考虑使用int类型保存

3) 如果我们需要安装字符的方式输出，这时我们需要格式化输出，即fmt.Printf(“%c”,c1)..

4) 在Go中，字符的本质是一个整数，直接输出时，是该字符对应的UTF-8编码的码值

5) Go语言的编码都统一成了utf-8。非常的方便，很统一，再也没有编码乱码的困扰了

##### 大小写转换

```go
package main

import (
	"fmt"
	"unicode"
)

func main() {
	var chb byte = 'B' + 'a' - 'A'
	fmt.Printf("%c\n", chb)
	fmt.Printf("%d\n", chb)

	// 单个字符转换
	ch := 'a' // 默认是 rune 类型 type rune = int32

	// 检查字符类型
	fmt.Printf("'%c' 是小写字母: %t\n", ch, unicode.IsLower(ch))
	fmt.Printf("'%c' 是大写字母: %t\n", ch, unicode.IsUpper(ch))
	fmt.Printf("'%c' 是字母: %t\n", ch, unicode.IsLetter(ch))
}

b
98
'a' 是小写字母: true
'a' 是大写字母: false
'a' 是字母: true
```

#### 布尔类型

1) 布尔类型也叫bool类型，bool类型数据只允许取值true和false

2) bool类型占1个字节。

3) bool类型适于逻辑运算，一般用于程序流程控制

```go
package main

import "fmt"

func main() {
	bo := true
	fmt.Printf("%t\n", bo)
	fmt.Println(bo)
}
```

#### string类型

字符串就是一串固定长度的字符连接起来的字符序列。Go的字符串是由单个字节连接起来的。Go语言的字符串的字节使用UTF-8编码标识Unicode文本

```go
package main

import (
	"fmt"
)

func main() {
	// 1. UTF-8 支持
	chineseStr := "Hello, 世界！"
	fmt.Printf("1. UTF-8 字符串: %s\n", chineseStr)

	// 2. 不可变性演示
	// original := "immutable"
	// original[0] = 'A' // 这行会编译错误

	// 3. 双引号 vs 反引号
	doubleQuoted := "第一行\n第二行\t制表符\"引号\""
	backticked := `第一行\n第二行\t制表符"引号"`
	fmt.Printf("   双引号: %s\n", doubleQuoted)
	fmt.Printf("   反引号: %s\n", backticked)

	// 4. 字符串拼接
	fmt.Println("4. 字符串拼接方式:")
	part1 := "Go"
	part2 := "语言"
	concatenated := part1 + part2
	fmt.Printf("   + 拼接: %s\n", concatenated)

	// 5. 多行字符串
	fmt.Println("5. 多行字符串处理:")
	multi := "这是第一行" + // 加号在最后面
		"这是第二行" +
		"这是第三行"
	fmt.Printf("   多行拼接: %s\n", multi)

	// 性能提示
	fmt.Println("\n=== 性能提示 ===")
	fmt.Println("- 大量字符串拼接请使用 strings.Builder")
	fmt.Println("- 只读多行文本使用反引号")
	fmt.Println("- 需要转义字符时使用双引号")
	fmt.Println("- 遍历中文字符请使用 for range")
}

1. UTF-8 字符串: Hello, 世界！
   双引号: 第一行
第二行	制表符"引号"
   反引号: 第一行\n第二行\t制表符"引号"
4. 字符串拼接方式:
   + 拼接: Go语言
5. 多行字符串处理:
   多行拼接: 这是第一行这是第二行这是第三行

=== 性能提示 ===
- 大量字符串拼接请使用 strings.Builder
- 只读多行文本使用反引号
- 需要转义字符时使用双引号
- 遍历中文字符请使用 for range
```

#### 基本数据类型的默认值

在go中，数据类型都有一个默认值，当程序员没有赋值时，就会保留默认值，在go中，默认值又叫零值

| 数据类型 | 默认值 |
| -------- | ------ |
| 整型     | 0      |
| 浮点型   | 0      |
| 布尔     | false  |
| 字符串   | “”     |

#### 基本数据类型的相互转换

Golang和java/c不同，Go在不同类型的变量之间赋值时需要显式转换。也就是说Golang中数据类型不能自动转换

```go
表达式T(v)将值v转换为类型T
package main

import "fmt"

func main() {
	// 1、小范围 → 大范围（安全转换）
	var a int8 = 100
	var b int16 = int16(a)
	fmt.Printf("%d\n", a) // 2、原值不变
	fmt.Printf("%d\n", b)

	// 3、大范围 → 小范围（可能溢出）
	var m int16 = 300                 // 300超过了int8的范围(-128~127)
	var n int8 = int8(m)              // 会发生溢出
	fmt.Printf("m=%d → n=%d\n", m, n) // 300 → 44

}

100
100
m=300 → n=44
```

#### 基本数据类型和string的转换

**基本类型转string类型**

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	// 方式1：fmt.Sprintf("%参数",表达式)【灵活】
	var num = 99
	str := fmt.Sprintf("%d", num)
	fmt.Println(str)
	var num2 = 22.33
	str2 := fmt.Sprintf("%f", num2)
	fmt.Printf("num2 = %s, quote num2=%q\n", str2, str2)

	// 方式2：strconv
	str3 := strconv.FormatInt(int64(num), 10)
	fmt.Printf("%s, %T\n", str3, str3)
	str4 := strconv.Itoa(num)
	fmt.Printf("%s, %T", str4, str4)
}

99
num2 = 22.330000, quote num2="22.330000"
99, string
99, string
```

**string类型转基本数据类型**

```go
package main

import (
	"fmt"
	"runtime"
	"strconv"
	"unsafe"
)

func main() {
	//1、在Go语言中，int类型的大小是平台相关的， 转换 int32和 int64需要显示转换
	//32位系统：int是32位（4字节），与int32相同
	//64位系统：int是64位（8字节），与int64相同
	var i int
	fmt.Printf("当前系统架构: %s\n", runtime.GOARCH)
	fmt.Printf("int类型大小: %d 字节\n", unsafe.Sizeof(i))
	fmt.Printf("int32类型大小: %d 字节\n", unsafe.Sizeof(int32(0)))
	fmt.Printf("int64类型大小: %d 字节\n", unsafe.Sizeof(int64(0)))
	if unsafe.Sizeof(i) == 4 {
		fmt.Println("当前int是32位的")
	} else {
		fmt.Println("当前int是64位的")
	}

	// 2、str 转 int, 正常情况
	str := "99"
	num, _ := strconv.ParseInt(str, 10, 64)
	fmt.Println(num)

	// bitSize移除
	str1 := "138"
	num1, err := strconv.ParseInt(str1, 10, 8)
	fmt.Printf("%T, %v\n", num1, num1)
	fmt.Printf("%T, %v\n", err, err)

	// 无法转换
	str2 := "hello"
	num2, err2 := strconv.ParseInt(str2, 10, 8)
	fmt.Printf("%T, %v\n", num2, num2)
	fmt.Printf("%T, %v\n", err2, err2)

}

当前系统架构: arm64
int类型大小: 8 字节
int32类型大小: 4 字节
int64类型大小: 8 字节
当前int是64位的
99
int64, 127
*strconv.NumError, strconv.ParseInt: parsing "138": value out of range
int64, 0
*strconv.NumError, strconv.ParseInt: parsing "hello": invalid syntax
```

### 指针

```go
package main

import "fmt"

func main() {
	i := 12
	var ptr *int = &i
	fmt.Printf("%v\n", ptr)
	fmt.Printf("%v\n", &ptr)
	fmt.Printf("%v\n", *ptr)
	fmt.Printf("%p\n", ptr)
}

0x14000098020
0x1400009a030
12
0x14000098020

1、值类型，都有对应的指针类型，形式为*数据类型，比如int的对应的指针就是 *int,float32对应的指针类型就是 *float32,依次类推
2、值类型和引用类型
  1) 值类型：基本数据类型int系列,float系列,bool,string、数组和结构体struct
  2) 引用类型：指针、slice切片、map、管道chan、interface等都是引用类型
3、值类型和引用类型的使用特点
  1) 值类型：变量直接存储值，内存通常在栈中分配
  2) 引用类型：变量存储的是一个地址，这个地址对应的空间才真正存储数据(值)，内存通常在堆上分配，当没有任何变量引用这个地址时，该地址对应的数据空间就成为一个垃圾，由GC来回收
```

### 标识符的命名规范

Golang对各种变量、方法、函数等命名时使用的字符序列称为标识符, 凡是自己可以起名字的地方都叫标识符

#### **标识符的命名规则**

1) 由26个英文字母大小写，0-9，_组成

2) 数字不可以开头 

3) Golang中严格区分大小写

4) 标识符不能包含空格

5) 下划线"_"本身在Go中是一个特殊的标识符，称为空标识符。可以代表任何其它的标识符，但是它对应的值会被忽略(比如：忽略某个返回值)。所以仅能被作为占位符使用，不能作为标识符使用

6) 不能以系统保留关键字作为标识符

#### **标识符命名注意事项**

1) 包名：保持package的名字和目录保持一致，尽量采取有意义的包名，简短，有意义，不要和标准库不要冲突fmt

2) 变量名、函数名、常量名：采用驼峰法 var stuName string = “tom”

3) **如果变量名、函数名、常量名首字母大写，则可以被其他的包访问；如果首字母小写，则只能在本包中使用**(注：可以简单的理解成，首字母大写是公开的，首字母小写是私有的),在golang没有public,private等关键字

4) 文件名使用小写字符，单词间用**下划线**分隔

## 运算符

### 算术运算符

```go
package main

import "fmt"

func main() {
	var num int = 10 / 3 // 不保留小数 
	fmt.Printf("%d\n", num)

	var num1 float32 = 10 / 3  // 不保留小数
	fmt.Println(num1)
	fmt.Printf("%f\n", num1)

	var num2 float32 = 10.0 / 3 // 保留小数
	fmt.Println(num2)
	fmt.Printf("%f\n", num2)

	// 取模 a%b=a-a/b*b  -10 - (-10)/3 * 3 = -10+9 = -1
	var num3 int = 10 % 3
	fmt.Println(num3)
	var num4 int = (-10) % 3
	fmt.Println(num4)

	// ++ --只能独立使用，否则报错， 不能前置++i,错误
	var i = 8
	var a int
	//a = i++  // 错误
	//a = i-- // 错误
	//if i++ > 0 { // 错误
	//}
	i++
	a = i
	fmt.Println(i)
	fmt.Println(a)

}

3
3
3.000000
3.3333333
3.333333
1
-1
9
9
```

### 关系运算符

```go
package main

import "fmt"

func main() {
	fmt.Println(1 == 2)
}

false
1) 关系运算符的结果都是bool型，也就是要么是true，要么是false
2) 关系运算符组成的表达式，我们称为关系表达式：a>b
```

### 逻辑运算符

1) &&也叫短路与：如果第一个条件为false，则第二个条件不会判断，最终结果为false

2) ||也叫短路或：如果第一个条件为true，则第二个条件不会判断，最终结果为true

### 赋值运算符

**赋值运算符的特点**

1) 运算顺序从右往左

2) 赋值运算符的左边只能是变量,右边可以是变量、表达式、常量值

3) 复合赋值运算符等价于下面的效果

比如：a+=3等价于a=a+3

**面试题**

有两个变量，a和b，要求将其进行交换，但是不允许使用中间变量，最终打印结果

```go
a = a + b
b = a - b
a = a - b
```

### 键盘输入语句

#### fmt

```go
package main

import "fmt"

func main() {
	var name string
	var age int

	// 为什么需要 &？
	// Scan函数需要修改name和age变量的值
	// 如果不传递地址，Scan只能得到变量的副本，无法修改原变量

	// 1. fmt.Scan - 读取多个值，空格分隔
	fmt.Print("请输入姓名和年龄（空格分隔）: ")
	n, err := fmt.Scan(&name, &age)
	if err ! = nil {
		fmt.Println("输入错误:", err)
		return
	}
	fmt.Printf("成功读取 %d 个值: 姓名=%s, 年龄=%d\n", n, name, age)

	// 清空缓冲区（重要！）
	fmt.Scanln()

	// 2. fmt.Scanln - 读取一行，空格分隔
	fmt.Print("请输入姓名和年龄（空格分隔）: ")
	n, err = fmt.Scanln(&name, &age)
	if err ! = nil {
		fmt.Println("输入错误:", err)
		return
	}
	fmt.Printf("成功读取 %d 个值: 姓名=%s, 年龄=%d\n", n, name, age)

	// 3. fmt.Scanf - 格式化读取
	var score float64
	fmt.Print("请输入姓名-年龄-分数（格式：张三-20-95.5）: ")
	n, err = fmt.Scanf("%s-%d-%f", &name, &age, &score)
	if err ! = nil {
		fmt.Println("输入错误:", err)
		return
	}
	fmt.Printf("成功读取 %d 个值: %s %d %.1f\n", n, name, age, score)
}

请输入姓名和年龄（空格分隔）: tom 12
成功读取 2 个值: 姓名=tom, 年龄=12

请输入姓名和年龄（空格分隔）: jerry 15
成功读取 2 个值: 姓名=jerry, 年龄=15
请输入姓名-年龄-分数（格式：张三-20-95.5）: kevin-18-90
输入错误: input does not match format
```

#### bufio

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
	"strings"
)

func main() {
	reader := bufio.NewReader(os.Stdin)

	// 读取单行字符串
	fmt.Print("请输入一行文字: ")
	text, _ := reader.ReadString('\n')
	text = strings.TrimSpace(text) // 去除换行符和空格
	fmt.Printf("你输入的是: '%s'\n", text)

	// 读取整数
	fmt.Print("请输入一个整数: ")
	numStr, _ := reader.ReadString('\n')
	numStr = strings.TrimSpace(numStr)
	num, err := strconv.Atoi(numStr)
	if err ! = nil {
		fmt.Println("输入的不是有效整数")
		return
	}
	fmt.Printf("整数: %d\n", num)

	// 读取多个整数（空格分隔）
	fmt.Print("请输入多个整数（空格分隔）: ")
	numsStr, _ := reader.ReadString('\n')
	numsStr = strings.TrimSpace(numsStr)
	numStrs := strings.Split(numsStr, " ")

	var nums []int
	for _, s := range numStrs {
		n, err := strconv.Atoi(s)
		if err ! = nil {
			fmt.Printf("'%s' 不是有效整数\n", s)
			continue
		}
		nums = append(nums, n)
	}
	fmt.Printf("整数数组: %v\n", nums)
}

请输入一行文字: hello world
你输入的是: 'hello world'
请输入一个整数: 12
整数: 12
请输入多个整数（空格分隔）: 12 45 666 777
整数数组: [12 45 666 777]

// 示例输入（多行，每行一个或多个数）： 一直都直到 EOF
// 1 2
// 3 4 5
// 6
for {
        // 读取一行
        line, err := reader.ReadString('\n')
        if err ! = nil {
            if err == io.EOF {
                break
            }
            fmt.Println("读取错误:", err)
            break
        }
```

### 进制

```go
package main

import "fmt"

func main() {
	num := 255

	// 十进制输出（默认）
	fmt.Printf("十进制: %d\n", num) // 255
	str := fmt.Sprintf("%b", num)
	fmt.Println("二进制:", str)

	// 二进制输出
	fmt.Printf("二进制: %b\n", num) // 11111111

	// 八进制输出
	fmt.Printf("八进制: %o\n", num)     // 377
	fmt.Printf("带前缀的八进制: %O\n", num) // 0o377 (Go 1.13+)

	// 十六进制输出
	fmt.Printf("小写十六进制: %x\n", num)      // ff
	fmt.Printf("大写十六进制: %X\n", num)      // FF
	fmt.Printf("带前缀的小写十六进制: %#x\n", num) // 0xff
	fmt.Printf("带前缀的大写十六进制: %#X\n", num) // 0XFF
}

十进制: 255
二进制: 11111111
二进制: 11111111
八进制: 377
带前缀的八进制: 0o377
小写十六进制: ff
大写十六进制: FF
带前缀的小写十六进制: 0xff
带前缀的大写十六进制: 0XFF
```

## 程序流程控制

### if

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	// 1. 基本语法（无括号）
	x := 10
	if x > 5 {
		fmt.Println("x大于5")
	}

	// 2. 带初始化语句（变量作用域限定在if块内）
	if y := 20; y > x {
		fmt.Printf("y=%d 大于 x=%d\n", y, x)
	}
	// 这里不能访问 y

	// 3. 错误处理模式（Go语言特色）
	if num, err := strconv.Atoi("123"); err ! = nil {
		fmt.Printf("转换失败: %v\n", err)
	} else {
		fmt.Printf("转换成功: %d\n", num)
	}

	// 4. 类型断言：检查接口值的具体类型 基本语法：value.(Type)
	var value interface{} = "hello"
	if str, ok := value.(string); ok {
		fmt.Printf("是字符串: %s\n", str)
	}

	// 5. 提前返回模式（避免深层嵌套）
	if err := validateInput(""); err ! = nil {
		fmt.Printf("验证失败: %v\n", err)
		return
	}
	fmt.Println("验证成功")
}

func validateInput(input string) error {
	if input == "" {
		return fmt.Errorf("输入不能为空")
	}
	if len(input) < 3 {
		return fmt.Errorf("输入至少3个字符")
	}
	return nil
}

x大于5
y=20 大于 x=10
转换成功: 123
是字符串: hello
验证失败: 输入不能为空
```

### switch分支控制

```go
package main

import "fmt"

func main() {
	// 1. 基本switch（自动break）
	fruit := "apple"
	switch fruit {
	case "apple":
		fmt.Println("这是苹果")
	case "banana":
		fmt.Println("这是香蕉")
	default:
		fmt.Println("未知水果")
	}

	// 2. 多条件case
	day := 3
	switch day {
	case 1, 7:
		fmt.Println("周末")
	case 2, 3, 4, 5, 6:
		fmt.Println("工作日")
	}

	// 3. 表达式switch（无标签）
	score := 85
	switch {
	case score >= 90:
		fmt.Println("优秀")
	case score >= 80:
		fmt.Println("良好") // 输出：良好
	case score >= 60:
		fmt.Println("及格")
	default:
		fmt.Println("不及格")
	}

	// 4. 类型switch（重点！）
	var value interface{} = "hello"
	switch v := value.(type) {
	case string:
		fmt.Printf("字符串: %s\n", v) // 输出：字符串: hello
	case int:
		fmt.Printf("整数: %d\n", v)
	default:
		fmt.Printf("未知类型: %T\n", v)
	}

	// 5. fallthrough穿透
	num := 2
	switch num {
	case 2:
		fmt.Println("是2")
		fallthrough // 继续执行下一个case
	case 3:
		fmt.Println("是2或是3") // 也会输出
	case 4:
		fmt.Println("是 4")
	}
}

这是苹果
工作日
良好
字符串: hello
是2
是2或是3
```

### for循环控制

```go
package main

import "fmt"

func main() {
	// 1. 基本for循环（类似其他语言的for）
	fmt.Println("=== 基本for循环 ===")
	for i := 0; i < 3; i++ {
		fmt.Printf("i = %d\n", i)
	}
  // 作用域在 for 中
	//fmt.Println(i) 错误

	// 2. while风格的循环（只有条件）
	fmt.Println("\n=== while风格循环 ===")
	count := 0
	for count < 3 {
		fmt.Printf("count = %d\n", count)
		count++
	}

	// 3. 无限循环（需要break退出）
	fmt.Println("\n=== 无限循环 ===")
	infiniteCount := 0
	for {
		fmt.Printf("无限循环 %d\n", infiniteCount)
		infiniteCount++
		if infiniteCount >= 2 {
			break
		}
	}

	// 4. for-range遍历（重点！）
	fmt.Println("\n=== for-range遍历 ===")
	fruits := []string{"apple", "banana", "orange"}
	for index, fruit := range fruits {
		fmt.Printf("索引: %d, 水果: %s\n", index, fruit)
	}

	// 5. for-range遍历map
	fmt.Println("\n=== for-range遍历map ===")
	scores := map[string]int{"Alice": 90, "Bob": 85}
	for name, score := range scores {
		fmt.Printf("姓名: %s, 分数: %d\n", name, score)
	}

	// 6. continue和break
	fmt.Println("\n=== continue和break ===")
	for i := 0; i < 5; i++ {
		if i == 1 {
			continue // 跳过本次循环
		}
		if i == 3 {
			break // 终止循环
		}
		fmt.Printf("处理: %d\n", i)
	}
}

=== 基本for循环 ===
i = 0
i = 1
i = 2

=== while风格循环 ===
count = 0
count = 1
count = 2

=== 无限循环 ===
无限循环 0
无限循环 1

=== for-range遍历 ===
索引: 0, 水果: apple
索引: 1, 水果: banana
索引: 2, 水果: orange

=== for-range遍历map ===
姓名: Alice, 分数: 90
姓名: Bob, 分数: 85

=== continue和break ===
处理: 0
处理: 2

如果我们的字符串含有中文，那么传统的遍历字符串方式，就是错误，会出现乱码。原因是传统的对字符串的遍历是按照字节来遍历，而一个汉字在utf8编码是对应3个字节。如何解决需要要将str转成[]rune切片
var str1 = "hello"
str2 := []rune(str)
```

### 没有while和do..while

### Break

```go
package main

import "fmt"

func main() {
	// 1. 基本break - 跳出当前循环
	fmt.Println("=== 基本break ===")
	for i := 0; i < 5; i++ {
		if i == 3 {
			break // 当i等于3时跳出循环
		}
		fmt.Printf("i = %d\n", i)
	}

	// 2. 嵌套循环中的break - 只跳出内层循环
	fmt.Println("\n=== 嵌套循环break ===")
	for i := 0; i < 3; i++ {
		for j := 0; j < 3; j++ {
			if j == 1 {
				break // 只跳出内层的j循环
			}
			fmt.Printf("i=%d, j=%d\n", i, j)
		}
	}

	// 3. 带标签的break - 跳出指定循环（重点！）
	fmt.Println("\n=== 带标签的break ===")
OuterLoop:
	for i := 0; i < 3; i++ {
		for j := 0; j < 3; j++ {
			if i == 1 && j == 1 {
				break OuterLoop // 直接跳出外层循环
			}
			fmt.Printf("带标签: i=%d, j=%d\n", i, j)
		}
	}
}

=== 基本break ===
i = 0
i = 1
i = 2

=== 嵌套循环break ===
i=0, j=0
i=1, j=0
i=2, j=0

=== 带标签的break ===
带标签: i=0, j=0
带标签: i=0, j=1
带标签: i=0, j=2
带标签: i=1, j=0
```

### Continue

```go
package main

import "fmt"

func main() {
	// 1. 基本continue - 跳过本次循环
	fmt.Println("=== 基本continue ===")
	for i := 0; i < 5; i++ {
		if i == 2 {
			continue // 跳过i=2的这次循环
		}
		fmt.Printf("i = %d\n", i)
	}

	// 2. 嵌套循环中的continue - 只跳过内层循环的当前迭代
	fmt.Println("\n=== 嵌套循环continue ===")
	for i := 0; i < 3; i++ {
		for j := 0; j < 3; j++ {
			if j == 1 {
				continue // 只跳过内层循环中j=1的情况
			}
			fmt.Printf("i=%d, j=%d\n", i, j)
		}
	}

	// 3. 带标签的continue - 跳到指定循环的下一次迭代（重点！）
	fmt.Println("\n=== 带标签的continue ===")
OuterLoop:
	for i := 0; i < 3; i++ {
		for j := 0; j < 3; j++ {
			if i == 1 && j == 1 {
				continue OuterLoop // 直接跳到外层循环的下一次迭代
			}
			fmt.Printf("带标签: i=%d, j=%d\n", i, j)
		}
	}
}

=== 基本continue ===
i = 0
i = 1
i = 3
i = 4

=== 嵌套循环continue ===
i=0, j=0
i=0, j=2
i=1, j=0
i=1, j=2
i=2, j=0
i=2, j=2

=== 带标签的continue ===
带标签: i=0, j=0
带标签: i=0, j=1
带标签: i=0, j=2
带标签: i=1, j=0
带标签: i=2, j=0
带标签: i=2, j=1
带标签: i=2, j=2
```

### Goto

```go
package main

import "fmt"

func main() {
    // 基本goto语法
    fmt.Println("开始")
    
    goto Label1 // 跳转到Label1标签
    
    fmt.Println("这行不会执行") // 被跳过了
    
Label1:
    fmt.Println("跳转到这里")
    
    // 另一个例子
    i := 0
    
Loop:
    fmt.Printf("i = %d\n", i)
    i++
    if i < 3 {
        goto Loop // 跳回Loop标签，形成循环
    }
    
    fmt.Println("结束")
}

开始
跳转到这里
i = 0
i = 1
i = 2
结束
```

### Return

```go
package main

import "fmt"

// 1. 无返回值函数
func noReturnFunc() {
    fmt.Println("执行无返回值函数")
    return // 可省略，函数末尾自动return
}

// 2. 单返回值函数
func singleReturn() int {
    fmt.Println("返回单个值")
    return 42 // 返回整数
}

// 3. 多返回值函数
func multiReturn() (int, string) {
    fmt.Println("返回多个值")
    return 100, "hello" // 返回两个值
}

// 4. 命名返回值
func namedReturn() (result int, message string) {
    // 返回值已在函数签名中声明
    result = 200
    message = "world"
    return // 自动返回已赋值的result和message
}

// 5. 提前返回
func earlyReturn(x int) string {
    if x < 0 {
        return "负数" // 条件满足时提前返回
    }
    if x == 0 {
        return "零" // 另一个提前返回
    }
    return "正数" // 默认返回
}

func main() {
    // 调用无返回值函数
    noReturnFunc()
    
    // 接收单返回值
    value := singleReturn()
    fmt.Printf("单返回值: %d\n", value)
    
    // 接收多返回值
    num, str := multiReturn()
    fmt.Printf("多返回值: %d, %s\n", num, str)
    
    // 忽略部分返回值
    numOnly, _ := multiReturn() // 忽略第二个返回值
    fmt.Printf("只取第一个返回值: %d\n", numOnly)
    
    // 命名返回值
    n, msg := namedReturn()
    fmt.Printf("命名返回值: %d, %s\n", n, msg)
    
    // 提前返回示例
    fmt.Printf("earlyReturn(-5): %s\n", earlyReturn(-5))
    fmt.Printf("earlyReturn(0): %s\n", earlyReturn(0))
    fmt.Printf("earlyReturn(10): %s\n", earlyReturn(10))
}

执行无返回值函数
返回单个值
单返回值: 42
返回多个值
多返回值: 100, hello
返回多个值
只取第一个返回值: 100
命名返回值: 200, world
earlyReturn(-5): 负数
earlyReturn(0): 零
earlyReturn(10): 正数
```

## 函数、包和错误处理

### 函数

```go
func 函数名（形参列表）(返回值列表){

执行语句

return 返回值列表

}
```

#### 函数使用的注意事项和细节讨论

1) Go函数不支持函数重载

2) 在Go中，函数也是一种数据类型，可以赋值给一个变量，则该变量就是一个函数类型的变量了。通过该变量可以对函数调用

3) 函数既然是一种数据类型，因此在Go中，函数可以作为形参，并且调用

### 包

1. **包声明**：`package 包名`（每个文件必须）
2. **主包**：`package main` + `func main()` 程序入口
3. **导入**：`import "包路径"`
4. **导出规则**：首字母大写的标识符可被外部访问
5. **初始化**：`init()` 函数在导入时自动执行
6. **模块管理**：`go.mod` 定义模块和依赖
7. m "myapp/utils"         // 别名导入

### init函数

```go
// 每一个源文件都可以包含一个init函数，该函数会在main函数执行前，被Go运行框架调用，也就是说init会在main函数前被调用
package main

import "fmt"

var global = "hello world"

func init() {
	fmt.Println("global:", global)
}

func main() {
	fmt.Println("main:", global)
}

执行的流程全局变量定义->init函数->main函数
global: hello world
main: hello world
```

### 匿名函数

```go
package main

import "fmt"

func main() {
	// 1. 基本匿名函数 - 赋值给变量
	add := func(a, b int) int {
		return a + b
	}
	result := add(3, 4)
	fmt.Printf("3 + 4 = %d\n", result)

	// 2. 立即执行匿名函数
	func() {
		fmt.Println("立即执行的匿名函数")
	}()
}

3 + 4 = 7
立即执行的匿名函数
```

### 闭包

**闭包（Closure）** 是一个函数值，它引用了其函数体之外的变量。这个函数可以访问并操作这些外部变量，即使这些变量已经离开了它们原本的作用域。

```go
package main

import "fmt"

func main() {
	// 1. 基本闭包 - 捕获外部变量
	counter := 0
	increment := func() int {
		counter++ // 捕获并修改外部变量
		return counter
	}

	fmt.Printf("计数: %d\n", increment()) // 1
	fmt.Printf("计数: %d\n", increment()) // 2
	fmt.Printf("计数: %d\n", increment()) // 3
	fmt.Printf("最终计数: %d\n", counter)   // 3

	// 2. 闭包记住状态 - 计数器工厂
	createCounter := func() func() int {
		count := 0 // 这个变量被闭包"记住"
		return func() int {
			count++
			return count
		}
	}

	counter1 := createCounter()
	counter2 := createCounter()

	fmt.Printf("计数器1: %d\n", counter1()) // 1
	fmt.Printf("计数器1: %d\n", counter1()) // 2
	fmt.Printf("计数器2: %d\n", counter2()) // 1 (独立的计数)
	fmt.Printf("计数器1: %d\n", counter1()) // 3

	// 3. 带参数的闭包
	multiplier := func(factor int) func(int) int {
		return func(x int) int {
			return x * factor
		}
	}

	double := multiplier(2)
	triple := multiplier(3)

	fmt.Printf("双倍: %d\n", double(5)) // 10
	fmt.Printf("三倍: %d\n", triple(5)) // 15

	// 4. 1.20 版本及以下循环中的闭包陷阱（重点！）
	var funcs []func()
	for i := 0; i < 3; i++ {
		// 错误：所有闭包都捕获同一个i变量
		funcs = append(funcs, func() {
			fmt.Printf("错误: i = %d\n", i)
		})
	}

	for _, f := range funcs {
		f() // 全部输出 i = 3
	}

	// 正确：创建局部副本
	var correctFuncs []func()
	for i := 0; i < 3; i++ {
		j := i // 创建局部变量副本
		correctFuncs = append(correctFuncs, func() {
			fmt.Printf("正确: j = %d\n", j)
		})
	}

	for _, f := range correctFuncs {
		f() // 正确输出 j = 0, 1, 2
	}
}
计数: 1
计数: 2
计数: 3
最终计数: 3
计数器1: 1
计数器1: 2
计数器2: 1
计数器1: 3
双倍: 10
三倍: 15
i = 3
i = 3
i = 3
i = 0
i = 1
i = 2


package main

import "fmt"

func main() {
	// 现代1.21 版本及以上Go中，每次迭代都会创建新的循环变量
	for i := 0; i < 3; i++ {
		// 每次迭代的 i 都是新的变量实例
		func() {
			fmt.Printf("迭代 %d: i地址 = %p\n", i, &i)
		}()
	}
}

迭代 0: i地址 = 0x14000104020
迭代 1: i地址 = 0x14000104030
迭代 2: i地址 = 0x1400010403
```

### defer

```go
package main

import "fmt"

func main() {
	// 1. 基本用法 - 延迟执行
	defer fmt.Println("最后执行")
	fmt.Println("先执行")

	// 2. 执行顺序 - 后进先出
	defer fmt.Println("第三")
	defer fmt.Println("第二")
	defer fmt.Println("第一")

	// 3. 参数立即求值
	x := 1
	defer fmt.Println("x =", x) // 输出 x = 1
	x = 2
}

先执行
x = 1
第一
第二
第三
最后执行
```

### 函数参数传递方式

1) 值传递

2) 引用传递

**其实，不管是值传递还是引用传递，传递给函数的都是变量的副本，不同的是，值传递的是值的拷贝，引用传递的是地址的拷贝，一般来说，地址拷贝效率高，因为数据量小，而值拷贝决定拷贝的数据大小，数据越大，效率越低**

**值类型和引用类型**

1) 值类型：基本数据类型int系列,float系列,bool,string、数组和结构体struct

2) 引用类型：指针、slice切片、map、管道chan、interface等都是引用类型

3) 如果希望函数内的变量能修改函数外的变量，可以传入变量的地址&，函数内以指针的方式操作变量。从效果上看类似引用。

### 变量作用域

1) 函数内部声明/定义的变量叫局部变量，作用域仅限于函数内部

2) 函数外部声明/定义的变量叫全局变量，作用域在整个包都有效，如果其首字母为大写，则作用域在整个程序有效

3) 如果变量是在一个代码块，比如for/if中，那么这个变量的的作用域就在该代码块

### 字符串常用的系统函数

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

func main() {
	str := " Hello, Go World! "

	// 1. 长度和基础操作
	fmt.Printf("1. 长度: %d\n", len(str))
	fmt.Printf("2. 去空格: \"%s\"\n", strings.TrimSpace(str))

	// 3. 大小写转换
	fmt.Printf("3. 大写: %s\n", strings.ToUpper("hello"))
	fmt.Printf("4. 小写: %s\n", strings.ToLower("HELLO"))

	// 5. 包含判断
	fmt.Printf("5. 包含Go: %t\n", strings.Contains(str, "Go"))
	fmt.Printf("6. 前缀: %t\n", strings.HasPrefix(str, " Hello"))
	fmt.Printf("7. 后缀: %t\n", strings.HasSuffix(str, "World! "))

	// 8. 查找和索引
	fmt.Printf("8. Go位置: %d\n", strings.Index(str, "Go"))
	fmt.Printf("9. 最后o位置: %d\n", strings.LastIndex(str, "o"))

	// 10. 替换
	fmt.Printf("10. 替换: %s\n", strings.Replace(str, "World", "Golang", 1))

	// 11. 分割和连接
	parts := strings.Split("a,b,c", ",")
	fmt.Printf("11. 分割: %v\n", parts)
	fmt.Printf("12. 连接: %s\n", strings.Join(parts, "-"))

	// 13. 重复
	fmt.Printf("13. 重复: %s\n", strings.Repeat("Go", 3))

	// 14. 类型转换
	numStr := "123"
	num, _ := strconv.Atoi(numStr)
	fmt.Printf("14. 字符串转数字: %d\n", num)
	fmt.Printf("15. 数字转字符串: %s\n", strconv.Itoa(456))
}
1. 长度: 18
2. 去空格: "Hello, Go World!"
3. 大写: HELLO
4. 小写: hello
5. 包含Go: true
6. 前缀: true
7. 后缀: true
8. Go位置: 8
9. 最后o位置: 12
10. 替换:  Hello, Go Golang! 
11. 分割: [a b c]
12. 连接: a-b-c
13. 重复: GoGoGo
14. 字符串转数字: 123
15. 数字转字符串: 456
```

### 时间和日期相关函数

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// 1. 获取当前时间
	now := time.Now()
	fmt.Printf("1. 当前时间: %v\n", now)

	// 2. 获取时间组成部分
	fmt.Printf("2. 年: %d, 月: %d, 日: %d\n",
		now.Year(), now.Month(), now.Day())
	fmt.Printf("3. 时: %d, 分: %d, 秒: %d\n",
		now.Hour(), now.Minute(), now.Second())

	// 4. 格式化时间（重点！）
	fmt.Printf("4. 标准格式: %s\n", now.Format("2006-01-02 15:04:05"))
	fmt.Printf("5. 日期格式: %s\n", now.Format("2006/01/02"))
	fmt.Printf("6. 时间格式: %s\n", now.Format("15:04:05"))

	// 7. 时间戳
	fmt.Printf("7. 秒时间戳: %d\n", now.Unix())
	fmt.Printf("8. 毫秒时间戳: %d\n", now.UnixMilli())

	// 9. 时间计算
	tomorrow := now.Add(24 * time.Hour)
	fmt.Printf("9. 明天: %s\n", tomorrow.Format("2006-01-02"))

	yesterday := now.Add(-24 * time.Hour)
	fmt.Printf("10. 昨天: %s\n", yesterday.Format("2006-01-02"))

	// 11. 时间比较
	fmt.Printf("11. 现在在明天之前: %t\n", now.Before(tomorrow))
	fmt.Printf("12. 现在在昨天之后: %t\n", now.After(yesterday))

	// 13. 创建特定时间
	specificTime := time.Date(2024, 1, 1, 12, 0, 0, 0, time.Local)
	fmt.Printf("13. 特定时间: %s\n", specificTime.Format("2006-01-02 15:04:05"))

	// 14. 解析字符串时间
	timeStr := "2024-12-25 18:30:00"
	parsedTime, _ := time.Parse("2006-01-02 15:04:05", timeStr)
	fmt.Printf("14. 解析时间: %s\n", parsedTime.Format("2006-01-02 15:04:05"))

	// 15. 时间差
	duration := tomorrow.Sub(now)
	fmt.Printf("15. 到明天还有: %.0f 小时\n", duration.Hours())
}

1. 当前时间: 2025-10-18 20:21:32.283935 +0800 CST m=+0.000104667
2. 年: 2025, 月: 10, 日: 18
3. 时: 20, 分: 21, 秒: 32
4. 标准格式: 2025-10-18 20:21:32
5. 日期格式: 2025/10/18
6. 时间格式: 20:21:32
7. 秒时间戳: 1760790092
8. 毫秒时间戳: 1760790092283
9. 明天: 2025-10-19
10. 昨天: 2025-10-17
11. 现在在明天之前: true
12. 现在在昨天之后: true
13. 特定时间: 2024-01-01 12:00:00
14. 解析时间: 2024-12-25 18:30:00
15. 到明天还有: 24 小时
```

### 内置函数

```go
package main

import "fmt"

func main() {
    // 1. len - 获取长度/容量
    arr := [3]int{1, 2, 3}
    slice := []int{1, 2, 3, 4, 5}
    str := "hello"
    m := map[string]int{"a": 1, "b": 2}
    
    fmt.Printf("1. 数组长度: %d\n", len(arr))
    fmt.Printf("2. 切片长度: %d\n", len(slice))
    fmt.Printf("3. 字符串长度: %d\n", len(str))
    fmt.Printf("4. map长度: %d\n", len(m))

    // 2. new - 分配内存，返回指针
    ptr := new(int)
    *ptr = 100
    fmt.Printf("5. new创建指针: %p, 值: %d\n", ptr, *ptr)
    
    // 3. make - 初始化切片、map、channel
    // 切片
    s := make([]int, 3, 5) // 长度3，容量5
    fmt.Printf("6. make切片: len=%d, cap=%d, %v\n", len(s), cap(s), s)
    
    // map
    dict := make(map[string]int)
    dict["key"] = 42
    fmt.Printf("7. make map: %v\n", dict)
    
    // channel
    ch := make(chan int, 2) // 缓冲大小为2
    ch < - 1
    ch < - 2
    fmt.Printf("8. make channel: %d, %d\n", < -ch, < -ch)
}

1. 数组长度: 3
2. 切片长度: 5
3. 字符串长度: 5
4. map长度: 2
5. new创建指针: 0x14000112020, 值: 100
6. make切片: len=3, cap=5, [0 0 0]
7. make map: map[key:42]
8. make channel: 1, 2
```

#### new

`new` 用于为值类型分配内存，并返回指向该类型的**指针**。

##### 语法：

```go
func new(Type) *Type
```

##### 特点：

- 返回指向新分配零值的指针
- 适用于所有类型
- 内存被初始化为零值

 ```go
  // 为基本类型分配内存
  p1 := new(int)      // *int, 指向 0
  p2 := new(string)   // *string, 指向 ""
  p3 := new(bool)     // *bool, 指向 false
 // 为结构体分配内存
 type Person struct {
     Name string
     Age  int
 }
 p4 := new(Person)   // *Person, 指向 {Name: "", Age: 0}
 ```

```go
package main

import "fmt"

func main() {
	// 1. 直接声明指针 - 零值nil
	var p1 *int
	fmt.Printf("1. 直接声明指针: %p, 值: %v\n", p1, p1)

	// 2. 使用new - 分配内存并初始化为零值
	p2 := new(int)
	fmt.Printf("2. new创建指针: %p, 指向的值: %d\n", p2, *p2)

	// 3. 赋值操作对比
	// p1 是 nil，直接解引用会panic
	// *p1 = 100  // 运行时panic!

	// p2 指向有效的内存，可以安全赋值
	*p2 = 100
	fmt.Printf("3. 赋值后: %d\n", *p2)

	// 4. 取地址操作符 vs new
	x := 42
	p3 := &x       // 取现有变量的地址
	p4 := new(int) // 创建新内存

	fmt.Printf("4. 取地址: %p -> %d\n", p3, *p3)
	fmt.Printf("5. new创建: %p -> %d\n", p4, *p4)

	// 5. 函数参数中的区别
	testPointers()
}

func testPointers() {
	fmt.Println("\n=== 函数参数测试 ===")

	// 直接声明的nil指针
	var nilPtr *int
	processPointer(nilPtr) // 传递nil指针

	// new分配的有效指针
	validPtr := new(int)
	*validPtr = 200
	processPointer(validPtr)
}

func processPointer(p *int) {
	if p == nil {
		fmt.Println("接收到nil指针")
		return
	}
	fmt.Printf("处理指针: %d\n", *p)
}

1. 直接声明指针: 0x0, 值: <nil>
2. new创建指针: 0x14000104020, 指向的值: 0
3. 赋值后: 100
4. 取地址: 0x14000104028 -> 42
5. new创建: 0x14000104030 -> 0

=== 函数参数测试 ===
接收到nil指针
处理指针: 200
```

####  make

`make` 用于创建并初始化**切片、map 和 channel**这三种引用类型。

##### 语法：

```go
// 切片
func make([]T, len, cap) []T

// map
func make(map[K]V, initialCapacity) map[K]V

// channel
func make(chan T, bufferSize) chan T
```

##### 特点

- 只适用于 slice、map、channel
- 返回的是**类型本身**，不是指针
- 会进行初始化，而不仅仅是分配内存

```go
package main

import "fmt"

func main() {
    // 创建切片
    slice1 := make([]int, 5)        // 长度=5, 容量=5
    slice2 := make([]int, 3, 10)    // 长度=3, 容量=10
    
    fmt.Printf("slice1: len=%d, cap=%d, %v\n", len(slice1), cap(slice1), slice1)
    fmt.Printf("slice2: len=%d, cap=%d, %v\n", len(slice2), cap(slice2), slice2)
    
    // 创建 map
    map1 := make(map[string]int)
    map2 := make(map[string]int, 10) // 指定初始容量
    
    map1["a"] = 1
    map2["b"] = 2
    fmt.Printf("map1: %v\n", map1)
    fmt.Printf("map2: %v\n", map2)
    
    // 创建 channel
    ch1 := make(chan int)       // 无缓冲channel
    ch2 := make(chan int, 10)   // 缓冲大小为10的channel
    
    fmt.Printf("ch1: %T\n", ch1)
    fmt.Printf("ch2: %T\n", ch2)
}
```

#### sort

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	// 1. 基本类型排序
	ints := []int{3, 1, 4, 1, 5, 9, 2, 6}
	strings := []string{"banana", "apple", "cherry"}

	sort.Ints(ints)       // 整数排序
	sort.Strings(strings) // 字符串排序

	fmt.Printf("1. 整数排序: %v\n", ints)     // [1 1 2 3 4 5 6 9]
	fmt.Printf("2. 字符串排序: %v\n", strings) // [apple banana cherry]

	// 2. 检查是否已排序
	fmt.Printf("3. 是否已排序: %t\n", sort.IntsAreSorted(ints))

	// 3. 自定义排序 - sort.Slice
	people := []struct {
		Name string
		Age  int
	}{
		{"Alice", 25},
		{"Bob", 30},
		{"Charlie", 20},
	}

	// 按年龄升序排序
	sort.Slice(people, func(i, j int) bool {
		return people[i].Age < people[j].Age
	})
	fmt.Printf("4. 按年龄升序: %v\n", people)

	// 按姓名降序排序
	sort.Slice(people, func(i, j int) bool {
		return people[i].Name > people[j].Name
	})
	fmt.Printf("5. 按姓名降序: %v\n", people)

	// 4. 稳定排序 - sort.SliceStable
	numbers := []int{3, 1, 4, 1, 5, 9, 2, 6}
	sort.SliceStable(numbers, func(i, j int) bool {
		return numbers[i] < numbers[j] // 升序
	})
	fmt.Printf("6. 稳定排序: %v\n", numbers)

	// 5. 反向排序
	sort.Sort(sort.Reverse(sort.IntSlice(ints)))
	fmt.Printf("7. 反向排序: %v\n", ints) // [9 6 5 4 3 2 1 1]

	// 6. 搜索已排序切片
	sorted := []int{1, 3, 5, 7, 9}
	index := sort.SearchInts(sorted, 5)
	fmt.Printf("8. 搜索5的位置: %d\n", index) // 2

	// 7. 自定义搜索条件
	index = sort.Search(len(sorted), func(i int) bool {
		return sorted[i] >= 6 // 第一个 >=6 的元素
	})
	fmt.Printf("9. 第一个>=6的元素: %d\n", sorted[index]) // 7
}

1. 整数排序: [1 1 2 3 4 5 6 9]
2. 字符串排序: [apple banana cherry]
3. 是否已排序: true
4. 按年龄升序: [{Charlie 20} {Alice 25} {Bob 30}]
5. 按姓名降序: [{Charlie 20} {Bob 30} {Alice 25}]
6. 稳定排序: [1 1 2 3 4 5 6 9]
7. 反向排序: [9 6 5 4 3 2 1 1]
8. 搜索5的位置: 2
9. 第一个>=6的元素: 7
```

### 错误处理

```go
package main

import (
	"errors"
	"fmt"
)

func main() {
	// 1. error - 预期的错误处理
	result, err := safeDivide(10, 0)
	if err != nil {
		fmt.Printf("1. error处理: %v\n", err) // 优雅处理错误
	} else {
		fmt.Printf("1. 结果: %d\n", result)
	}

	// 2. panic - 不可恢复的严重错误
	fmt.Println("2. 准备触发panic...")
	defer func() {
		if r := recover(); r != nil {
			fmt.Printf("3. 捕获到panic: %v\n", r) // 恢复程序执行
		}
	}()

	dangerousDivide(10, 0) // 这会触发panic
	fmt.Println("这行不会执行")  // 被panic中断
}

// 使用error处理预期错误
func safeDivide(a, b int) (int, error) {
	if b == 0 {
		return 0, errors.New("除数不能为零")
	}
	return a / b, nil
}

// 使用panic处理不可恢复错误
func dangerousDivide(a, b int) int {
	if b == 0 {
		panic("数学错误：除零操作") // 严重错误，立即停止
	}
	return a / b
}

1. error处理: 除数不能为零
2. 准备触发panic...
3. 捕获到panic: 数学错误：除零操作
```

## 数组

**重要特性**：

- **值类型**：赋值和传参都会复制整个数组
- **长度固定**：创建后不能改变长度
- **可比较**：相同类型的数组可以用 `==` 比较
- **索引访问**：从0开始，`arr[index]`

```go
package main

import "fmt"

func main() {
	// 1. 数组声明和初始化
	var arr1 [3]int           // 声明，零值初始化
	arr2 := [3]int{1, 2, 3}   // 声明并初始化
	arr3 := [...]int{4, 5, 6} // 编译器推导长度

	fmt.Printf("1. arr1: %v\n", arr1) // [0 0 0]
	fmt.Printf("2. arr2: %v\n", arr2) // [1 2 3]
	fmt.Printf("3. arr3: %v\n", arr3) // [4 5 6]

	// 2. 数组长度和容量
	fmt.Printf("4. arr2长度: %d, 容量: %d\n", len(arr2), cap(arr2))

	// 3. 数组访问和修改
	arr2[0] = 100
	fmt.Printf("5. 修改后arr2: %v\n", arr2) // [100 2 3]

	// 4. 数组遍历
	fmt.Printf("6. 数组遍历: ")
	for i := 0; i < len(arr3); i++ {
		fmt.Printf("%d ", arr3[i])
	}
	fmt.Println()

	fmt.Printf("7. range遍历: ")
	for index, value := range arr3 {
		fmt.Printf("arr3[%d]=%d ", index, value)
	}
	fmt.Println()

	// 5. 数组是值类型（重点！）
	arr4 := arr2 // 复制整个数组
	arr4[0] = 999
	fmt.Printf("8. 原数组: %v\n", arr2)  // [100 2 3]
	fmt.Printf("9. 复制数组: %v\n", arr4) // [999 2 3]

	// 6. 多维数组
	matrix := [2][3]int{
		{1, 2, 3},
		{4, 5, 6},
	}
	fmt.Printf("10. 二维数组: %v\n", matrix)

	// 7. 数组比较（只有相同长度和类型的数组可以比较）
	a := [2]int{1, 2}
	b := [2]int{1, 2}
	c := [2]int{1, 3}
	fmt.Printf("11. 数组比较: a==b: %t, a==c: %t\n", a == b, a == c)

	arr5 := [3]int{1, 2, 3}
	modifyArray(arr5)
	fmt.Printf("arr5: %v\n", arr5)

}

// 8. 数组作为函数参数（值传递）
func modifyArray(arr [3]int) {
	arr[0] = 999
	fmt.Printf("12. 函数内修改: %v\n", arr)
}

1. arr1: [0 0 0]
2. arr2: [1 2 3]
3. arr3: [4 5 6]
4. arr2长度: 3, 容量: 3
5. 修改后arr2: [100 2 3]
6. 数组遍历: 4 5 6 
7. range遍历: arr3[0]=4 arr3[1]=5 arr3[2]=6 
8. 原数组: [100 2 3]
9. 复制数组: [999 2 3]
10. 二维数组: [[1 2 3] [4 5 6]]
11. 数组比较: a==b: true, a==c: false
12. 函数内修改: [999 2 3]
arr5: [1 2 3]
```

## 切片

### 底层实现

```
切片内存布局示例: make([]int, 3, 5)

切片变量 s (在栈上):
+--------+--------+--------+
|  ptr   |  len   |  cap   |
| 0x1234 |   3    |   5    |
+--------+--------+--------+
    |
    v
底层数组 (在堆上):
+----+----+----+----+----+
| 1  | 2  | 3  | 0  | 0  |   ← 已使用3个，剩余2个容量
+----+----+----+----+----+
索引: 0    1    2    3    4
```

### 重要特性

**创建方式**：

- `var s []int` - nil切片
- `s := []int{1,2,3}` - 字面量
- `s := make([]int, len, cap)` - make函数
- `s := arr[start:end]` - 从数组切片

**核心特性**：

- **动态大小**：长度可变，自动扩容
- **引用语义**：传递切片不会复制底层数据
- **共享底层**：多个切片可共享同一数组
- **自动扩容**：append时容量不足会自动增长（通常翻倍）

**重要操作**：

- `append()` - 追加元素，可能触发扩容
- `copy()` - 复制切片内容
- `len()` - 获取长度
- `cap()` - 获取容量
- 切片表达式 `[start:end]`

**扩容规则**：

- 容量不足时自动扩容
- 通常扩容为原容量的2倍
- 容量<1024时翻倍，>=1024时增长25%

```go
package main

import "fmt"

func main() {
	// 1. 切片的创建方式
	var s1 []int            // 声明，nil切片
	s2 := []int{1, 2, 3}    // 字面量创建
	s3 := make([]int, 3, 5) // make创建，长度3，容量5

	fmt.Printf("1. s1: %v, len=%d, cap=%d, isnil=%t\n", s1, len(s1), cap(s1), s1 == nil)
	fmt.Printf("2. s2: %v, len=%d, cap=%d\n", s2, len(s2), cap(s2))
	fmt.Printf("3. s3: %v, len=%d, cap=%d\n", s3, len(s3), cap(s3))

	// 2. 从数组创建切片
	arr := [5]int{1, 2, 3, 4, 5}
	slice1 := arr[1:3] // 从索引1到3（不包括3）
	slice2 := arr[:3]  // 从开始到索引3
	slice3 := arr[2:]  // 从索引2到结束

	fmt.Printf("4. slice1: %v\n", slice1) // [2 3]
	fmt.Printf("5. slice2: %v\n", slice2) // [1 2 3]
	fmt.Printf("6. slice3: %v\n", slice3) // [3 4 5]

	// 3. 切片操作会共享底层数组
	slice1[0] = 999
	fmt.Printf("7. 修改后原数组: %v\n", arr) // [1 999 3 4 5]

	// 4. append 操作（重点！）
	s4 := []int{1, 2, 3}
	s4 = append(s4, 4)              // 追加单个元素
	s4 = append(s4, 5, 6, 7)        // 追加多个元素
	s4 = append(s4, []int{8, 9}...) // 追加切片， ...切片解压缩

	fmt.Printf("8. 追加后: %v, len=%d, cap=%d\n", s4, len(s4), cap(s4))

	// 5. 容量自动增长
	s5 := make([]int, 0, 2) // 容量2
	fmt.Printf("9. 初始: len=%d, cap=%d\n", len(s5), cap(s5))
	s5 = append(s5, 1, 2) // 容量刚好
	fmt.Printf("10. 追加2个: len=%d, cap=%d\n", len(s5), cap(s5))
	s5 = append(s5, 3) // 容量不够，自动扩容
	fmt.Printf("11. 追加第3个: len=%d, cap=%d\n", len(s5), cap(s5))

	// 6. copy 操作
	src := []int{1, 2, 3, 4, 5}
	dst := make([]int, 3)
	n := copy(dst, src) // 复制元素
	dst[0] = 100
	fmt.Printf("12. 复制了 %d 个元素: src is: %v, dst is %v\n", n, src, dst)

	// 7. 切片遍历
	fmt.Printf("13. 遍历切片: ")
	for i, v := range s2 {
		fmt.Printf("s2[%d]=%d ", i, v)
	}
	fmt.Println()

	s6 := []int{1, 2, 3, 4, 5}
	modifySlice(s6)
	fmt.Println(s6)
}

// 8. 切片作为函数参数（引用语义）
func modifySlice(s []int) {
	s[0] = 100
}

1. s1: [], len=0, cap=0, isnil=true
2. s2: [1 2 3], len=3, cap=3
3. s3: [0 0 0], len=3, cap=5
4. slice1: [2 3]
5. slice2: [1 2 3]
6. slice3: [3 4 5]
7. 修改后原数组: [1 999 3 4 5]
8. 追加后: [1 2 3 4 5 6 7 8 9], len=9, cap=12
9. 初始: len=0, cap=2
10. 追加2个: len=2, cap=2
11. 追加第3个: len=3, cap=4
12. 复制了 3 个元素: src is: [1 2 3 4 5], dst is [100 2 3]
13. 遍历切片: s2[0]=1 s2[1]=2 s2[2]=3 
[100 2 3 4 5]
```

### ...使用

```go
package main

import "fmt"

// 1. 可变参数函数
func showNames(names ...string) {
    for _, name := range names {
        fmt.Printf("%s ", name)
    }
    fmt.Println()
}

func main() {
    // 2. 切片展开追加
    nums := []int{1, 2, 3}
    var slice []int
    slice = append(slice, nums...)  // 展开切片
    fmt.Printf("切片展开: %v\n", slice)
    
    // 3. 可变参数调用
    showNames("Alice", "Bob")           // 直接传参
    showNames([]string{"Charlie", "David"}...) // 切片展开传参
    
    // 4. 数组长度推导
    arr := [...]int{1, 2, 3, 4, 5}     // 编译器计算长度
    fmt.Printf("数组长度: %d\n", len(arr))
}

切片展开: [1 2 3]
Alice Bob 
Charlie David 
数组长度: 5
```

### 初始化

```go
package main

import (
	"fmt"
)

func main() {
	// 初始化一个空的切片 nums
	var nums []int

	// 初始化一个大小为 7 的切片 nums，元素值默认都为 0
	nums = make([]int, 7)

	// 初始化一个包含元素 1, 3, 5 的切片 nums
	nums = []int{1, 3, 5}

	// 初始化一个大小为 7 的切片 nums，其值全都为 2
	nums = make([]int, 7)
	for i := range nums {
		nums[i] = 2
	}

	fmt.Println(nums)

	// 初始化一个大小为 3 * 3 的布尔切片 dp，其中的值都初始化为 true
	var dp [][]bool
	dp = make([][]bool, 3)
	for i := 0; i < len(dp); i++ {
		row := make([]bool, 3)
		for j := range row {
			row[j] = true
		}
		dp[i] = row
	}

	fmt.Println(dp)
}
```

### 常用操作

```go
package main

import (
	"fmt"
)

func main() {
	n := 10
	// 初始化切片，大小为 10，元素值都为 0
	nums := make([]int, n)

	// 输出：false
	fmt.Println(len(nums) == 0)

	// 输出：10
	fmt.Println(len(nums))

	// 在切片尾部插入一个元素 20
	// append 函数会返回一个新的切片，所以需要将返回值重新赋值给 nums
	nums = append(nums, 20)
	// 输出：11
	fmt.Println(len(nums))

	// 得到切片最后一个元素
	// 输出：20
	fmt.Println(nums[len(nums)-1])

	// 删除切片的最后一个元素
	nums = nums[:len(nums)-1]
	// 输出：10
	fmt.Println(len(nums))

	// 可以通过索引直接取值或修改
	nums[0] = 11
	// 输出：11
	fmt.Println(nums[0])

	// 在索引 3 处插入一个元素 99
	// ... 是展开操作符，表示将切片中的元素展开
	nums = append(nums[:3], append([]int{99}, nums[3:]...)...)

	// 删除索引 2 处的元素
	nums = append(nums[:2], nums[3:]...)

	// 交换 nums[0] 和 nums[1]
	nums[0], nums[1] = nums[1], nums[0]

	// 遍历切片
	// 输出：0 11 99 0 0 0 0 0 0 0
	for _, num := range nums {
		fmt.Print(num, " ")
	}
	fmt.Println()
}
```

### string和slice

```go
package main

import "fmt"

func main() {
	// ====== 底层相似性 ======
	str := "Hello,世界"

	// 1. 底层都是字节序列
	fmt.Printf("1. 字符串字节: ")
	for i := 0; i < len(str); i++ {
		fmt.Printf("%02x ", str[i])
	}
	fmt.Println()

	// 2. 都支持切片操作
	byteSlice := str[0:5] // 字符串切片 → 新字符串
	// byteSlice[0] = 'c' 编译错误：无法赋值
	fmt.Printf("2. 字符串切片: %s\n", byteSlice)

	// ====== 相互转换 ======
	// 3. string → []byte (复制数据)
	byteArr := []byte(str)
	byteArr[0] = 'h' // 修改字节切片
	fmt.Printf("3. 修改字节切片: %s\n", string(byteArr))
	fmt.Printf("4. 原字符串不变: %s\n", str) // 证明数据被复制

	// 4. string → []rune (处理Unicode)
	runeArr := []rune(str)
	fmt.Printf("5. 字符数: %d\n", len(runeArr))
	fmt.Printf("6. 字节数: %d\n", len(str))

	// ====== 重要区别 ======
	// 5. 字符串不可变，切片可变
	slice := []byte{'H', 'e', 'l', 'l', 'o'}
	slice[0] = 'h' // 可以直接修改
	// str[0] = 'h'                 // 编译错误：字符串不可变

	fmt.Printf("7. 切片可变: %s\n", string(slice))
}

// 6. 函数参数传递的相似性
func processString(s string) {
	// s 是只读的
	fmt.Printf("字符串参数: %s\n", s)
}

func processSlice(s []byte) {
	// s 可以修改，会影响调用方
	if len(s) > 0 {
		s[0] = 'X'
	}
}


1. 字符串字节: 48 65 6c 6c 6f 2c e4 b8 96 e7 95 8c 
2. 字符串切片: Hello
3. 修改字节切片: hello,世界
4. 原字符串不变: Hello,世界
5. 字符数: 8
6. 字节数: 12
7. 切片可变: hello
```

### strings.Builder

#### 创建和初始化

```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    // 方法1: 直接声明
    var builder1 strings.Builder
    
    // 方法2: 使用零值
    builder2 := strings.Builder{}
    
    // 方法3: 使用 new 函数
    builder3 := new(strings.Builder)
    
    fmt.Printf("builder1: %T\n", builder1)
    fmt.Printf("builder2: %T\n", builder2)
    fmt.Printf("builder3: %T\n", builder3)
}
builder1: strings.Builder
builder2: strings.Builder
builder3: *strings.Builder
```

#### 写入内容

```go
func writeMethods() {
    var builder strings.Builder
    
    // 1. WriteString - 写入字符串
    builder.WriteString("Hello, ")
    builder.WriteString("World!")
    
    // 2. Write - 写入字节切片
    bytes := []byte(" This is bytes.")
    builder.Write(bytes)
    
    // 3. WriteByte - 写入单个字节
    builder.WriteByte(' ')
    builder.WriteByte('B')
    
    // 4. WriteRune - 写入 Unicode 字符
    builder.WriteRune(' ')
    builder.WriteRune('🎉')
    
    fmt.Println(builder.String()) // Hello, World! This is bytes. B 🎉
}
```

## Map

### 初始化

```go
package main

import (
    "fmt"
)

func main() {
    // 初始化一个空的哈希表 hashmap
    var hashmap map[int]string
    hashmap = make(map[int]string)

    // 初始化一个包含一些键值对的哈希表 hashmap
    hashmap = map[int]string{
        1: "one",
        2: "two",
        3: "three",
    }

    fmt.Println(hashmap)
}
```

### 常用操作

```go
package main

import (
    "fmt"
)

func main() {
    // 初始化哈希表
    hashmap := make(map[int]string)
    hashmap[1] = "one"
    hashmap[2] = "two"
    hashmap[3] = "three"

    // 检查哈希表是否为空，输出：false
    fmt.Println(len(hashmap) == 0)

    // 获取哈希表的大小，输出：3
    fmt.Println(len(hashmap))

    // 查找指定键值是否存在
    // 输出：Key 2 -> two
    if val, exists := hashmap[2]; exists {
        fmt.Println("Key 2 ->", val)
    } else {
        fmt.Println("Key 2 not found.")
    }

    // 获取指定键对应的值，若不存在会返回空字符串
    // 输出：
    fmt.Println(hashmap[4])

    // 插入一个新的键值对
    hashmap[4] = "four"

    // 获取新插入的值，输出：four
    fmt.Println(hashmap[4])

    // 删除键值对
    delete(hashmap, 3)

    // 检查删除后键 3 是否存在
    // 输出：Key 3 not found.
    if val, exists := hashmap[3]; exists {
        fmt.Println("Key 3 ->", val)
    } else {
        fmt.Println("Key 3 not found.")
    }

    // 遍历哈希表
    // 输出（顺序可能不同）：
    // 1 -> one
    // 2 -> two
    // 4 -> four
    for key, value := range hashmap {
        fmt.Printf("%d -> %s\n", key, value)
    }
}
```

### map重点

**创建方式**：

- `var m map[K]V` - nil map（不能直接使用）
- `m := make(map[K]V)` - 使用 make 创建
- `m := map[K]V{...}` - 字面量创建

**基本操作**：

- `m[key] = value` - 添加/修改
- `value := m[key]` - 获取值
- `delete(m, key)` - 删除键值对
- `value, exists := m[key]` - 检查键是否存在

**重要特性**：

- **无序集合**：遍历顺序不固定
- **引用类型**：传递 map 不会复制数据
- **键唯一性**：每个键只能出现一次
- **零值可用**：不存在的键返回值类型的零值
- **自动扩容**

**使用注意事项**：

- nil map 不能添加元素，必须先初始化
- 使用双返回值形式检查键是否存在
- 并发访问需要加锁（非线程安全）

**性能特点**：

- 基于哈希表实现
- 查找、插入、删除平均 O(1) 时间复杂度
- 自动扩容处理

```go
package main

import "fmt"

func main() {
	// 1. Map 的创建和初始化
	var m1 map[string]int // 声明，nil map, 不分配空间，未初始化，不能直接赋值
	//m1["key"] = 2
	//fmt.Println(m1) // panic: assignment to entry in nil map

	m2 := make(map[string]int) // 使用 make 创建
	m3 := map[string]int{      // 字面量创建
		"apple":  5,
		"banana": 3,
	}

	fmt.Printf("1. m1: %v, isnil: %t\n", m1, m1 == nil)
	fmt.Printf("2. m2: %v\n", m2)
	fmt.Printf("3. m3: %v\n", m3)

	// 2. Map 的基本操作
	// 添加/修改元素
	m3["orange"] = 8
	m3["apple"] = 10 // 修改已存在的键

	// 获取元素
	appleCount := m3["apple"]
	fmt.Printf("4. apple数量: %d\n", appleCount)

	// 3. 检查键是否存在
	count, exists := m3["grape"]
	if exists {
		fmt.Printf("5. grape数量: %d\n", count)
	} else {
		fmt.Printf("5. grape不存在\n")
	}

	// 4. 删除元素
	delete(m3, "banana")
	fmt.Printf("6. 删除banana后: %v\n", m3)

	// 5. 遍历 Map
	fmt.Printf("7. 遍历map: ")
	for key, value := range m3 {
		fmt.Printf("%s:%d ", key, value)
	}
	fmt.Println()

	// 6. Map 长度
	fmt.Printf("8. map长度: %d\n", len(m3))

	// 7. 复杂值类型
	scores := map[string][]int{
		"Alice": {85, 90, 78},
		"Bob":   {92, 88, 95},
	}
	fmt.Printf("9. 复杂map: %v\n", scores)

	// 8. Map 作为函数参数
	modifyMap(m3)
	fmt.Printf("10. 函数修改后: %v\n", m3)
}

// Map 作为函数参数是引用传递
func modifyMap(m map[string]int) {
	m["watermelon"] = 15
}

1. m1: map[], isnil: true
2. m2: map[]
3. m3: map[apple:5 banana:3]
4. apple数量: 10
5. grape不存在
6. 删除banana后: map[apple:10 orange:8]
7. 遍历map: orange:8 apple:10 
8. map长度: 2
9. 复杂map: map[Alice:[85 90 78] Bob:[92 88 95]]
10. 函数修改后: map[apple:10 orange:8 watermelon:15]
```

### map排序

**基本介绍**

1) golang中没有一个专门的方法针对map的key进行排序

2) golang中的map默认是无序的，注意也不是按照添加的顺序存放的，你每次遍历，得到的输出可能不一样.

3) golang中map的排序，是先将key进行排序，然后根据key值遍历输出即可

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	// 1. 创建测试 map
	scores := map[string]int{
		"Charlie": 85,
		"Alice":   92,
		"Bob":     78,
		"David":   96,
	}

	fmt.Println("原始map（无序）:", scores)

	// 2. 按键排序（字母顺序）
	fmt.Println("\n=== 按键排序 ===")
	keys := make([]string, 0, len(scores))
	for k := range scores { // 只获取 key 值，可省略最后一个value 获取
		keys = append(keys, k)
	}
	sort.Strings(keys) // 对键排序

	fmt.Print("按键排序结果: ")
	for _, k := range keys {
		fmt.Printf("%s:%d ", k, scores[k])
	}
	fmt.Println()

	// 3. 按值排序（分数从高到低）
	fmt.Println("\n=== 按值排序 ===")
	type kv struct {
		Key   string
		Value int
	}

	var pairs []kv
	for k, v := range scores {
		pairs = append(pairs, kv{k, v})
	}

	// 按值降序排序
	sort.Slice(pairs, func(i, j int) bool {
		return pairs[i].Value > pairs[j].Value
	})

	fmt.Print("按值排序结果: ")
	for _, pair := range pairs {
		fmt.Printf("%s:%d ", pair.Key, pair.Value)
	}
	fmt.Println()

	// 4. 整数键的排序
	fmt.Println("\n=== 整数键排序 ===")
	intMap := map[int]string{
		300: "三百",
		100: "一百",
		200: "二百",
	}

	intKeys := make([]int, 0, len(intMap))
	for k := range intMap {
		intKeys = append(intKeys, k)
	}
	sort.Ints(intKeys)

	fmt.Print("整数键排序: ")
	for _, k := range intKeys {
		fmt.Printf("%d:%s ", k, intMap[k])
	}
	fmt.Println()

	// 5. 自定义排序规则
	fmt.Println("\n=== 自定义排序 ===")
	customSort(scores)
}

func customSort(m map[string]int) {
	type kv struct {
		Key   string
		Value int
	}

	var pairs []kv
	for k, v := range m {
		pairs = append(pairs, kv{k, v})
	}

	// 自定义排序：先按值降序，值相同时按键升序
	sort.Slice(pairs, func(i, j int) bool {
		if pairs[i].Value == pairs[j].Value {
			return pairs[i].Key < pairs[j].Key
		}
		return pairs[i].Value > pairs[j].Value
	})

	fmt.Print("自定义排序: ")
	for _, pair := range pairs {
		fmt.Printf("%s:%d ", pair.Key, pair.Value)
	}
	fmt.Println()
}


原始map（无序）: map[Alice:92 Bob:78 Charlie:85 David:96]

=== 按键排序 ===
按键排序结果: Alice:92 Bob:78 Charlie:85 David:96 

=== 按值排序 ===
按值排序结果: David:96 Alice:92 Charlie:85 Bob:78 

=== 整数键排序 ===
整数键排序: 100:一百 200:二百 300:三百 

=== 自定义排序 ===
自定义排序: David:96 Alice:92 Charlie:85 Bob:78 
```

## 面向对象编程

### 1.结构体

```go
package main

import "fmt"

// 结构体定义 - 封装相关数据
type Person struct {
    // 字段名大写表示公开，小写表示私有（包内可见）
    name    string  // 私有字段
    Age     int     // 公开字段
    Email   string  // 公开字段
}

// 构造函数模式
func NewPerson(name string, age int, email string) *Person {
    return &Person{
        name:  name,
        Age:   age,
        Email: email,
    }
}

// Getter方法 - 访问私有字段
func (p *Person) GetName() string {
    return p.name
}

// Setter方法 - 修改私有字段
func (p *Person) SetName(name string) {
    p.name = name
}

func main() {
    // 创建对象实例
    person1 := NewPerson("张三", 25, "zhang@example.com")
    
    // 访问公开字段
    fmt.Printf("年龄: %d, 邮箱: %s\n", person1.Age, person1.Email)
    
    // 通过方法访问私有字段
    fmt.Printf("姓名: %s\n", person1.GetName())
    
    // 修改私有字段
    person1.SetName("张小三")
    fmt.Printf("修改后姓名: %s\n", person1.GetName())
}

年龄: 25, 邮箱: zhang@example.com
姓名: 张三
修改后姓名: 张小三
```

### 2.方法

值接收者 vs 指针接收者


```go
package main

import "fmt"

type BankAccount struct {
    accountNumber string
    balance       float64
    owner         string
}

// 构造函数
func NewBankAccount(number, owner string, initialBalance float64) *BankAccount {
    return &BankAccount{
        accountNumber: number,
        balance:       initialBalance,
        owner:         owner,
    }
}

// 值接收者方法 - 不修改原对象，适合只读操作
func (b BankAccount) DisplayInfo() {
    fmt.Printf("账户: %s, 户主: %s, 余额: ¥%.2f\n", 
        b.accountNumber, b.owner, b.balance)
}

// 指针接收者方法 - 修改原对象，适合写操作
func (b *BankAccount) Deposit(amount float64) {
    if amount > 0 {
        b.balance += amount
        fmt.Printf("存款成功: +¥%.2f\n", amount)
    }
}

// 指针接收者方法
func (b *BankAccount) Withdraw(amount float64) bool {
    if amount > 0 && b.balance >= amount {
        b.balance -= amount
        fmt.Printf("取款成功: -¥%.2f\n", amount)
        return true
    }
    fmt.Printf("取款失败: 余额不足\n")
    return false
}

// 指针接收者方法
func (b *BankAccount) Transfer(amount float64, target *BankAccount) bool {
    if b.Withdraw(amount) {
        target.Deposit(amount)
        fmt.Printf("转账成功: %s → %s, 金额: ¥%.2f\n", 
            b.accountNumber, target.accountNumber, amount)
        return true
    }
    return false
}

func main() {
    // 创建账户
    account1 := NewBankAccount("001", "Alice", 1000.0)
    account2 := NewBankAccount("002", "Bob", 500.0)
    
    // 使用方法
    account1.DisplayInfo()
    account2.DisplayInfo()
    
    account1.Deposit(200.0)
    account1.Withdraw(100.0)
    account1.Transfer(300.0, account2)
    
    fmt.Println("\n操作后状态:")
    account1.DisplayInfo()
    account2.DisplayInfo()
}

账户: 001, 户主: Alice, 余额: ¥1000.00
账户: 002, 户主: Bob, 余额: ¥500.00
存款成功: +¥200.00
取款成功: -¥100.00
取款成功: -¥300.00
存款成功: +¥300.00
转账成功: 001 → 002, 金额: ¥300.00

操作后状态:
账户: 001, 户主: Alice, 余额: ¥800.00
账户: 002, 户主: Bob, 余额: ¥800.00
```

### 3.接口

接口定义和实现

```go
package main

import (
    "fmt"
    "math"
)

// 定义接口 - 行为契约
type Shape interface {
    Area() float64
    Perimeter() float64
    Name() string
}

type Drawable interface {
    Draw()
}

// 矩形实现
type Rectangle struct {
    Width  float64
    Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func (r Rectangle) Perimeter() float64 {
    return 2 * (r.Width + r.Height)
}

func (r Rectangle) Name() string {
    return "矩形"
}

func (r Rectangle) Draw() {
    fmt.Printf("绘制%s: 宽%.1f, 高%.1f\n", r.Name(), r.Width, r.Height)
}

// 圆形实现
type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return math.Pi * c.Radius * c.Radius
}

func (c Circle) Perimeter() float64 {
    return 2 * math.Pi * c.Radius
}

func (c Circle) Name() string {
    return "圆形"
}

func (c Circle) Draw() {
    fmt.Printf("绘制%s: 半径%.1f\n", c.Name(), c.Radius)
}

// 三角形实现
type Triangle struct {
    A, B, C float64
}

func (t Triangle) Area() float64 {
    // 使用海伦公式
    s := t.Perimeter() / 2
    return math.Sqrt(s * (s - t.A) * (s - t.B) * (s - t.C))
}

func (t Triangle) Perimeter() float64 {
    return t.A + t.B + t.C
}

func (t Triangle) Name() string {
    return "三角形"
}

func (t Triangle) Draw() {
    fmt.Printf("绘制%s: 边长%.1f,%.1f,%.1f\n", t.Name(), t.A, t.B, t.C)
}

// 使用接口的函数
func PrintShapeInfo(s Shape) {
    fmt.Printf("%s - 面积: %.2f, 周长: %.2f\n", 
        s.Name(), s.Area(), s.Perimeter())
}

func DrawShapes(drawables []Drawable) {
    fmt.Println("开始绘制图形:")
    for _, d := range drawables {
        d.Draw()
    }
}

func main() {
    // 创建不同形状
    shapes := []Shape{
        Rectangle{Width: 5, Height: 3},
        Circle{Radius: 4},
        Triangle{A: 3, B: 4, C: 5},
    }
    
    drawables := []Drawable{
        Rectangle{Width: 5, Height: 3},
        Circle{Radius: 4},
        Triangle{A: 3, B: 4, C: 5},
    }
    
    // 多态：统一处理不同实现
    fmt.Println("=== 图形信息 ===")
    for _, shape := range shapes {
        PrintShapeInfo(shape)
    }
    
    fmt.Println()
    DrawShapes(drawables)
    
    // 类型断言
    fmt.Println("\n=== 类型断言示例 ===")
    for _, shape := range shapes {
        if circle, ok := shape.(Circle); ok {
            fmt.Printf("发现圆形，半径: %.1f\n", circle.Radius)
        }
    }
    
    // 类型开关
    fmt.Println("\n=== 类型开关示例 ===")
    for _, shape := range shapes {
        switch s := shape.(type) {
        case Rectangle:
            fmt.Printf("矩形: 宽%.1f, 高%.1f\n", s.Width, s.Height)
        case Circle:
            fmt.Printf("圆形: 半径%.1f\n", s.Radius)
        case Triangle:
            fmt.Printf("三角形: 边长%.1f,%.1f,%.1f\n", s.A, s.B, s.C)
        default:
            fmt.Printf("未知图形: %T\n", s)
        }
    }
}

=== 图形信息 ===
矩形 - 面积: 15.00, 周长: 16.00
圆形 - 面积: 50.27, 周长: 25.13
三角形 - 面积: 6.00, 周长: 12.00

开始绘制图形:
绘制矩形: 宽5.0, 高3.0
绘制圆形: 半径4.0
绘制三角形: 边长3.0,4.0,5.0

=== 类型断言示例 ===
发现圆形，半径: 4.0

=== 类型开关示例 ===
矩形: 宽5.0, 高3.0
圆形: 半径4.0
三角形: 边长3.0,4.0,5.0
```

### 4.组合

结构体嵌入和方法提升

```go
package main

import "fmt"

// 基础类型
type Animal struct {
    Name string
    Age  int
}

func (a *Animal) Speak() {
    fmt.Printf("%s发出声音\n", a.Name)
}

func (a *Animal) Sleep() {
    fmt.Printf("%s在睡觉\n", a.Name)
}

// 通过嵌入实现组合
type Dog struct {
    Animal        // 嵌入Animal，获得其所有字段和方法
    Breed  string // 狗特有的字段
}

// 重写方法
func (d *Dog) Speak() {
    fmt.Printf("%s汪汪叫 (品种: %s)\n", d.Name, d.Breed)
}

// 狗特有的方法
func (d *Dog) Fetch() {
    fmt.Printf("%s在接飞盘\n", d.Name)
}

type Cat struct {
    Animal       // 嵌入Animal
    Color string // 猫特有的字段
}

// 重写方法
func (c *Cat) Speak() {
    fmt.Printf("%s喵喵叫 (颜色: %s)\n", c.Name, c.Color)
}

// 猫特有的方法
func (c *Cat) Climb() {
    fmt.Printf("%s在爬树\n", c.Name)
}

// 接口定义
type Pet interface {
    Speak()
    Sleep()
    GetName() string
}

func (a *Animal) GetName() string {
    return a.Name
}

func PlayWithPet(p Pet) {
    fmt.Printf("和%s玩耍: ", p.GetName())
    p.Speak()
}

func main() {
    // 创建对象
    dog := &Dog{
        Animal: Animal{Name: "旺财", Age: 3},
        Breed:  "金毛",
    }
    
    cat := &Cat{
        Animal: Animal{Name: "咪咪", Age: 2},
        Color:  "白色",
    }
    
    // 使用嵌入的方法（方法提升）
    dog.Sleep()  // 来自Animal
    cat.Sleep()  // 来自Animal
    
    // 使用重写的方法
    dog.Speak()  // Dog自己的实现
    cat.Speak()  // Cat自己的实现
    
    // 使用特有的方法
    dog.Fetch()
    cat.Climb()
    
    // 多态：通过接口统一处理
    fmt.Println("\n=== 多态演示 ===")
    pets := []Pet{dog, cat}
    for _, pet := range pets {
        PlayWithPet(pet)
    }
    
    // 访问嵌入字段
    fmt.Printf("\n=== 字段访问 ===\n")
    fmt.Printf("狗的名字: %s, 年龄: %d\n", dog.Name, dog.Age)
    fmt.Printf("猫的名字: %s, 年龄: %d\n", cat.Name, cat.Animal.Age) // 两种访问方式
}

旺财在睡觉
咪咪在睡觉
旺财汪汪叫 (品种: 金毛)
咪咪喵喵叫 (颜色: 白色)
旺财在接飞盘
咪咪在爬树

=== 多态演示 ===
和旺财玩耍: 旺财汪汪叫 (品种: 金毛)
和咪咪玩耍: 咪咪喵喵叫 (颜色: 白色)

=== 字段访问 ===
狗的名字: 旺财, 年龄: 3
猫的名字: 咪咪, 年龄: 2
```

### 5.空接口和类型断言

处理任意类型

```go
package main

import "fmt"

// 空接口可以存储任何值
func printAny(value interface{}) {
    fmt.Printf("值: %v, 类型: %T\n", value, value)
}

// 类型安全的处理
func processValue(value interface{}) {
    switch v := value.(type) {
    case int:
        fmt.Printf("整数: %d, 平方: %d\n", v, v*v)
    case string:
        fmt.Printf("字符串: %s, 长度: %d\n", v, len(v))
    case bool:
        fmt.Printf("布尔值: %t, 取反: %t\n", v, !v)
    case []int:
        fmt.Printf("整数切片: %v, 长度: %d\n", v, len(v))
    default:
        fmt.Printf("未知类型: %T, 值: %v\n", v, v)
    }
}

// 类型断言示例
func safeStringConversion(value interface{}) (string, bool) {
    if str, ok := value.(string); ok {
        return str, true
    }
    return "", false
}

func main() {
    // 空接口可以存储任何类型
    var anything interface{}
    
    anything = 42
    printAny(anything)
    
    anything = "Hello, Go!"
    printAny(anything)
    
    anything = []int{1, 2, 3}
    printAny(anything)
    
    fmt.Println("\n=== 类型安全处理 ===")
    processValue(100)
    processValue("Golang")
    processValue(true)
    processValue([]int{1, 2, 3, 4, 5})
    processValue(3.14)
    
    fmt.Println("\n=== 类型断言 ===")
    if str, ok := safeStringConversion("hello"); ok {
        fmt.Printf("转换成功: %s\n", str)
    } else {
        fmt.Println("转换失败")
    }
    
    if str, ok := safeStringConversion(123); ok {
        fmt.Printf("转换成功: %s\n", str)
    } else {
        fmt.Println("转换失败")
    }
}

值: 42, 类型: int
值: Hello, Go!, 类型: string
值: [1 2 3], 类型: []int

=== 类型安全处理 ===
整数: 100, 平方: 10000
字符串: Golang, 长度: 6
布尔值: true, 取反: false
整数切片: [1 2 3 4 5], 长度: 5
未知类型: float64, 值: 3.14

=== 类型断言 ===
转换成功: hello
转换失败
```

### 总结

Go语言面向对象核心特性：

| 特性     | Go实现方式          | 关键点                               |
| :------- | :------------------ | :----------------------------------- |
| **封装** | 结构体 + 大小写控制 | 字段/方法名大写公开，小写私有        |
| **继承** | 结构体嵌入（组合）  | 通过嵌入获得字段和方法，支持方法重写 |
| **多态** | 接口实现            | 隐式接口，鸭子类型，类型断言         |
| **抽象** | 接口定义            | 只定义行为，不包含实现               |

### 重要原则：

1. **组合优于继承**：Go通过结构体嵌入实现代码复用
2. **接口隔离**：定义小而专注的接口
3. **依赖倒置**：依赖抽象（接口），而不是具体实现
4. **开闭原则**：对扩展开放，对修改关闭

## 文件操作

### 基本文件操作

| 操作          | 函数/方法                          | 说明                       |
| :------------ | :--------------------------------- | :------------------------- |
| **创建/写入** | `os.Create()`, `os.OpenFile()`     | 创建新文件或覆盖现有文件   |
| **读取**      | `os.ReadFile()`, `os.Open()`       | 读取整个文件或打开文件句柄 |
| **追加**      | `os.OpenFile()` with `os.O_APPEND` | 在文件末尾添加内容         |
| **删除**      | `os.Remove()`                      | 删除文件                   |
| **信息**      | `os.Stat()`                        | 获取文件元数据             |

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "path/filepath"
)

func main() {
    filename := "test.txt"
    
    // 1. 写入文件
    fmt.Println("=== 写入文件 ===")
    if err := writeFile(filename, "Hello, Go!\n这是第二行\n"); err != nil {
        fmt.Printf("写入失败: %v\n", err)
        return
    }
    fmt.Println("文件写入成功")
    
    // 2. 读取整个文件
    fmt.Println("\n=== 读取整个文件 ===")
    if content, err := readFile(filename); err != nil {
        fmt.Printf("读取失败: %v\n", err)
    } else {
        fmt.Printf("文件内容:\n%s", content)
    }
    
    // 3. 逐行读取
    fmt.Println("\n=== 逐行读取 ===")
    if err := readLineByLine(filename); err != nil {
        fmt.Printf("逐行读取失败: %v\n", err)
    }
    
    // 4. 追加内容
    fmt.Println("\n=== 追加内容 ===")
    if err := appendToFile(filename, "\n这是追加的内容"); err != nil {
        fmt.Printf("追加失败: %v\n", err)
    } else {
        fmt.Println("内容追加成功")
    }
    
    // 5. 文件信息
    fmt.Println("\n=== 文件信息 ===")
    if err := fileInfo(filename); err != nil {
        fmt.Printf("获取文件信息失败: %v\n", err)
    }
    
    // 6. 复制文件
    fmt.Println("\n=== 复制文件 ===")
    copyFile := "test_copy.txt"
    if err := copyFileContent(filename, copyFile); err != nil {
        fmt.Printf("复制失败: %v\n", err)
    } else {
        fmt.Printf("文件已复制到: %s\n", copyFile)
    }
    
    // 7. 删除文件
    fmt.Println("\n=== 清理 ===")
    if err := cleanupFiles([]string{filename, copyFile}); err != nil {
        fmt.Printf("清理失败: %v\n", err)
    } else {
        fmt.Println("文件清理完成")
    }
    
    // 8. 临时文件
    fmt.Println("\n=== 临时文件 ===")
    useTempFile()
}

// 1. 写入文件
func writeFile(filename, content string) error {
    // os.O_CREATE: 如果不存在则创建
    // os.O_WRONLY: 只写模式  
    // os.O_TRUNC: 如果存在则清空
    file, err := os.OpenFile(filename, os.O_CREATE|os.O_WRONLY|os.O_TRUNC, 0644)
    if err != nil {
        return err
    }
    defer file.Close()
    
    // 使用 bufio 提高写入性能
    writer := bufio.NewWriter(file)
    _, err = writer.WriteString(content)
    if err != nil {
        return err
    }
    
    // 确保所有数据刷入磁盘
    return writer.Flush()
}

// 2. 读取整个文件
func readFile(filename string) (string, error) {
    // 使用 os.ReadFile 简化读取（适合小文件）
    content, err := os.ReadFile(filename)
    if err != nil {
        return "", err
    }
    return string(content), nil
}

// 3. 逐行读取（适合大文件）
func readLineByLine(filename string) error {
    file, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer file.Close()
    
    scanner := bufio.NewScanner(file)
    lineNum := 1
    for scanner.Scan() {
        fmt.Printf("第%d行: %s\n", lineNum, scanner.Text())
        lineNum++
    }
    
    return scanner.Err()
}

// 4. 追加内容
func appendToFile(filename, content string) error {
    // os.O_APPEND: 追加模式
    file, err := os.OpenFile(filename, os.O_APPEND|os.O_WRONLY|os.O_CREATE, 0644)
    if err != nil {
        return err
    }
    defer file.Close()
    
    _, err = file.WriteString(content)
    return err
}

// 5. 获取文件信息
func fileInfo(filename string) error {
    info, err := os.Stat(filename)
    if err != nil {
        return err
    }
    
    fmt.Printf("文件名: %s\n", info.Name())
    fmt.Printf("大小: %d 字节\n", info.Size())
    fmt.Printf("权限: %s\n", info.Mode())
    fmt.Printf("修改时间: %s\n", info.ModTime())
    fmt.Printf("是否是目录: %t\n", info.IsDir())
    
    return nil
}

// 6. 复制文件
func copyFileContent(src, dst string) error {
    // 打开源文件
    source, err := os.Open(src)
    if err != nil {
        return err
    }
    defer source.Close()
    
    // 创建目标文件
    destination, err := os.Create(dst)
    if err != nil {
        return err
    }
    defer destination.Close()
    
    // 使用 io.Copy 高效复制
    _, err = io.Copy(destination, source)
    return err
}

// 7. 清理文件
func cleanupFiles(files []string) error {
    for _, file := range files {
        if err := os.Remove(file); err != nil && !os.IsNotExist(err) {
            return err
        }
    }
    return nil
}

// 8. 使用临时文件
func useTempFile() {
    // 创建临时文件
    tempFile, err := os.CreateTemp(".", "temp_*.txt")
    if err != nil {
        fmt.Printf("创建临时文件失败: %v\n", err)
        return
    }
    defer os.Remove(tempFile.Name()) // 程序结束时删除
    
    fmt.Printf("临时文件: %s\n", tempFile.Name())
    
    // 写入临时数据
    tempFile.WriteString("这是临时数据\n")
    tempFile.Close()
    
    // 读取验证
    content, _ := os.ReadFile(tempFile.Name())
    fmt.Printf("临时文件内容: %s", string(content))
    
    // 临时文件会自动在 defer 中删除
}

// 9. 文件存在性检查
func checkFileExists(filename string) {
    if _, err := os.Stat(filename); os.IsNotExist(err) {
        fmt.Printf("文件 %s 不存在\n", filename)
    } else {
        fmt.Printf("文件 %s 存在\n", filename)
    }
}

// 10. 目录操作示例
func directoryOperations() {
    dir := "test_dir"
    
    // 创建目录
    os.Mkdir(dir, 0755)
    
    // 在目录中创建文件
    filepath := filepath.Join(dir, "nested.txt")
    os.WriteFile(filepath, []byte("嵌套文件内容"), 0644)
    
    // 列出目录内容
    entries, _ := os.ReadDir(dir)
    fmt.Printf("目录 %s 中的文件:\n", dir)
    for _, entry := range entries {
        fmt.Printf("  - %s\n", entry.Name())
    }
    
    // 清理
    os.RemoveAll(dir)
}


=== 写入文件 ===
文件写入成功

=== 读取整个文件 ===
文件内容:
Hello, Go!
这是第二行

=== 逐行读取 ===
第1行: Hello, Go!
第2行: 这是第二行

=== 追加内容 ===
内容追加成功

=== 文件信息 ===
文件名: test.txt
大小: 50 字节
权限: -rw-r--r--
修改时间: 2024-01-15 10:30:45 +0800 CST
是否是目录: false

=== 复制文件 ===
文件已复制到: test_copy.txt

=== 清理 ===
文件清理完成

=== 临时文件 ===
临时文件: ./temp_123456.txt
临时文件内容: 这是临时数据
```

### 参数

```go
package main

import (
    "flag"
    "fmt"
    "os"
    "strings"
)

// 全局变量存储命令行参数
var (
    name     string
    age      int
    married  bool
    salary   float64
    hobbies  string
    count    int
    verbose  bool
    version  bool
)

// 初始化命令行参数
func init() {
    // 基本类型参数
    flag.StringVar(&name, "name", "匿名", "用户姓名")
    flag.StringVar(&name, "n", "匿名", "用户姓名(简写)")
    
    flag.IntVar(&age, "age", 0, "用户年龄")
    flag.IntVar(&age, "a", 0, "用户年龄(简写)")
    
    flag.BoolVar(&married, "married", false, "婚姻状况")
    flag.BoolVar(&married, "m", false, "婚姻状况(简写)")
    
    flag.Float64Var(&salary, "salary", 0.0, "月薪")
    flag.Float64Var(&salary, "s", 0.0, "月薪(简写)")
    
    // 特殊参数
    flag.StringVar(&hobbies, "hobbies", "", "爱好列表(逗号分隔)")
    flag.IntVar(&count, "count", 1, "重复次数")
    flag.BoolVar(&verbose, "verbose", false, "详细输出模式")
    flag.BoolVar(&verbose, "v", false, "详细输出模式(简写)")
    flag.BoolVar(&version, "version", false, "显示版本信息")
}

func main() {
    // 显示自定义用法说明
    flag.Usage = func() {
        fmt.Fprintf(os.Stderr, "Usage: %s [OPTIONS] [COMMAND] [ARGUMENTS...]\n", os.Args[0])
        fmt.Fprintln(os.Stderr, "\nOptions:")
        flag.PrintDefaults()
        fmt.Fprintln(os.Stderr, "\nCommands:")
        fmt.Fprintln(os.Stderr, "  help    显示帮助信息")
        fmt.Fprintln(os.Stderr, "  greet   打招呼")
        fmt.Fprintln(os.Stderr, "  repeat  重复消息")
        fmt.Fprintln(os.Stderr, "\nExamples:")
        fmt.Fprintf(os.Stderr, "  %s -name Alice -age 25 greet\n", os.Args[0])
        fmt.Fprintf(os.Stderr, "  %s -n Bob -a 30 -m repeat \"Hello World\"\n", os.Args[0])
    }

    // 解析命令行参数
    flag.Parse()

    // 处理特殊标志
    if version {
        showVersion()
        return
    }

    // 获取非标志参数（命令和参数）
    args := flag.Args()
    if len(args) == 0 {
        flag.Usage()
        return
    }

    // 根据命令路由到不同处理函数
    switch args[0] {
    case "help":
        flag.Usage()
    case "greet":
        handleGreet(args[1:])
    case "repeat":
        handleRepeat(args[1:])
    default:
        fmt.Printf("未知命令: %s\n", args[0])
        flag.Usage()
    }
}

// 显示版本信息
func showVersion() {
    fmt.Println("命令行工具 v1.0.0")
    fmt.Println("编译时间: 2024-01-15")
}

// 处理打招呼命令
func handleGreet(args []string) {
    if verbose {
        fmt.Println("=== 详细模式 ===")
        fmt.Printf("姓名: %s\n", name)
        fmt.Printf("年龄: %d\n", age)
        fmt.Printf("婚姻状况: %t\n", married)
        fmt.Printf("月薪: ¥%.2f\n", salary)
        
        if hobbies != "" {
            hobbyList := strings.Split(hobbies, ",")
            fmt.Printf("爱好: %v\n", hobbyList)
        }
        fmt.Println("================")
    }
    
    greeting := fmt.Sprintf("你好，%s！", name)
    if age > 0 {
        greeting += fmt.Sprintf(" 你今年%d岁", age)
    }
    if married {
        greeting += "，已结婚"
    } else {
        greeting += "，未婚"
    }
    if salary > 0 {
        greeting += fmt.Sprintf("，月薪¥%.2f", salary)
    }
    
    fmt.Println(greeting)
}

// 处理重复命令
func handleRepeat(args []string) {
    if len(args) == 0 {
        fmt.Println("错误: 请提供要重复的消息")
        return
    }
    
    message := strings.Join(args, " ")
    
    if verbose {
        fmt.Printf("重复设置: 次数=%d, 消息=\"%s\"\n", count, message)
    }
    
    for i := 0; i < count; i++ {
        fmt.Printf("[%d/%d] %s\n", i+1, count, message)
    }
}

// 辅助函数：演示直接使用 flag 包的方法
func demonstrateFlagMethods() {
    // 这些是 flag 包的其他有用方法
    
    // 1. 直接定义并获取参数值
    debug := flag.Bool("debug", false, "启用调试模式")
    port := flag.Int("port", 8080, "服务端口")
    
    // 需要调用 flag.Parse() 来解析，但这里只是演示
    // flag.Parse()
    
    // 2. 查看已设置的参数
    // flag.Visit() 和 flag.VisitAll() 可以遍历参数
    
    // 避免未使用变量警告
    _ = debug
    _ = port
}
```

### JSON

```go
package main

import (
	"encoding/json"
	"fmt"
)

// 基础结构体
type User struct {
	Name     string   `json:"name"`
	Age      int      `json:"age"`
	Email    string   `json:"email,omitempty"` // 为空时不输出
	Password string   `json:"-"`               // 忽略该字段
	Tags     []string `json:"tags"`
}

func main() {
	// 1. 结构体 → JSON
	user := User{
		Name:     "张三", 
		Age:      25,
		Tags:     []string{"go", "backend"},
		Password: "secret",
	}
	
	jsonData, _ := json.MarshalIndent(user, "", "  ")
	fmt.Println("1. 结构体转JSON:")
	fmt.Println(string(jsonData))

	// 2. JSON → 结构体
	jsonStr := `{"name":"李四","age":30,"tags":["java","frontend"]}`
	var user2 User
	json.Unmarshal([]byte(jsonStr), &user2)
	fmt.Println("\n2. JSON转结构体:")
	fmt.Printf("%+v\n", user2)

	// 3. Map ↔ JSON
	data := map[string]interface{}{
		"id":   1,
		"name": "王五",
		"data": map[string]string{"key": "value"},
	}
	
	jsonData, _ = json.MarshalIndent(data, "", "  ")
	fmt.Println("\n3. Map转JSON:")
	fmt.Println(string(jsonData))

	// 4. 切片 ↔ JSON
	users := []User{
		{Name: "用户1", Age: 20},
		{Name: "用户2", Age: 30},
	}
	
	jsonData, _ = json.MarshalIndent(users, "", "  ")
	fmt.Println("\n4. 切片转JSON:")
	fmt.Println(string(jsonData))
}

1. 结构体转JSON:
{
  "name": "张三",
  "age": 25,
  "tags": [
    "go",
    "backend"
  ]
}

2. JSON转结构体:
{Name:李四 Age:30 Email: Tags:[java frontend] Password:}

3. Map转JSON:
{
  "data": {
    "key": "value"
  },
  "id": 1,
  "name": "王五"
}

4. 切片转JSON:
[
  {
    "name": "用户1",
    "age": 20,
    "tags": null
  },
  {
    "name": "用户2",
    "age": 30,
    "tags": null
  }
]
```

## 单元测试

### 1. 测试文件命名

- 测试文件：`*_test.go`
- 测试函数：`TestXxx(t *testing.T)`
- 基准测试：`BenchmarkXxx(b *testing.B)`
- 示例函数：`ExampleXxx()`

### 2. 表格驱动测试（推荐）

```go
func TestXxx(t *testing.T) {
    tests := []struct{
        name string
        input string
        expected string
    }{
        {"case1", "input1", "expected1"},
        {"case2", "input2", "expected2"},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            // 测试逻辑
        })
    }
}
```

### 3. 常用断言模式

```go
// 相等断言
if got != want {
    t.Errorf("got %v, want %v", got, want)
}

// 错误断言
if err != nil {
    t.Fatalf("unexpected error: %v", err)
}

// 切片比较
if !reflect.DeepEqual(got, want) {
    t.Errorf("got %v, want %v", got, want)
}
```

### 4. 测试命令

| 命令                     | 用途           |
| :----------------------- | :------------- |
| `go test`                | 运行测试       |
| `go test -v`             | 详细输出       |
| `go test -run Pattern`   | 运行匹配的测试 |
| `go test -bench Pattern` | 运行基准测试   |
| `go test -cover`         | 测试覆盖率     |

### 5. 最佳实践

- 使用表格驱动测试覆盖多种情况
- 为每个测试用例命名（`t.Run`）
- 测试边界条件和错误情况
- 保持测试独立，不依赖执行顺序
- 并行测试时注意数据竞争

```go
// math.go

package main

// 简单数学函数
func Add(a, b int) int {
    return a + b
}

func Multiply(a, b int) int {
    return a * b
}

// 业务逻辑函数
func CalculateDiscount(price float64, isVIP bool) float64 {
    if price <= 0 {
        return 0
    }
    if isVIP {
        return price * 0.8 // VIP 8折
    }
    if price > 1000 {
        return price * 0.9 // 满1000打9折
    }
    return price
}

// 字符串处理
func ReverseString(s string) string {
    runes := []rune(s)
    for i, j := 0, len(runes)-1; i < j; i, j = i+1, j-1 {
        runes[i], runes[j] = runes[j], runes[i]
    }
    return string(runes)
}
```

```go
// math_test.go

package main

import (
    "testing"
)

// 1. 基础测试
func TestAdd(t *testing.T) {
    result := Add(2, 3)
    expected := 5
    if result != expected {
        t.Errorf("Add(2, 3) = %d; expected %d", result, expected)
    }
}

func TestMultiply(t *testing.T) {
    result := Multiply(4, 5)
    expected := 20
    if result != expected {
        t.Errorf("Multiply(4, 5) = %d; expected %d", result, expected)
    }
}

// 2. 表格驱动测试（推荐）
func TestAdd_TableDriven(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"正数相加", 2, 3, 5},
        {"零值相加", 0, 5, 5},
        {"负数相加", -2, -3, -5},
        {"正负相加", 5, -3, 2},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; expected %d", tt.a, tt.b, result, tt.expected)
            }
        })
    }
}

// 3. 边界测试
func TestCalculateDiscount(t *testing.T) {
    tests := []struct {
        name     string
        price    float64
        isVIP    bool
        expected float64
    }{
        {"普通用户不满1000", 500, false, 500},
        {"普通用户满1000", 1500, false, 1350},
        {"VIP用户小金额", 500, true, 400},
        {"VIP用户大金额", 1500, true, 1200},
        {"零价格", 0, false, 0},
        {"负价格", -100, false, 0},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := CalculateDiscount(tt.price, tt.isVIP)
            if result != tt.expected {
                t.Errorf("CalculateDiscount(%.2f, %t) = %.2f; expected %.2f", 
                    tt.price, tt.isVIP, result, tt.expected)
            }
        })
    }
}

// 4. 字符串测试
func TestReverseString(t *testing.T) {
    tests := []struct {
        input    string
        expected string
    }{
        {"hello", "olleh"},
        {"", ""},
        {"a", "a"},
        {"世界", "界世"},
        {"hello 世界", "界世 olleh"},
    }

    for _, tt := range tests {
        t.Run(tt.input, func(t *testing.T) {
            result := ReverseString(tt.input)
            if result != tt.expected {
                t.Errorf("ReverseString(%q) = %q; expected %q", tt.input, result, tt.expected)
            }
        })
    }
}

// 5. 并行测试
func TestMultiply_Parallel(t *testing.T) {
    tests := []struct {
        a, b, expected int
    }{
        {1, 1, 1},
        {2, 3, 6},
        {0, 5, 0},
        {-2, 3, -6},
    }

    for _, tt := range tests {
        tt := tt // 重要：创建局部变量
        t.Run("", func(t *testing.T) {
            t.Parallel() // 并行执行
            result := Multiply(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Multiply(%d, %d) = %d; expected %d", tt.a, tt.b, result, tt.expected)
            }
        })
    }
}

// 6. 基准测试
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(100, 200)
    }
}

func BenchmarkReverseString(b *testing.B) {
    for i := 0; i < b.N; i++ {
        ReverseString("hello world, this is a test string")
    }
}

// 7. 示例测试（会出现在文档中）
func ExampleAdd() {
    result := Add(3, 4)
    fmt.Println(result)
    // Output: 7
}

func ExampleReverseString() {
    result := ReverseString("hello")
    fmt.Println(result)
    // Output: olleh
}
```

```bash
# 运行所有测试
go test

# 运行测试并显示详细信息
go test -v

# 运行特定测试
go test -v -run TestAdd

# 运行包含"Discount"的测试
go test -v -run "Discount"

# 运行基准测试
go test -bench=.

# 运行基准测试并显示内存分配
go test -bench=. -benchmem

# 生成测试覆盖率报告
go test -cover
go test -coverprofile=coverage.out
go tool cover -html=coverage.out

=== RUN   TestAdd
--- PASS: TestAdd (0.00s)
=== RUN   TestMultiply
--- PASS: TestMultiply (0.00s)
=== RUN   TestAdd_TableDriven
=== RUN   TestAdd_TableDriven/正数相加
=== RUN   TestAdd_TableDriven/零值相加
=== RUN   TestAdd_TableDriven/负数相加
=== RUN   TestAdd_TableDriven/正负相加
--- PASS: TestAdd_TableDriven (0.00s)
=== RUN   TestCalculateDiscount
=== RUN   TestCalculateDiscount/普通用户不满1000
=== RUN   TestCalculateDiscount/普通用户满1000
=== RUN   TestCalculateDiscount/VIP用户小金额
=== RUN   TestCalculateDiscount/VIP用户大金额
=== RUN   TestCalculateDiscount/零价格
=== RUN   TestCalculateDiscount/负价格
--- PASS: TestCalculateDiscount (0.00s)
=== RUN   TestReverseString
=== RUN   TestReverseString/hello
=== RUN   TestReverseString/
=== RUN   TestReverseString/a
=== RUN   TestReverseString/世界
=== RUN   TestReverseString/hello_世界
--- PASS: TestReverseString (0.00s)
=== RUN   TestMultiply_Parallel
=== RUN   TestMultiply_Parallel/#00
=== RUN   TestMultiply_Parallel/#01
=== RUN   TestMultiply_Parallel/#02
=== RUN   TestMultiply_Parallel/#03
--- PASS: TestMultiply_Parallel (0.00s)
PASS
coverage: 100.0% of statements
ok      yourpackage   0.002s
```

## goroutine

### 基本介绍

**Go协程和Go主线程**

Go主线程(有程序员直接称为线程/也可以理解成进程):一个Go线程上，可以起多个协程，你可以这样理解，协程是轻量级的线程[编译器做优化

Go协程的特点

1) 有独立的栈空间

2) 共享程序堆空间

3) 调度由用户控制

4) 协程是轻量级的线程

### 快速入门

1) 主线程是一个物理线程，直接作用在cpu上的。是重量级的，非常耗费cpu资源。

2) 协程从主线程开启的，是轻量级的线程，是逻辑态。对资源消耗相对小。

3) Golang的协程机制是重要的特点，可以轻松的开启上万个协程。其它编程语言的并发机制是一般基于线程的，开启过多的线程，资源耗费大，这里就突显Golang在并发上的优势了

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

// 1. 基本Goroutine启动
func basicGoroutine() {
    fmt.Println("=== 1. 基本Goroutine ===")
    
    // 使用go关键字启动goroutine
    go func() {
        fmt.Println("这是在goroutine中执行")
    }()
    
    // 主goroutine等待一下，让上面的goroutine有机会执行
    time.Sleep(100 * time.Millisecond)
    fmt.Println("主goroutine结束")
}

// 2. 带参数的Goroutine
func goroutineWithParams() {
    fmt.Println("\n=== 2. 带参数的Goroutine ===")
    
    for i := 0; i < 3; i++ {
        // 注意：直接使用循环变量i会有问题（在Go 1.21+中已修复）
        go func(id int) {
            fmt.Printf("Goroutine %d 执行\n", id)
        }(i) // 通过参数传递
    }
    
    time.Sleep(200 * time.Millisecond)
}

// 3. 使用WaitGroup同步
func waitGroupExample() {
    fmt.Println("\n=== 3. WaitGroup同步 ===")
    
    var wg sync.WaitGroup
    results := make([]string, 0)
    var mu sync.Mutex // 保护共享数据
    
    for i := 0; i < 5; i++ {
        wg.Add(1) // 增加计数
        go func(id int) {
            defer wg.Done() // 完成时减少计数
            
            // 模拟工作
            time.Sleep(100 * time.Millisecond)
            result := fmt.Sprintf("结果-%d", id)
            
            // 保护共享数据访问
            mu.Lock()
            results = append(results, result)
            mu.Unlock()
            
            fmt.Printf("Worker %d 完成\n", id)
        }(i)
    }
    
    wg.Wait() // 等待所有goroutine完成
    fmt.Printf("所有任务完成，结果: %v\n", results)
}

// 4. Channel通信
func channelExample() {
    fmt.Println("\n=== 4. Channel通信 ===")
    
    // 无缓冲channel
    ch := make(chan string)
    
    // 生产者
    go func() {
        for i := 0; i < 3; i++ {
            msg := fmt.Sprintf("消息-%d", i)
            ch <- msg // 发送消息
            fmt.Printf("发送: %s\n", msg)
        }
        close(ch) // 关闭channel
    }()
    
    // 消费者
    go func() {
        for msg := range ch { // 自动检测channel关闭
            fmt.Printf("接收: %s\n", msg)
            time.Sleep(100 * time.Millisecond) // 模拟处理时间
        }
        fmt.Println("Channel已关闭")
    }()
    
    time.Sleep(500 * time.Millisecond)
}

// 5. 缓冲Channel
func bufferedChannelExample() {
    fmt.Println("\n=== 5. 缓冲Channel ===")
    
    // 缓冲大小为3的channel
    ch := make(chan int, 3)
    
    go func() {
        for i := 0; i < 5; i++ {
            ch <- i
            fmt.Printf("发送: %d (缓冲区长度: %d/%d)\n", i, len(ch), cap(ch))
        }
        close(ch)
    }()
    
    go func() {
        for value := range ch {
            fmt.Printf("接收: %d\n", value)
            time.Sleep(200 * time.Millisecond) // 慢消费者
        }
    }()
    
    time.Sleep(2 * time.Second)
}

// 6. Select多路复用
func selectExample() {
    fmt.Println("\n=== 6. Select多路复用 ===")
    
    ch1 := make(chan string)
    ch2 := make(chan string)
    
    // 两个生产者
    go func() {
        time.Sleep(100 * time.Millisecond)
        ch1 <- "来自ch1"
    }()
    
    go func() {
        time.Sleep(50 * time.Millisecond)
        ch2 <- "来自ch2"
    }()
    
    // 使用select等待多个channel
    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-ch1:
            fmt.Printf("收到: %s\n", msg1)
        case msg2 := <-ch2:
            fmt.Printf("收到: %s\n", msg2)
        case <-time.After(300 * time.Millisecond): // 超时
            fmt.Println("等待超时")
            return
        }
    }
}

// 7. Worker Pool模式
func workerPoolExample() {
    fmt.Println("\n=== 7. Worker Pool模式 ===")
    
    jobs := make(chan int, 10)
    results := make(chan int, 10)
    
    // 启动3个worker
    for w := 1; w <= 3; w++ {
        go worker(w, jobs, results)
    }
    
    // 发送工作
    for j := 1; j <= 5; j++ {
        jobs <- j
    }
    close(jobs)
    
    // 收集结果
    for a := 1; a <= 5; a++ {
        result := <-results
        fmt.Printf("收到结果: %d\n", result)
    }
}

func worker(id int, jobs <-chan int, results chan<- int) {
    for job := range jobs {
        fmt.Printf("Worker %d 开始工作 %d\n", id, job)
        time.Sleep(500 * time.Millisecond) // 模拟工作
        results <- job * 2
        fmt.Printf("Worker %d 完成工作 %d\n", id, job)
    }
}

// 8. 生产者和消费者模式
func producerConsumerExample() {
    fmt.Println("\n=== 8. 生产者和消费者模式 ===")
    
    jobs := make(chan int, 5)
    done := make(chan bool)
    
    // 生产者
    go func() {
        for i := 1; i <= 5; i++ {
            jobs <- i
            fmt.Printf("生产: %d\n", i)
            time.Sleep(100 * time.Millisecond)
        }
        close(jobs)
    }()
    
    // 消费者
    go func() {
        for job := range jobs {
            fmt.Printf("消费: %d\n", job)
            time.Sleep(200 * time.Millisecond)
        }
        done <- true
    }()
    
    <-done // 等待消费者完成
    fmt.Println("生产消费完成")
}

func main() {
    basicGoroutine()
    goroutineWithParams()
    waitGroupExample()
    channelExample()
    bufferedChannelExample()
    selectExample()
    workerPoolExample()
    producerConsumerExample()
}

=== 1. 基本Goroutine ===
这是在goroutine中执行
主goroutine结束

=== 2. 带参数的Goroutine ===
Goroutine 0 执行
Goroutine 1 执行
Goroutine 2 执行

=== 3. WaitGroup同步 ===
Worker 0 完成
Worker 2 完成
Worker 1 完成
Worker 3 完成
Worker 4 完成
所有任务完成，结果: [结果-0 结果-2 结果-1 结果-3 结果-4]

=== 4. Channel通信 ===
发送: 消息-0
接收: 消息-0
发送: 消息-1
接收: 消息-1
发送: 消息-2
接收: 消息-2
Channel已关闭

=== 5. 缓冲Channel ===
发送: 0 (缓冲区长度: 1/3)
发送: 1 (缓冲区长度: 2/3)
发送: 2 (缓冲区长度: 3/3)
接收: 0
发送: 3 (缓冲区长度: 3/3)
接收: 1
发送: 4 (缓冲区长度: 3/3)
接收: 2
接收: 3
接收: 4

=== 6. Select多路复用 ===
收到: 来自ch2
收到: 来自ch1

=== 7. Worker Pool模式 ===
Worker 1 开始工作 1
Worker 2 开始工作 2
Worker 3 开始工作 3
Worker 1 完成工作 1
Worker 1 开始工作 4
收到结果: 2
Worker 2 完成工作 2
Worker 2 开始工作 5
收到结果: 4
Worker 3 完成工作 3
收到结果: 6
Worker 1 完成工作 4
收到结果: 8
Worker 2 完成工作 5
收到结果: 10

=== 8. 生产者和消费者模式 ===
生产: 1
消费: 1
生产: 2
消费: 2
生产: 3
生产: 4
消费: 3
生产: 5
消费: 4
消费: 5
生产消费完成
```

## channel

### 1. Channel创建

```go
// 无缓冲Channel - 同步通信
ch1 := make(chan int)

// 缓冲Channel - 异步通信
ch2 := make(chan int, 10)     // 缓冲大小10

// 单向Channel
var sendOnly chan < - int       // 只能发送
var receiveOnly < -chan int    // 只能接收
```

### 2. 基本操作

```go
// 发送数据
ch < - value

// 接收数据
value := < -ch

// 关闭Channel
close(ch)

// 检查Channel是否关闭
value, ok := < -ch
if !ok {
    // Channel已关闭
}
```

### 3. Channel遍历

```go
// 自动检测关闭
for value := range ch {
    // 处理value
}

// 等价于
for {
    value, ok := <-ch
    if !ok {
        break
    }
    // 处理value
}
```

### 4. Select多路复用

```go
select {
case msg1 := <-ch1:
    // 处理ch1
case msg2 := <-ch2:
    // 处理ch2
case ch3 <- data:
    // 发送到ch3
case <-time.After(timeout):
    // 超时处理
default:
    // 非阻塞操作
}
```

### 5. 阻塞行为

- **无缓冲Channel**：发送和接收都会阻塞，直到另一端准备好
- **缓冲Channel**：发送在缓冲区满时阻塞，接收在缓冲区空时阻塞

### 6. Channel状态

| 操作               | 正常Channel | 已关闭Channel | nil Channel |
| :----------------- | :---------- | :------------ | :---------- |
| **发送** ch <- v   | 成功或阻塞  | panic         | 永久阻塞    |
| **接收** <-ch      | 成功或阻塞  | 收到零值      | 永久阻塞    |
| **关闭** close(ch) | 成功        | panic         | panic       |

### 7. 最佳实践

**正确关闭Channel：**

```go
// 只有发送者应该关闭Channel
func producer(ch chan<- int) {
    defer close(ch) // 确保关闭
    for i := 0; i < 10; i++ {
        ch <- i
    }
}
```

**避免Channel泄漏：**

```go
// 使用超时防止永久阻塞
select {
case result := <-ch:
    return result
case <-time.After(5 * time.Second):
    return errors.New("timeout")
}
```

**安全的并发访问：**

```
// Channel本身是并发安全的，无需额外同步
```

### 常用模式

### 1. 工作池模式

```go
func workerPool(jobs <-chan Job, results chan<- Result) {
    for job := range jobs {
        results <- process(job)
    }
}
```

### 2. 发布订阅模式

```go
type Publisher struct {
    subscribers []chan<- Message
}

func (p *Publisher) Subscribe() <-chan Message {
    ch := make(chan Message, 10)
    p.subscribers = append(p.subscribers, ch)
    return ch
}
```

### 3. 管道模式

```go
func pipeline(input <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range input {
            out <- n * 2
        }
        close(out)
    }()
    return out
}
```

**关键记忆点**：

- `make(chan Type)` - 无缓冲Channel
- `make(chan Type, size)` - 缓冲Channel
- `ch <- data` - 发送数据
- `data := <-ch` - 接收数据
- `close(ch)` - 关闭Channel
- `select` - 多路Channel操作

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    // 1. 创建Channel
    fmt.Println("=== 1. Channel创建 ===")
    var ch1 chan int          // 声明，此时ch1是nil
    ch2 := make(chan int)     // 无缓冲channel
    ch3 := make(chan int, 3)  // 缓冲大小为3的channel
    
    fmt.Printf("ch1: %v, ch2: %v, ch3: %v\n", ch1, ch2, ch3)
    
    // 2. 基本发送接收
    fmt.Println("\n=== 2. 基本发送接收 ===")
    go func() {
        ch2 <- 42 // 发送数据
        fmt.Println("数据已发送")
    }()
    
    value := <-ch2 // 接收数据
    fmt.Printf("接收到数据: %d\n", value)
    time.Sleep(100 * time.Millisecond)
    
    // 3. 缓冲Channel演示
    bufferedChannelDemo()
    
    // 4. Channel遍历和关闭
    channelRangeDemo()
    
    // 5. Select多路复用
    selectDemo()
    
    // 6. 单向Channel
    directionalChannelDemo()
    
    // 7. 实际应用模式
    practicalPatterns()
}

// 3. 缓冲Channel演示
func bufferedChannelDemo() {
    fmt.Println("\n=== 3. 缓冲Channel ===")
    ch := make(chan string, 2) // 缓冲大小为2
    
    // 不会阻塞，因为缓冲区有空位
    ch <- "第一条消息"
    ch <- "第二条消息"
    fmt.Printf("缓冲区状态: %d/%d\n", len(ch), cap(ch))
    
    // 如果再发送会阻塞，因为缓冲区已满
    // ch <- "第三条消息" // 这行会阻塞
    
    fmt.Println("接收:", <-ch)
    fmt.Println("接收:", <-ch)
    fmt.Printf("接收后缓冲区状态: %d/%d\n", len(ch), cap(ch))
}

// 4. Channel遍历和关闭
func channelRangeDemo() {
    fmt.Println("\n=== 4. Channel遍历和关闭 ===")
    ch := make(chan int, 3)
    
    // 生产者
    go func() {
        for i := 1; i <= 3; i++ {
            ch <- i * 10
            fmt.Printf("生产: %d\n", i*10)
        }
        close(ch) // 重要：关闭channel
        fmt.Println("Channel已关闭")
    }()
    
    // 消费者 - 使用range自动检测关闭
    go func() {
        for value := range ch {
            fmt.Printf("消费: %d\n", value)
            time.Sleep(100 * time.Millisecond)
        }
        fmt.Println("所有数据已消费")
    }()
    
    time.Sleep(500 * time.Millisecond)
}

// 5. Select多路复用
func selectDemo() {
    fmt.Println("\n=== 5. Select多路复用 ===")
    ch1 := make(chan string)
    ch2 := make(chan string)
    
    go func() {
        time.Sleep(50 * time.Millisecond)
        ch1 <- "来自ch1"
    }()
    
    go func() {
        time.Sleep(100 * time.Millisecond)
        ch2 <- "来自ch2"
    }()
    
    // 使用select监听多个channel
    for i := 0; i < 2; i++ {
        select {
        case msg := <-ch1:
            fmt.Printf("Case 1: %s\n", msg)
        case msg := <-ch2:
            fmt.Printf("Case 2: %s\n", msg)
        case <-time.After(200 * time.Millisecond):
            fmt.Println("超时!")
            return
        }
    }
}

// 6. 单向Channel
func directionalChannelDemo() {
    fmt.Println("\n=== 6. 单向Channel ===")
    
    ch := make(chan int)
    
    // 只发送channel
    go producer(ch)
    
    // 只接收channel  
    go consumer(ch)
    
    time.Sleep(300 * time.Millisecond)
}

// 只发送channel参数
func producer(ch chan<- int) {
    for i := 0; i < 3; i++ {
        ch <- i
        fmt.Printf("生产者发送: %d\n", i)
    }
    close(ch)
}

// 只接收channel参数
func consumer(ch <-chan int) {
    for num := range ch {
        fmt.Printf("消费者接收: %d\n", num)
    }
    fmt.Println("消费者完成")
}

// 7. 实际应用模式
func practicalPatterns() {
    fmt.Println("\n=== 7. 实际应用模式 ===")
    
    // 模式1: 完成信号
    done := make(chan bool)
    
    go func() {
        fmt.Println("正在执行任务...")
        time.Sleep(200 * time.Millisecond)
        fmt.Println("任务完成")
        done <- true
    }()
    
    <-done // 等待任务完成
    fmt.Println("收到完成信号")
    
    // 模式2: 超时控制
    timeoutDemo()
    
    // 模式3: 退出信号
    quitSignalDemo()
}

// 超时控制
func timeoutDemo() {
    fmt.Println("\n--- 超时控制 ---")
    ch := make(chan string)
    
    go func() {
        time.Sleep(500 * time.Millisecond) // 模拟耗时操作
        ch <- "操作结果"
    }()
    
    select {
    case result := <-ch:
        fmt.Printf("成功: %s\n", result)
    case <-time.After(300 * time.Millisecond): // 300ms超时
        fmt.Println("操作超时!")
    }
}

// 退出信号
func quitSignalDemo() {
    fmt.Println("\n--- 退出信号 ---")
    quit := make(chan bool)
    messages := make(chan string)
    
    // 工作goroutine
    go func() {
        for i := 0; ; i++ {
            select {
            case <-quit:
                fmt.Println("收到退出信号，清理资源...")
                return
            default:
                messages <- fmt.Sprintf("消息%d", i)
                time.Sleep(100 * time.Millisecond)
            }
        }
    }()
    
    // 主goroutine接收一些消息后发送退出信号
    go func() {
        for i := 0; i < 3; i++ {
            msg := <-messages
            fmt.Printf("主程序收到: %s\n", msg)
        }
        quit <- true // 发送退出信号
    }()
    
    time.Sleep(500 * time.Millisecond)
}

=== 1. Channel创建 ===
ch1: <nil>, ch2: 0xc00001a0c0, ch3: 0xc00001a120

=== 2. 基本发送接收 ===
数据已发送
接收到数据: 42

=== 3. 缓冲Channel ===
缓冲区状态: 2/2
接收: 第一条消息
接收: 第二条消息
接收后缓冲区状态: 0/2

=== 4. Channel遍历和关闭 ===
生产: 10
生产: 20
消费: 10
生产: 30
Channel已关闭
消费: 20
消费: 30
所有数据已消费

=== 5. Select多路复用 ===
Case 1: 来自ch1
Case 2: 来自ch2

=== 6. 单向Channel ===
生产者发送: 0
消费者接收: 0
生产者发送: 1
消费者接收: 1
生产者发送: 2
消费者接收: 2
消费者完成

=== 7. 实际应用模式 ===
正在执行任务...
任务完成
收到完成信号

--- 超时控制 ---
操作超时!

--- 退出信号 ---
主程序收到: 消息0
主程序收到: 消息1
主程序收到: 消息2
收到退出信号，清理资源...
```

## 反射

### 基本介绍

1) 反射可以在运行时动态获取变量的各种信息,比如变量的类型(type)，类别(kind)

2) 如果是结构体变量，还可以获取到结构体本身的信息(包括结构体的字段、方法)

3) 通过反射，可以修改变量的值，可以调用关联的方法。

4) 使用反射，需要import(“reflect”)

### 反射的注意事项和细节

1) reflect.Value.Kind，获取变量的类别，返回的是一个常量

2) Type和Kind的区别

Type是类型,Kind是类别，Type和Kind可能是相同的，也可能是不同的.

比如:var num int=10 num的Type是int,Kind也是int

比如:var stu Student stu的Type是pkg1.Student,Kind是struct

3) 通过反射的来修改变量,注意当使用SetXxx方法来设置需要通过对应的指针类型来完成,这样才能改变传入的变量的值,同时需要使用到reflect.Value.Elem()方法

```go
package main

import (
	"fmt"
	"reflect"
)

type User struct {
	Name   string `json:"name"`
	Age    int    `json:"age"`
	Email  string `json:"email,omitempty"`
	secret string // 未导出字段
}

func main() {
	// 1. 基本类型反射
	fmt.Println("=== 基本反射 ===")
	var num float64 = 3.14
	fmt.Println("类型:", reflect.TypeOf(num))
	fmt.Println("值:", reflect.ValueOf(num))
	
	// 2. 修改值（必须使用指针）
	v := reflect.ValueOf(&num).Elem()
	if v.CanSet() {
		v.SetFloat(6.28)
		fmt.Println("修改后的值:", num)
	}
	
	// 3. 结构体反射
	fmt.Println("\n=== 结构体反射 ===")
	user := User{Name: "Alice", Age: 25, secret: "hidden"}
	t := reflect.TypeOf(user)
	vUser := reflect.ValueOf(user)
	
	// 遍历结构体字段
	for i := 0; i < t.NumField(); i++ {
		field := t.Field(i)
		value := vUser.Field(i)
		
		fmt.Printf("字段: %s, 类型: %s", field.Name, field.Type)
		
		// 只处理可导出字段
		if value.CanInterface() {
			fmt.Printf(", 值: %v", value.Interface())
		} else {
			fmt.Printf(", 未导出字段")
		}
		
		// 读取tag
		if tag := field.Tag.Get("json"); tag != "" {
			fmt.Printf(", JSON标签: %s", tag)
		}
		fmt.Println()
	}
	
	// 4. 动态调用方法
	fmt.Println("\n=== 方法反射 ===")
	calc := Calculator{}
	vCalc := reflect.ValueOf(calc)
	
	// 调用Add方法
	method := vCalc.MethodByName("Add")
	if method.IsValid() {
		args := []reflect.Value{
			reflect.ValueOf(10),
			reflect.ValueOf(20),
		}
		result := method.Call(args)
		fmt.Printf("方法调用结果: %d\n", result[0].Int())
	}
	
	// 5. 切片反射
	fmt.Println("\n=== 切片反射 ===")
	slice := []string{"apple", "banana"}
	sliceValue := reflect.ValueOf(slice)
	fmt.Printf("切片长度: %d, 容量: %d\n", sliceValue.Len(), sliceValue.Cap())
	
	// 6. 动态创建实例
	fmt.Println("\n=== 动态创建 ===")
	newSlice := reflect.MakeSlice(reflect.TypeOf([]string{}), 2, 5)
	newSlice.Index(0).SetString("动态")
	newSlice.Index(1).SetString("创建")
	fmt.Println("动态创建的切片:", newSlice.Interface())
}

type Calculator struct{}

func (c Calculator) Add(a, b int) int {
	return a + b
}

func (c Calculator) Multiply(a, b int) int {
	return a * b
}


=== 基本反射 ===
类型: float64
值: 3.14
修改后的值: 6.28

=== 结构体反射 ===
字段: Name, 类型: string, 值: Alice, JSON标签: name
字段: Age, 类型: int, 值: 25, JSON标签: age
字段: Email, 类型: string, 值: , JSON标签: email,omitempty
字段: secret, 类型: string, 未导出字段

=== 方法反射 ===
方法调用结果: 30

=== 切片反射 ===
切片长度: 2, 容量: 2

=== 动态创建 ===
动态创建的切片: [动态 创建]
```

## 数据结构

### Stack

#### slice实现

```go
type Stack struct {
    elements []int
}

// 创建新栈
func NewStack() *Stack {
    return &Stack{elements: make([]int, 0)}
}

// 入栈
func (s *Stack) Push(val int) {
    s.elements = append(s.elements, val)
}

// 出栈
func (s *Stack) Pop() int {
    if s.IsEmpty() {
        return -1 // 或者根据题目需求返回特定值
    }
    lastIndex := len(s.elements) - 1
    val := s.elements[lastIndex]
    s.elements = s.elements[:lastIndex]
    return val
}

// 查看栈顶元素
func (s *Stack) Peek() int {
    if s.IsEmpty() {
        return -1
    }
    return s.elements[len(s.elements)-1]
}

// 判断栈是否为空
func (s *Stack) IsEmpty() bool {
    return len(s.elements) == 0
}

// 获取栈大小
func (s *Stack) Size() int {
    return len(s.elements)
}
```

#### 使用示例

```go
func TestStack() {
	stack := NewStack()
	fmt.Printf("栈是否为空: %t\n", stack.IsEmpty())
	stack.Push(5)
	stack.Push(3)
	stack.Push(4)
	fmt.Printf("弹出栈顶元素是: %d\n", stack.Pop())
	fmt.Printf("查看栈顶元素是: %d\n", stack.Peek())
	fmt.Printf("栈大小是: %d\n", stack.Size())
	fmt.Printf("栈是否为空: %t\n", stack.IsEmpty())
}

栈是否为空: true
弹出栈顶元素是: 4
查看栈顶元素是: 3
栈大小是: 2
栈是否为空: false
```

#### 不封装

```go
package main

import (
    "container/list"
    "fmt"
)

func main() {
    // 初始化一个空的整型队列 q
    q := list.New()

    // 在队尾添加元素
    q.PushBack(10)
    q.PushBack(20)
    q.PushBack(30)

    // 检查队列是否为空，输出：false
    fmt.Println(q.Len() == 0)

    // 获取队列的大小，输出：3
    fmt.Println(q.Len())

    // 获取队列的队头元素
    // 输出：10
    front := q.Front().Value.(int)
    fmt.Println(front)

    // 删除队头元素
    q.Remove(q.Front())

    // 输出新的队头元素：20
    newFront := q.Front().Value.(int)
    fmt.Println(newFront)
}
```



### Queue

#### slice

```go
type Queue struct {
    elements []int
}

func NewQueue() *Queue {
    return &Queue{elements: make([]int, 0)}
}

// 入队
func (q *Queue) Enqueue(val int) {
    q.elements = append(q.elements, val)
}

// 出队
func (q *Queue) Dequeue() int {
    if q.IsEmpty() {
        return -1
    }
    val := q.elements[0]
    q.elements = q.elements[1:]
    return val
}

// 查看队首元素
func (q *Queue) Front() int {
    if q.IsEmpty() {
        return -1
    }
    return q.elements[0]
}

func (q *Queue) IsEmpty() bool {
    return len(q.elements) == 0
}

func (q *Queue) Size() int {
    return len(q.elements)
}
```

#### 使用示例

```go

func TestQueue() {
	q := NewQueue()
	fmt.Printf("队列是否为空: %t\n", q.IsEmpty())
	q.Enqueue(2)
	q.Enqueue(3)
	q.Enqueue(4)
	fmt.Printf("队列是否为空: %t\n", q.IsEmpty())
	fmt.Printf("查看队头元素: %d\n", q.Front())
	fmt.Printf("队列中元素大小是: %d\n", q.Size())
	fmt.Printf("出队元素是: %d\n", q.Dequeue())

}

队列是否为空: true
队列是否为空: false
查看队头元素: 2
队列中元素大小是: 3
出队元素是: 2
```

#### 双链表

```go
package main

import (
    "container/list"
    "fmt"
)

func main() {
    // 初始化一个空的整型队列 q
    q := list.New()

    // 在队尾添加元素
    q.PushBack(10)
    q.PushBack(20)
    q.PushBack(30)

    // 检查队列是否为空，输出：false
    fmt.Println(q.Len() == 0)

    // 获取队列的大小，输出：3
    fmt.Println(q.Len())

    // 获取队列的队头元素
    // 输出：10
    front := q.Front().Value.(int)
    fmt.Println(front)

    // 删除队头元素
    q.Remove(q.Front())

    // 输出新的队头元素：20
    newFront := q.Front().Value.(int)
    fmt.Println(newFront)
}
```

### Priority Queue

#### container/heap

```go
package main

// 使用包装结构体，语义更清晰
type IntHeap struct {
	elements []int
}

// 全部使用指针接收器
func (h *IntHeap) Len() int {
	return len(h.elements)
}

func (h *IntHeap) Less(i, j int) bool {
	return h.elements[i] < h.elements[j]
}

func (h *IntHeap) Swap(i, j int) {
	h.elements[i], h.elements[j] = h.elements[j], h.elements[i]
}

func (h *IntHeap) Push(x interface{}) {
	h.elements = append(h.elements, x.(int))
}

func (h *IntHeap) Pop() interface{} {
	n := len(h.elements)
	x := h.elements[n-1]
	h.elements = h.elements[:n-1]
	return x
}

func (h *IntHeap) Peek() interface{} {
	if h.Len() == 0 {
		return -1
	}
	return h.elements[0]
}
```

#### 使用示例

```go
func TestPriorityQueue() {
	h := &IntHeap{elements: []int{2, 1, 5}}
	heap.Init(h)

	heap.Push(h, 3)
	fmt.Printf("最小值: %d\n", h.Peek())

	for h.Len() > 0 {
		fmt.Printf("出队列: %d ", heap.Pop(h))
	}
}

最小值: 1
出队列: 1 出队列: 2 出队列: 3 出队列: 5 
进程 已完成，退出代码为 0
```

### 双链表

```go
package main

import (
    "container/list"
    "fmt"
)

func main() {
    // 初始化链表
    lst := list.New()
    lst.PushBack(1)
    lst.PushBack(2)
    lst.PushBack(3)
    lst.PushBack(4)
    lst.PushBack(5)

    // 检查链表是否为空，输出：false
    fmt.Println(lst.Len() == 0)

    // 获取链表的大小，输出：5
    fmt.Println(lst.Len())

    // 在链表头部插入元素 0
    lst.PushFront(0)
    // 在链表尾部插入元素 6
    lst.PushBack(6)

    // 获取链表头部和尾部元素，输出：0 6
    front := lst.Front().Value.(int)
    back := lst.Back().Value.(int)
    fmt.Println(front, back)

    // 删除链表头部元素
    lst.Remove(lst.Front())
    // 删除链表尾部元素
    lst.Remove(lst.Back())

    // 在链表中插入元素
    // 移动到第三个位置
    third := lst.Front().Next().Next()
    lst.InsertBefore(99, third)

    // 删除链表中某个元素
    second := lst.Front().Next()
    lst.Remove(second)

    // 遍历链表
    // 输出：1 99 3 4 5
    for e := lst.Front(); e != nil; e = e.Next() {
        fmt.Print(e.Value.(int), " ")
    }
    fmt.Println()
}


false
5
0 6
1 99 3 4 5 
```

### set

Golang 没有内置的哈希集合类型，但可以使用哈希表 `map` 来模拟集合，键为元素，值为 `struct{}` 或 `bool`。

一般会推荐使用 `map[int]struct{}` 来模拟哈希集合，因为 `struct{}` 不会占用额外的内存空间，而 `bool` 类型会占用一个字节的内存空间。

```go
package main

import (
	"fmt"
)

func main() {
	// 初始化一个包含一些元素的哈希集合 hashset
	hashset := map[int]struct{}{
		1: {},
		2: {},
		3: {},
		4: {},
	}

	// 检查哈希集合是否为空，输出：false
	fmt.Println(len(hashset) == 0)

	// 获取哈希集合的大小，输出：4
	fmt.Println(len(hashset))

	// 查找指定元素是否存在
	// 输出：Element 3 found.
	if _, exists := hashset[3]; exists {
		fmt.Println("Element 3 found.")
	} else {
		fmt.Println("Element 3 not found.")
	}

	// 插入一个新的元素
	hashset[5] = struct{}{}

	// 删除一个元素
	delete(hashset, 2)
	// 输出：Element 2 not found.
	if _, exists := hashset[2]; exists {
		fmt.Println("Element 2 found.")
	} else {
		fmt.Println("Element 2 not found.")
	}

	// 遍历哈希集合
	// 输出（顺序可能不同）：
	// 1
	// 3
	// 4
	// 5
	for element := range hashset {
		fmt.Println(element)
	}
}
```

