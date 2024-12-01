# Shell

## 字符串

```bash
不加引号
test_aaa="abc def"
test_abc=($test_aaa)
echo ${test_abc}
echo "${#test_abc[@]}"

abc
2

加引号(当成一个整体)
test_abc=("$(find ${pytest_root} -name "${py_name}")")
echo "${test_abc[@]}"
echo "${#test_abc[@]}"

abc def
1

解决：https://www.shellcheck.net/wiki/SC2206
# For bash
IFS=" " read -r -a array <<< "$var"

# For ksh
IFS=" " read -r -A array <<< "$var"

test_aaa="abc def"
IFS=" " read -r -a test_abc <<<"${test_aaa}"
# IFS=" " read -ra test_abc <<<"${test_aaa}"
默认空格
# read -ra test_abc <<<"${test_aaa}"
abc
2
```

## Shell 中的中括号用法总结

参考资料：https://www.runoob.com/w3cnote/shell-summary-brackets.html

- 算术比较, 比如一个变量是否为0, `[ $var -eq 0 ]`。
- 文件属性测试，比如一个文件是否存在，`[ -e $var ]`, 是否是目录，`[ -d $var ]`。
- 字符串比较, 比如两个字符串是否相同， `[[ $var1 = $var2 ]]`。

## Linux_Shell_ Map 的使用和遍历

参考资料 https://www.jianshu.com/p/10c2df1ece78

#### 定义初始化 map

```
declare -A map=(["100"]="1" ["200"]="2")
```

#### 输出所有 key

```
echo ${!map[*]}
```

#### 输出所有的值

```
echo ${map[@]}
```

#### 获取 map 的长度

```
echo ${#map[*]}
```

#### 输出 key 对应的值

```
echo  ${map["100"]}
```

#### 修改 key 对应的值

```
map["100"]="1"
```

#### 遍历 map

```
for key in  ${!map[*]}
do
    echo ${map[$key]}
done 
```

## 文件操作

### 改名

```
mv 旧名称  新名称
```

### 打包

```bash
打包文件
tar -cvf 打包文件.tar 被打包的文件／路径...
```

tar 选项说明

| 选项 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| c    | 生成档案文件，创建打包文件                                   |
| x    | 解开档案文件                                                 |
| v    | 列出归档解档的详细过程，显示进度                             |
| f    | 指定档案文件名称，f 后面一定是 .tar 文件，所以必须放选项最后 |

> 注意：f 选项必须放在最后，其他选项顺序可以随意

### 解压

```bash
解压缩到指定路径
tar -zxvf test.tgz -C 指定目录
解压缩文件
tar -zxvf 打包文件.tar.gz
```

### zip解压

```bash
安装unzip
sudo apt install unzip
解压文件
unzip file.zip
将文件解压到指定文件夹中，如果该文件夹不存在，将会被创建
unzip file.zip -d directory
```

### 统计文件

```shell
1、 统计当前文件夹下文件的个数

　　ls -l |grep "^-"|wc -l

2、 统计当前文件夹下目录的个数

　　ls -l |grep "^d"|wc -l

3、统计当前文件夹下文件的个数，包括子文件夹里的 

　　ls -lR|grep "^-"|wc -l

4、统计文件夹下目录的个数，包括子文件夹里的

　　ls -lR|grep "^d"|wc -l

grep "^-" 

　　这里将长列表输出信息过滤一部分，只保留一般文件，如果只保留目录就是 ^d

wc -l 

　　统计输出信息的行数，因为已经过滤得只剩一般文件了，所以统计结果就是一般文件信息的行数，又由于一行信息对应一个文件，所以也就是文件的个数。

判断目录下文件数与指定文件数量是否相等
```

### 复制

https://blog.csdn.net/u012593447/article/details/76575242/

现在假设服务器的ip为：10.12.13.14，域名是www.abc.com

1、从服务器分别复制文件和文件夹到本地：

```
scp root@10.12.13.14:/home/file /myMachine/x (可以将ip换成域名，也可以去掉root@)

scp -r www.abc.com:/home/file/ /myMachine/myFile/
```

2、本地复制到服务器

```
复制文件
scp /myMachine/x root@10.12.13.14:/home/file
复制文件夹
scp -r /myMachine/myFile/ www.abc.com:/home/file/ 
```

## wget

https://www.jianshu.com/p/9aed3bf36e32

wget的时候加上 -N（参数）：意思是只下载比本地新的文件

```
$ wget -N "http://xx"
-q quite no output
```

- 如果本地没有这个文件，会进行下载；

- 如果有这个文件，但是文件没有变化，不进行下载。

- 如果有这个文件，但是文件有变化，进行下载。

### 修改用户/修改组ID/目录属组

```
cat /etc/passwd | grep br104
br104:x:1000:1000:br104,,,:/home/br104:/bin/bash
is not in the sudoers file.  This incident will be reported.
wq! 权限保存
sudo su -
usermod -u 1000 admin
groupmod -g 1000 admin
chown -R br104:br104 /home/br104
```



