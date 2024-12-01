# Cmake

## Windows

### 开发环境搭建

1、安装MinGw和Cmake

2、在vscode中安装插件c++、cmake、cmake tools插件

### VSCode调试

单文件调试

```c++
// 不带参数
g++ .\main.cpp // 编译main.cpp文件，未带参数，默认在当前文件夹下生成a.exe程序
.\a.exe   // 执行a.exe 文件
      
// 带参数
g++ -g .\main.cpp -o my_single_swap  // -g表示生成带有调试信息的可执行文件，-o 表示指定文件名
.\my_single_swap.exe  // 执行my_single_swap.exe 文件
```

多文件调试

```c++
g++ -g .\main.cpp .\swap.cpp -o my_multi_swap  // 多文件编译

//不会生成默认文件,需要手动该配置
// 1、在launch.json文件中，修改program文件内容，指定需要调试的文件名
"program": "${workspaceFolder}\\my_multi_swap.exe",
// 2、注释
"preLaunchTask": "C/C++: g++.exe 生成活动文件"
```

### Cmake

```cmake
创建文件CMakeLists.txt 

project(MYSWAP) #工程名字
add_definitions(-Wall -g) # 可调试选项
add_executable(my_cmake_swap main.cpp swap.cpp) #需要编译的文件 生成的文件名

1、配置vscode ctrl+shift+p 选择Cmake：configure 选择gcc编译器
2、切换到build文件夹，检查配置是否正确
cmake ..
-- Configuring done
-- Generating done
-- Build files have been written to: D:/vscode/cmake/build
3、输入指令mingw32-make.exe 编译生成可执行文件 ，文件位于build文件夹下  文件名my_cmake_swap.exe
[ 33%] Building CXX object CMakeFiles/my_cmake_swap.dir/main.cpp.obj
[ 66%] Building CXX object CMakeFiles/my_cmake_swap.dir/swap.cpp.obj
[100%] Linking CXX executable my_cmake_swap.exe
[100%] Built target my_cmake_swap
4、调试需要修改launch  "program": "${workspaceFolder}\\build\\my_cmake_swap.exe", 文件路径
```

注意

```cmake
手动构建build文件夹
mkdir build 
cd build
# 如果电脑上已经安装了vs,可能会调用微软MSVC编译器，使用(cmake -G "MinGW Makefiles" ..)代替 cmake .. 即可
# 仅第一次使用cmake时使用(cmake -G "MinGW Makefiles" ..)，后面可用 cmake .. 
```

### 配置Json

