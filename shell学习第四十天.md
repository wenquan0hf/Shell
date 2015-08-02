##  shell 学习四十天----awk 的惊人表现

### awk 的惊人表现

awk 可以胜任几乎所有的文本处理工作。
 
**awk调用**

调用 awk：

- 方式一：命令行方式 `awk [-F field-separator] 'commands' input-file(s)`
[ `-F` 域分隔符 ] 是可选的，因为 awk 使用空格作为缺省的域分隔符，因此如果要浏览域间有空格的文本，不必指定这个选项，如果要浏览例如 passwd 文件。此文件名域以冒号作为分隔符，则必须指明 `-F` 选项，如：`awk -F 'commands' input-file`
 
- 方式二：将所有 `awk` 命令插入一个文件，并使阿瓦库程序可执行，然后用 `awk` 命令解释器作为脚本的首行，以便通过键入脚本名成功来调用它。
 
- 方式三：将所有的 `awk` 命令插入一个单纯文件，然后调用：`awk -f awk-script-file input-file(s)`
	- `-f` 选项指明在文件 `awk_script_file` 中的 awk 脚本，`input_file(s)` 是使用 `awk` 进行浏览的文件名。
 
 
**模式和动作**
 
任何 `awk` 语句都是有模式和动作组成。在一个 `awk` 脚本中可能有很多语句，模式部分决定动作语句何时触发以及出发时间。处理即对数据进行的操作。如果省略模式部分，动作将时刻保持执行状态。模式可以是任何条件语句或符合语句或正则表达式。模式包括两个特殊字段 `BEGIN` 和 `END`。使用 `BEGIN` 语句设置计数和打印头。`BEGIN` 语句使用在任何文本浏览动作之前，之后文本浏览动作一句输入文本开始执行。`END` 语句用来在 `awk` 完成浏览动作后打印输出文本总数和结尾状态标识。
 
**域和记录**

使用 `$1`，`$3` 表示参照第一个和第三个域，注意这里使用逗号做域分割，如果希望打印一个有 5 个域的记录的所有域，可使用 `$0`，意即所有域。为打印一个域或所有域，使用 `printf` 命令，这是一个 `awk` 动作。
 
**模式和动作**

- 模式：两个特殊段 `BEGIN` 和 `END`
- 动作：实际动作大多在 {} 内指明
 
输出：

1. 抽取域
命令：`awk -F：'{print $1}' /etc/passwd`
输出：打印 /etc/passwd 目录下的所有用户名
 
2. 保存输出
`awk -F：'{print $1}' /etc/passwd |tee user`  使用 `tee` 命令，在输出文件的同时，输出到屏幕
 
