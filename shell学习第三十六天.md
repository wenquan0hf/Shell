## shell 学习三十六天----命令替换  

### 命令替换

命令替换是指 shell 可以先执行命令，将输出结果暂时保存，在适当的地方输出。

命令替换的语法：

`command`

注意这是反引号，而不是单引号，这个键位于 ESC 键的下方。

案例：

```
\#!/bin/bash
DATE=`date`
echo "Date is $DATE"
 \
USERS=`who | wc -l`
echo "Logged in user are $USERS"
UP=`date ；uptime`
echo "Uptime is $UP"
```

执行结果：

```
Date is 2015 年 07 月 04 日 星期六 10：54：22 CST
Logged in user are 1
Uptime is 2015 年 07 月 04 日 星期六 10：54：22 CST
10：54：22 up  1：22， 1 user， load average：0.00，0.00，0.00
``` 

**为 `head` 命令使用 sed**

使用 sed 的 `head` 命令来显示文件的前 n 行。真实的 `head` 命令可加上选项，以指定要显示多少行。例如 `head -n 10 /etc/passwd`。

使用命令替换与 sed，使其与原始的 head 版本的工作方式相同：

```
\#!/bin/bash
count=$(echo $1 | sed's/^-//')     #截去前置的负号
shift                         #移除 $1
sed ${count}q "$@"
```

当调用这个脚本时，使用命令`./head.sh -10 /etc/passwd` 调用这个脚本时，sed 最终是以 `sed 10q /etc/passwd` 被引用。
 
 
案例：发一封邮件信息给当前已登录的所有用户：

```
mail $(who | cut -d'    '-f1)
```

使用 `who` 命令获得当前在线用户，使用 `cut` 获得用户的名称，把括号内命令生成的结果先执行，然后作为 mail 参数。

**回顾一下 cut 命令**：

语法：`cut` 选项 参数

主要是用来从一个文本文件或者文本流中提取文本列。
 
主要选项：

- `-b`，`-c`，`-f` 分别表示字节，字符，字段 (即 byte，character，field)
- `-d`：使用指定分界符代替制表符作为区域分界
- `-f`：只选中指定的这些域；并打印所有不包含分界符的行，除非 -s 选项被指定。
- `-s`：不打印没有分界符的行
- `--output-delimiter= 字符串`    // 使用指定的字符串作为输出分界符，默认采用输入的分界符。
 
**简易教学：`expr`**

`expr` 命令是一个手工命令行计数器，用于在 UNIX/LINUX 下求表达式变量的值，一般用于整数值，也可用于字符串。

- 格式为：`expr Expression`(命令读入 Expression 参数，计算它的值，然后将结果写入到标准输出)
- 参数应用规则：
	- 用空格隔开每个项；
	- 用 / (反斜杠) 放在 shell 特定的字符前面；
	- 对包含空格和其他特殊字符的字符串要用引号括起来

expr 主要用于 shell 的算术运算。expr 的语法很麻烦：运算数与运算符必须是单个的命令行参数；因此建议在这里大量使用空格间隔它们。很多 expr 的运算符同时也是 shell 的 meta 字符，所以必须谨慎使用引号。

expr 被设置用在命令替换之内。这样，它会通过打印的方式把值返回到标准输出，而非通过使用退出码 (也就是 shell 内的 `$?`)。

**expr 运算符 (优先级由小至大)**

```
表达式
意义
e1 | e2
如果 e1 是非零值或非 null，则使用它的值。否则如果 e2 是非零值或非 null，则使用它的值。如果两者都不是，则最后值为零
e1&e2
如果 e1 与 e2 都非零值或非 null，则返回 e1 的值。否则，最后值为零
e1=e2
等于
e1!=e2
不等于
e1<e2
小于
e1<=e2
小于或等于
e1>e2
大于
e1>=e2
大于或等于
```

以上这些运算符，如果指示的比较为真，则会使得 expr 显示 1，否则显示 0。如果两个运算符都为整数，则以数字方式比较；如果不是，则以字符串方式比较

```
e1+e2
e1 与 e2 的加和
e1-e2
e1 与 e2 的相减
e1*e2
e1 与 e2 的相乘
e1/e2
e1 除以 e2 的整数结果 (取整)
e1%e2
e1 除以 e2 的整数结果 (取余)
e1：e2
e1 与 e2 的 BRE 匹配
```

- `integer` 
一个只包含数字的数目，允许前置负号，但却不支持一元的正号
- `string`
字符串值，不允许被误用为数字或运算符

在新的代码里，可以使用 `test` 或 `$((...))` 进行这里所有运算。正则表达式的匹配与提取，也可搭配 sed 或是 shell 的 case 语句来完成。
 
案例一：计算字符串长度  
`expr length "this is a test"`  
输出：14

案例二：抓取字串  
`expr substr "this is a test"`  
输出：is is  

案例三：抓取第一个字符数字串出现的位置  
`expr index "qweasdzxcasdqwe" a`  
输出：4  

案例四：整数运算  
`expr 14 % 9`(空格隔开)  
输出：5  
其他运算符相同  

案例五：增量计算  

```
test=0  
test=`expr $LOOP + 1`(这里的是反引号，ESC 下面的那个键)  
```

案例六：数值测试  
说明：用 `expr` 测试一个数。如果试图计算非整数，则会返回错误。 
 
```
rr=3.4
expr $rr + 1
expr：non-numeric argument
rr=5
expr $rr + 1
6
```

案例七： 
 
```
$ a=2
$ b=3
$ c=`expr $a + $b`//` 是 Tab 上面的那个按键，意思在这行里面
两个 `` 之间的命令最先执行
$ echo $c
你还可以用这种方面来计算：
$ a=2
$ b=3
$ c=$(($a+$b))
$ echo $c
解释一下：$((里面能进行运算))
```

更详细的参考：

[http：//blog.csdn.net/guhong5153/article/details/6542995](http：//blog.csdn.net/guhong5153/article/details/6542995)

案例：

```
\#!/bin/bash
i=1
while ["$i" -le 5]
do
        echo i is $i
        i=`expr $i + 1`
done
echo $i
```

输出：

```
i is 1
i is 2
i is 3
i is 4
i is 5
6
```

这类的算术运算，已经给出了可能遇到的 expr 的使用方式 99%。故意在这里使用 test(别名用法为 [...]) 以及反引号的命令替换，因为这是 expr 的传统用法。

在新的代码里，使用 shell 的内建算术替换应该会更好：

```
\#!/bin/bash
i=1
while ["$i" -le 5]
do
        echo i is $i
        i=$(($i+1))
done
echo $i
```

无论 expr 的价值如何，它支持 32 位的算术运算，也支持 64 位的算术运算----在很多系统上都可以，因此，几乎不会有计数器溢出的问题。