## shell 学习第三十天----break,continue,shift,getopts

### break 和 continue  

这两个命令分别用来退出循环，或跳到循环体的其他地方。使用 while 与 break，等待用户登录。

bash 代码:

```
printf “Enter username:”
read user
while true
do
if who | grep “$user” >/dev/null
then 
break;
fi
sleep 30
done
```

等待特定用户，每 30 秒确认一次
 
true 命令什么事也不必做，只是成功的退出。这用于编写无限循环，即会永久的执行循环。在编写无限循环时，必须放置一个退出条件在循环体内，正如这里所作的。另有一个 false 命令和它有点相似，只是很少的用到，也不做人和事，仅表示不成功的状态.false 命令常见于无线的 until false..循环中。
 
continue 命令则用于提早的开始下一段重复动作，也就是在到大循环体的底部之前。  

break 与 continue 命令都可以接受可选的数值参数，可分别用来之处要中断 (break) 或继续多少个被包含的循环 (如果循环技术需要的是一个在运行时可被计算的表达式时，可以使用 $((...)) )。

**案例**：

```
while condition1 // 外循环
do...
while condition2 // 内循环
do..
break； 外循环的终端
done
done
```

break 与 continue 特别具备终端或继续多个循环层的能力。从而以简洁的形式弥补了 shell 语言里缺乏 goto 关键字的不足。

**使用 continue 的案例**：

```
\#!/bin/bash
limit=19
echo "printing Number 1 throught 20"
a=0
while [$a -le"$limit"]
do
        let a++
        #let a+=1
        #a=$(($a+1))
        if ["$a" -eq 3] || ["$a" -eq 11]
        then
                continue
        fi
        echo -n "$a"
done
```

输出结果：

printing Number 1 throught 20 
 
1 2 4 5 6 7 8 9 10 12 13 14 15 16 17 18 19 20  

由此可见 continue 的作用是结束本次循环，执行下一次循环
 
 
**使用 break 的案例**：

```
\#!/bin/bash
limit=19
echo "printing Number 1 throught 20"
a=0
while [$a -le"$limit"]
do
        let a++
        #let a+=1
        #a=$(($a+1))
        if ["$a" -eq 3] || ["$a" -eq 11]
        then
                break
        fi
        echo -n "$a"
done
```

输出结果：

printing Number 1 throught 20

1 2

由此可见，break 的作用是退出当前循环。
 
**shift**

我们知道，对于位置变量或命令行参数，其个数必须是确定的，或者当 Shell 程序不知道其个数时，可以把所有参数一起赋值给变量 `$*`。若用户要求 Shell 在不知道位置变量个数的情况下，还能逐个的把参数一一处理，也就是在 `$1` 后为 `$2`，在 `$2` 后面为 `$`3 等。在 shift 命令执行前变量 `$1` 的值在 shift 命令执行后就不可用了。

**案例**：

```
\#!/bin/bash
until [$# -eq 0]
do
        echo " 第一个参数为：$1 参数个数为：$#"
        shift
done
```

执行命令：`./shift.sh 1 2 3 4`

输出为：

第一个参数为：1 参数个数为：4  
第一个参数为：2 参数个数为：3  
第一个参数为：3 参数个数为：2  
第一个参数为：4 参数个数为：1  

分析：

- 从上可知 shift 命令每执行一次，变量的个数 ($#) 减一，而变量值提前一位。
- shift 可以用来向左移动位置参数。
- Shell 的名字 $0

第一个参数 $1  
第二个参数 $2  
第 n 个参数 $n  
所有参数 $@ 或 $*  
参数个数 $#  

**案例**：

bash 代码：

```
until [-z"$1"]  # Until all parameters used up
do
  echo "$@"
  shift
done
```

命令：`./shift1.sh 1 2 3 4 5 6 7 8 9 10`

输出：

1 2 3 4 5 6 7 8 9 10   
2 3 4 5 6 7 8 9 10   
3 4 5 6 7 8 9 10   
4 5 6 7 8 9 10   
5 6 7 8 9 10   
6 7 8 9 10   
7 8 9 10   
8 9 10   
9 10   
10  
 
**getopts 命令**

语法：

`getopts option_spec variable [arguments...]`

现在来看一个简单的例子：

```
\#!/bin/bash
echo $*
while getopts ":a:bc:" opt
do
        case $opt in
                a)
                echo $OPTARG
                echo $OPTIND
                ;;
                b)
                echo "b $OPTIND"
                ;;
                c)
                echo "c $OPTIND"
                ;;
                ?)
                echo "error"
                exit 1
        esac
done
echo $OPTIND
shift $(($OPTIND-1))
echo $0
echo $*
```
 
如果执行命令：`./getopts.sh -a 11 -b -c 6`  

结果为：

-a 11 -b -c 6  
11  
3  
b 4  
c 6  
6  
./getopts.sh  
 
看分析：

`getopts` 后面的字符串就是可以使用的选项列表，每个字母代表一个选项，后面带：的意味着选项除了定义本身之外，还会带上一个参数作为选项的值，比如 a：在实际的使用中就会对应 `-a 11`，选项的值就是 11；`getopts` 字符串中没有跟随：的是开关型选项，不需要再指定值，相当于 true/false，只要带了这个参数就是 true。如果命令行中包含了没有在 `getopts` 列表中的选项，会有警告信息，如果在整个 getopts 字符串前面也加上个:，就能消除警告信息了。使用 `getopts` 识别出各个选项之后，就可以配合 case 来进行相应的操作了。`optarg` 这个变变，`getopts` 修改了这个变量。这里变量 `$optarg` 存储相应选项的参数，而 `$optind` 总是存储原始 `$*` 中下一个要处理的元素 (不是参数，而是选项，此处值得的是 a，b，c 这三个选线，而不是那些数字，当然数字也是会占有位置的) 位置。

`while getopts ":a:bc:" opt`  # 第一个冒号表示忽略错误；字符后面的冒号表示该选项必须有自己的参数。
 
使用 `getopts` 处理参数虽然是方便，但仍然有两个小小的局限：

1. 选项参数的格式必须是 `-d val`，而不能是中间没有空格的 `-dval`。
2. 所有选项参数必须写在其它参数的前面，因为 `getopts` 是从命令行前面开始处理，遇到非 `-` 开头的参数，或者选项参数结束标记 `--` 就中止了，如果中间遇到非选项的命令行参数，后面的选项参数就都取不到了。
3. 不支持长选项，也就是 --`debug` 之类的选项
  
**案例**：

```
\#!/bin/bash
while getopts "ab:cd:" opt
do
        case $opt in
                a)
                echo $OPTIND
                ;;
                b)
                echo $OPTIND
                echo $OPTARG
                ;;
                c)
                echo $OPTIND
                ;;
                d)
                echo $OPTIND
                echo $OPTARG
        esac
done
shift $(($OPTIND-1))
```
 
使用命令：`./getopts1.sh -a -b foo -c -d haha`

得到结果：

2  
4  
foo  
5  
7  
haha  

最后使用 `shift $(($OPTIND-1))` 的作用是：通过 `shift $(($OPTIND - 1))` 的处理，`$*` 中就只保留了除去选项内容的参数，可以在其后进行正常的 shell 编程处理了。 貌似不用也可以。

如果出现这种情况：

- `getopts “:ab:c”` 第一个冒号代表的含义是：第一个冒号表示忽略错误，即当出现没有的选项是会忽略；字符后面的冒号表示该选项必须有自己的参数。