## shell 学习第三十二天----read 读取一行

### 标准输入输出与标准错误输出

标准输入/输出可能是软件工具设计原则里最基本的观念了。他的构想是：程序应有一个数据来源，数据出口（数据要去哪里），以及报告问题的地方。他们分别叫做标准输入，标准输出和标准错误输出。程序应该不知道也不在意其输入与输出背后是另一个执行的程序！程序可以预期，在他启动的时候，这些标准位置都已打开，且已经准备好可以使用了。
 
默认情况下，程序会读取标准输入，写入标准输出，并将错误信息传递给标准错误输出。这样的程序我们称为过滤器，因为他们过滤数据流，每一个都会在数据流上执行某种运算，再通过管道，将它传递给下一个。
 
**使用 read 读取行**

`read` 命令是用于从终端或者文件中读取输入的内部命令，`read` 命令读取整行输入，每行末尾的换行符不被读入。在 `read` 后面，如果没有指定变量名，读取的数据将被自动赋值给特定的变量 `REPLY`。

语法：

`read [-r] variable`

用途：将信息读入一个或多个 shell 变量

主要选项：

- `-r`：原始读取，不作任何处理。不将行结尾处的反斜杠解释为续行字符。

行为模式：

自标准输入读取行 (数据) 后，通过 shell 字段切割的功能 (使用 `$IFS`) 进行切分。第一个单词赋值给第一个变量，第二个单词则赋值给第二个变量，以次类推。如果单词多于变量，则所有剩下的单词，全赋值给最后一个变量。`read` 一旦遇到文件结尾，会以失败退出。

如果输入行以反斜杠结尾，则 `read` 会丢弃反斜杠与换行符，然后继续读取下一行数据。如果使用 `-r` 选项，那么 `read` 便会以字面意义读取最后的反斜杠。
 
警告：

当你将 `read` 应用在管道里时，许多 shell 会在一个分开的进程内执行它。在这种情况下，任何以 `read` 所设置的变量，都不会保留他们在父 shell 里的值。对管道中间的循环，也是这样。
 
**案例一**：

bash 代码：

```
\#!/bin/bash
read -p "input Numbers"
echo $REPLY
```

执行结果为：input Numbers $REPLY(你所输入的数字)
 
**案例二**：

```
\#!/bin/bash
two()
{
        read -p "input 2 numbers" v1 v2
        echo $(($v1+$v2))
}
two
```

执行：`./read1.sh` 

输出结果：

input 2 numbers  5 6  
11
 
**案例三**：

```
\#!/bin/bash
read -n 1 -p "Do you want to continue [Y/N] ? " answer
case $answer in
        Y|y)
        echo "continue"
        ;;
        N|n)
        echo "break"
        ;;
        *)
        echo "error"
        ;;
esac
exit 0
```

分析：该例子使用了 `-n` 选项，`-n` 选项的意思是说后面可以接受多少个字符的输入，这里指定了 1 表示接受一个字符就退出，也就是说只要按下一个键就会立即接受输入并将其传递给变量。无需按回车符。
 
**案例四**：

```
\#!/bin/bash
if read -t 5 -p "please enter your name：" name
then 
        echo "hello $name,welcome to my world"
else
        echo "sorry ,too slow"
fi
exit 0
```

分析：这里使用了 `-t` 选项，使用 `read` 命令会存在潜在的危险。脚本很可能会停下来一直等待用户的输入。如果无论是否输入数据脚本都必须继续执行，那么可以使用 `-t` 选项指定一个定时器。`-t` 选项指定 `read` 命令等待输入的秒数。当计数达到 `-t` 执行的时间时，`read` 命令返回一个非零退出状态。`-t` 选项后面指定的是秒数。

**案例五**：

```
\#!/bin/bashread  -s  -p "Enter your password：" passecho "your password is $pass"exit 0 
```

分析：`s` 选项能够使 `read` 命令中输入的数据不显示在监视器上（实际上，数据是显示的，只是 `read` 命令将文本颜色设置成与背景相同的颜色）。

**案例六**：

如何得到一个只有 IP 的字符串?

`/sbin/ifconfig eth0 | grep Bcast | sed -e 's/^.* addr：.∗ Bcast.*$/\1/'`
 
想要实现输入一个 IP 跟机器上的 IP 对照，观察是否存在。

```
\#!/bin/bash
ip=$(/sbin/ifconfig eth0 | grep Bcast | sed -e's/^.* addr：.∗ Bcast.*$/\1/')
read var
\#echo $ip
if ["$var" = "$ip"]
then
        echo "Ok"
else
        echo "no"
fi
```

分析：回顾一下 `sed` 命令，`sed` 命令是一种在线编辑器，一次处理一行内容。`sed` 命令的 `-e` 选项是说多点编辑，此处相当于：

`ifconfig eth0 |grep "inet" | sed 's/^.*addr：//g'| sed 's/Bcast.*$//g'`

sed 参考连接：  
[http：//blog.csdn.net/dawnstar_hoo/article/details/4043887](http：//blog.csdn.net/dawnstar_hoo/article/details/4043887)
  
关于特殊符号的参考：  
[http：//www.ahlinux.com/shell/9964.html](http：//www.ahlinux.com/shell/9964.html)