#### launch.json

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "g++.exe - 生成和调试活动文件", # 运行时配置名称
            "type": "cppdbg",
            "request": "launch",
            
            "program": "${workspaceFolder}\\build\\my_cmake_swap.exe", #可执行文件路径
            "args": [], # 主函数参数
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}", # 进入当前目录
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb", # gdb调试方式
            "miDebuggerPath": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-win32-seh-rt_v6-rev0\\mingw64\\bin\\gdb.exe", # 调试器路径
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            // 链接到tasks.json文件中 ,tasks.json中会重新编译生成可执行文件
             "preLaunchTask": "C/C++: g++.exe 生成活动文件" 
            // 将这句注释情况，如果修改了代码，重新调试，仍然执行未修改之前的代码
        }
    ]
}
```

"preLaunchTask": "C/C++: g++.exe 生成活动文件"  

```c++
int main(int argc ,char **argv){
    int val1=10;
    int val2=20;

    cout<<"before swap:"<<endl;
    cout<<"val1="<<val1<<endl;
    cout<<"val2="<<val2<<endl;
    
  breakpointg: swap(val1,val2);

    // 注释如下代码 ，重新调试
    //cout<<"after swap:"<<endl;
    //cout<<"val1="<<val1<<endl;
    //cout<<"val2="<<val2<<endl;

    return 0;
}
输出情况：
before swap:
val1=10
val2=20
after swap:
val1=20
val2=10
```

#### tasks.json(编译有关)（默认）

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe 生成活动文件",
            "command": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-win32-seh-rt_v6-rev0\\mingw64\\bin\\g++.exe",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

问题

```
感觉CMakeLists.txt文件中少加了一个可调试的编译选项：add_definitions(-Wall -g)
没有这个选项点调试是没有用的
```

#### tasks.json(Cmake)(对应Cmake的编译命令)

```json
{
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build" # 相当于 cd ..命令
    },
    "tasks": [
        {
            "type": "shell",
            "label":"cmake",
            "command":"cmake",
            "args": [
                ".."
            ]
            
        },
        {
            "label": "make",
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command":"mingw32-make.exe", // 执行 mingw32-make.exe 命令
            "args": [

            ]
        },
        {
            "label": "Build", // 需要修改launch中的preLaunchTask名称一致
            "dependsOn":[  // 以来上面两个lable中的命令，间接调用
                "cmake", 
                "make"
            ]
        }
    ]
}
```

## Ubuntu

### 开发环境搭建

```
sudo apt install gcc
sudo apt intsall g++
sudo apt install gdb
sudo apt install cmake
```

### GCC编译器

#### 编译过程

![](images\1.png)

```bash
相当于 g++ test.cpp  -o test
```

### g++参数

1、-g 编译带调试信息的可执行文件

```bash
g++ -g test.cpp
```

2、-O（大写的）

```
优化代码
```

![](images\2.png)

3、-l（小写）和 -L（大写）指定库文件 和指定库文件路径

![](images\3.png)

4、-I（大写i）指定头文件搜索目录

![](images\4.png)

5、-Wall 打印警告信息

```bash
g++ -Wall test.cpp 
```

6、-w 关闭警告信息

```c++
g++ -w test.cpp
```

7、 -std=c++11 设置编译标准

```bash
g++ -std=c++11 test.cpp
```

8、 -o (小写)指定输出文件名

```bash
g++ test.cpp -o test
```

9、 -D 定义宏

![](images\5.png)

### GDB

```
GDB是一个调试C/C++程序的功能强大的调试器，是liunx系统开发C/C++最常用的调试器，vscode是通过GDB调试
```

主要功能

1. 设置断点(断点可以是条件表达式)
2. 使程序在指定的代码上暂停执行，便于观察
3. 单步执行程序，便于调试
4. 查看程序中变量值的变化
5. 动态改变程序执行环境
6. 分析奔溃程序产生的core文件

#### 调试参数

![](images\6.png)

```c++
$(gdb)quit(q) #退出gdb
```

![image-20210518192811898](images\7.png)

#### 命令行调试

```c++
#include<iostream>
using namespace std;

int main(int argc,char **argv){
	
	int N=100;
	int sum=0;
	int i=1;
	while(i<=N){
	  sum=sum+i;
	  i=i+1;
	}
	
	cout<<"sum = "<<sum<<endl;
	cout<<"The program is over."<<endl;
	return 0;
}
```

```bash
gdb调试必须在编译时加入 -g参数生成调试信息的可执行文件

gdb sum_with_g(进入gdb程序中)

run
sum = 5050
The program is over.
[Inferior 1 (process 3511) exited normally]

break(b) 10 在第10行打断点
Breakpoint 1 at 0x555555554936: file sum.cpp, line 10.

info(i) breakpoints 显示断点信息
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x0000555555554936 in main(int, char**)
                                                   at sum.cpp:10
print(p) i 打印变量i的信息
$1 =1

continue 
Continuing.
Breakpoint 1, main (argc=1, argv=0x7fffffffe388) at sum.cpp:10
10                sum=sum+i;