3. 使用标准输出
`awk -F ：'{print $1} /etc/passwd > user3`
　
4. 打印所有记录
`awk -F ：'{print $0}` /etc/passwd`

5. 打印单独记录
`awk -F：'{print $1，$4}' /etc/passwd`
 
6. 打印报告头
`awk -F ：'BEGIN{print "NAME\n"}{print $1}' /etc/passwd`
 
7. 打印结尾
`awk -F：'{print $1}END{print "this is all users\n"}' /etc/passwd`
 
**条件操作符**

1. 匹配  
`awk -F ：'{if($1~/root/) print}' /etc/passwd`   
分析：`if($1~/roo/t)` 表示如果 file 中包含 root，打印他 
2. 精确匹配使用符号 ==    
`awk -F：'{if($3==0) print}' /etc/passwd`
3. 不匹配!~  
`awk -F：'{if($1!~/linuxone/) print}' /etc/passwd`
精确不匹配!=  
`awk -F：'{if($1!=/linuxone/) print}' /etc/passwd`
4. 小于<
5. 小于等于<=
6. 大于>
7. 设置大小写  
awk '/[Rr]oot' /etc/passwd
8. 任意字符  
`awk -F ：'{f($1~/^...t/) print}' /etc/passwd`
分析：if($1~/^...t/) 表示第四个字母是 t  
9. 或关系匹配awk -F  
`'{if($1~/(squid|nagios)/) print}' /etc/passwd`
10. 行首  
`awk '/^root/' /etc/passwd`
分析：^root(行首包含 root)  
11. AND &&  
`awk -F ：'{if($1=="root"&&$3=="0") print}' /etc/passwd`
12. OR ||
 
**内置变量**：

```
变量名
含义
ARCC
命令行参数个数
ARGV
命令行参数列表
ENV |RON
支持队列中的系统环境变量的使用
FNR
浏览文件的记录数
FS
置顶分隔符，等价于 -F
NF
浏览记录的域的个数
NR
一度的记录数
OFS
输出域分隔符
ORS
输出记录分隔符
RS
控制记录分隔符
```
 
案例：

打印有多少行记录  
`awk 'END{print NR}' /etc/passwd`
 
设置输入域到变量名  
`awk -F ：'{name=$1；path=$7；if(name~/root/)print name"\tpath is ：" path}' /etc/passwd`
 
域值比较操作  
`awk '{if($6<$7) print $0}' input-file`
 
修改文本域只显示修改的记录  
`awk -F ：'{if($1=="root"){$1="nagios server"；print}}' /etc/passwd`  
 
文件长度相加  
`ls -l | awk '/^[^d]/ {print $9"\t"$5}{tot+=$5}\
 END {print"total kb："tot}'`
  
**内置的字符串函数**

```
gsub(r，s)
在整个 $0 中 s 替换 r
gsub(r，s，t)
在整个 t 中 s 替换 r
index(s，t)
返回 s 中字符串 t 的第一位置
length(s)
返回 s 长度
match(s，r)
测试 s 中是否包含匹配 r 的字符串
split(s，a，fs)
在 fs 上将 s 分成序列 a
sub(s，)
用 $0 中最左边也是最长的字符串替代
subtr(s，p)
返回字符串 s 中从 p 开始的后缀部分
substr(s，p，n)
返回字符串 s 中从 p 开始长度为 n 的后缀部分
```
 
1. gsub  
`awk 'gsub(/^root/，"netseek") {print}' /etc/passwd  # `将以 root 开头的字符串替换为 netseek 并打印  
```awk 'gsub(/0/，2){print}' /etc/passwd
awk '{print gsub(/0/，2) $0}' /etc/fstab```
2. index  
```awk 'BEGIN{print index("root"，"o")}'  #查询 o 在 root 字符串中出现的第一位置
awk -F ：'$1=="root"{print index($1，"o")" "$1}' /etc/passwd
awk -F ：'{print index($1，"o") $1}' /etc/passwd```
3. length  
```awk -F ：'{print length($1)}' /etc/passwd
wk -F ：'$1=="root"{print length($1)"\t"$0}' /etc/passwd```
4. match(在 ANCD 中查找 C 的位置)  
`awk 'BEGIN{print match("ANCD"，"C")}'`
5. split 返回字符串数组元素个数  
`awk 'BEGIN{print split("123#456#789"，array，"#")}'`
6. sub 只能替换指定域的第一个 0  
`awk 'sub(/0/，2){print}' /etc/fstab`
7. substr 按照起始位置以及长度返回字符串的一部分  
```awk 'BEGIN{print substr("www.baidu.com"，5，9)}' #第五个子夫开始，取 9 个字符
awk 'BEGIN{print substr("www.baidu.com"，5)}'  #第五个位置开始，一直到最后```

**字符串屏蔽序列**

```
符号
含义
\b
退格符
\f
走纸换页
\n
新行
\r
回车
\t
tab 键 (四个空格)
\c
任意其他特殊字符
\ddd
八进制
```
 
案例：  
`awk -F ：'{print $1，"\b"$2，"\t"$3}' /etc/passwd`  
分析：print 和 printf 两者效果不同
 
printf 修饰符

- `-` ：左对齐
- `width` ：域的步长 0 表示 0 步长
- `.prec` ：最大字符串长度，或小数点右边的位数
 
**awk printf 格式**

```
符号
含义
%c
ASCII 字符
%d
整数
%e
科学计数法
%f
浮点数
%g
awk 决定使用哪种浮点数转换，e 或者 f
%o
八进制数
%s
字符串
%x
十六进制
```
 
1. 字符串转换  
```echo "65" | awk '{printf"%c\n"，$0}' 
awk 'BEGIN{printf"%c\n"，65}'
awk 'BEGIN{printf"%f\n"，999}'```
 
2. 格式化输出  
```awk -F ：'{printf"%-15s %s\n"，$1，$3}' /etc/passwd
awk -F ：'BEGIN{printf"USER\t\tUID\n"}{printf"%-15s %s\n"，$1，$3}' /etc/passwd```
 
3. 向一行 awk 命令传值  
```who | awk '{if ($1==user) print $1 "you are connected ：" $2}' user=$LOGNAME```
 
4. awk 脚本文件 (在文件名字后面加后缀.awk 翻遍区分)
```
\#!/bin/awk -f
BEGIN{
        FS="："
        print "User\t\tUID"
        print "----------------"
}
\
{printf"%-15s %s\n"，$1，$3}
\
END{
        print "end"
}
```

分析：`awk` 脚本文件开头一般都是这样的：`#!/bin/awk -f`
已经指明了 `-f` 选项。执行时，直接在 `awk` 脚本名后面加要处理的文件名作为参数即可。