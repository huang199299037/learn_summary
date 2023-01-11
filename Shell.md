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