display i 显示变量i的信息
1: i = 2
```

### cmake

```
跨平台编译工具，可以用简单的语句来描述所以有平台的安装(编译过程)
cmake成为大部分c++开源项目的标配
```

![image-20210519095954348](images\8.png)

#### 语法

基本语法格式：指令(参数1、参数2、…. )

- 参数使用==括弧==括起 
- 参数之间使用==空格==或==分号==分开

指令大小写无关，参数和变量是大小写相关的

```cmake
set(HELLO hello.cpp)
add_executable(hello main.cpp hello.cpp)
ADD_EXECUTABLE(hello main.cpp ${HELLO}) 
```

变量使用${}方式取值，但是在IF控制语句中是直接使用变量名(if (HELLO))

#### 重要的指令和CMake常用变量

**指令**

- ==cmake_minimum_required== - 指定CMake的最小版本

  ```cmake
  cmake_minimum_required(VERSION versionNumber [FATAL_ERROR])
  # 最小版本要求是2.8.3
  cmake_minimum_required(VERSION 2.8.3)
  ```

- ==project== -定义工程名称，并可指定工程支持语言

   

  ```cmake
  project (projectname [CXX][C][Java])
  # 指定工程名为HELLOWORLD
  project(HELLOWORLD)
  ```

- ==set== - 显式的定义变量

  ```cmake
  set (VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])
  # 定义SRC变量，其值为main.cpp hello.cpp
  set(SRC main.cpp hello.cpp)
  ```

- ==include_directories== - 向工程添加多个特定的头文件搜索路径 —》相当于指定g++ 编译器的 -I 参数

  ```cmake
  include_directories([AFTER|BEFORE][SYSTEM] dir1 dir2 ...)
  # 将/usr/include/myincludefolder 和 ./include 添加到头文件搜索路径
  include_directories(/usr/include/myincludefolder ./include)
  ```

- ==link_directories== -向工程添加多个特定的库文件搜索路径—-》相当于g++编译器的-L参数

  ```cmake
  link_directories(dir1 dir2 ...)
  # 将/usr/lib/mylibfolder 和 ./lib 添加到库文件搜索路径
  link_directories(/usr/lib/mylibfolder ./lib)
  ```

- ==add_library== -生成库文件

  ```cmake
  add_library(libname [SHARED | STATIC| MODULE] [EXCLUDE_FROM_ALL] source1 source2 ... sourceN)
  # 通过变量SRC 生成libhello.so 共享库
  add_library(hello SHARED $(SRC))
  ```

- ==add_compile_options== -添加编译参数

  ```cmake
  add_compile_options(<option>...)
  # 添加编译参数 -Wall -std=c++11
  add_compile_options(-Wall -std=c++11 -O2)
  ```

- ==add_executable== - 生成可执行文件

  ```cmake
  add_executable(exename source1 source2 ... sourceN)
  # 编译main.cpp 生成可执行文件main
  add_executable(main main.cpp)
  ```

- ==target_link_libraries== -为target添加需要链接的共享库 —-》相当于指定g++ 编译器 -I 参数

  ```
  target_link_libraries(target library1<debug | optimized> library2...)
  # 将hello动态库文件链接到可执行文件main
  target_link_libraries(main hello)
  ```

- ==add_subdirectory== -向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标魏晋至存放的位置

  ```cmake
  add_subdirectory(source_dir[binary_dir][EXCLUDE_FROM_ALL])
  #添加src子目录，src中需有一个CMakelists.txt
  add_subdirectory(src)
  ```

- ==aux_source_directory== -发现一个目录下所有的源代码并将列表存储在一个变量中，这个指令临时被用来自动构建源文件列表

- ```cmake
  aux_source_directory(dir VARIABLE)
  # 定义SRC变量，其值为当前目录下所有源代码文件
  aux_source_directory(. SRC)
  # 编译SRC变量所代表的源文件，生 成main可执行文件
  add_executable (main ${SRC})
  ```

**常用变量**

![image-20210519112954596](images\9.png)

![image-20210519113038696](images\10.png)

### cmake编译工程

目录结构：项目主目录存在一个CMakeLists.txt文件

编译规则：

1. 包含源文件的子文件夹包含CMakeLists.txt文件，主目录的CMakeLists.txt通过add_subdirectory添加子目录即可
2. 包含源文件子目录未包含CMakeLists.txt文件，子目录编译规则体现在主目录CMakeLists.txt中

![image-20210519113653861](images\11.png)

![image-20210519113837209](images\12.png)

