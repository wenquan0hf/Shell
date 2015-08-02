## shell 学习第三十一天----函数问题

### 函数

**案例一**：

```
\#!/bin/bash
hello () {
        echo "hahahah"
}
hello
```

执行函数，结果为：hahaha
 
 
**案例二**：

```
\#!/bin/bash
funWithReturn()
{
        echo "the function is to get the sum of two number"
        read -p "input first number" num1
        read -p "input second number" num2
        echo "the two number are $num1 and $num2 !"
        return $(($num1+$num2))
}
\
funWithReturn
ret=$?
echo "sum = $ret"
```

执行函数，得到两个数的和。
 
**案例三**：

```
\#!/bin/bash
number_one() {
        echo "number_one"
        number_two
}
\
number_two()
{
        echo "number_two"
}
number_one
```

执行命令：`[root@localhost tmp]# sh function2.sh` 

输出结果：

number_one  
number_two  
 
分析：定义 shell 函数的语法为

```
[funciton] funname [()]
{
action
[return int]
}
```

说明：

1. 可以带 function fun() 定义，也可以直接 fun() 定义，不带任何参数 (关键字 function 可以省略)。
2. 函数返回，可以显示的加：return 返回，如果不加，将以最后一条命令的运行结果作为返回值。
 
Shell 函数类似于 Shell 脚本，里面存放了一系列的指令，不过 Shell 的函数存在于内存，而不是硬盘文件，所以速度很快，另外，Shell 还能对函数进行预处理，所以函数的启动比脚本更快。
 
语句部分可以是任意的 Shell 命令，也可以调用其他的函数。如果在函数中使用 exit 命令，可以退出整个脚本，通常情况，函数结束之后会返回调用函数的部分继续执行。可以使用 break 语句来中断函数的执行。

- `declare –f` 可以显示定义的函数清单
- `declare –F` 可以只显示定义的函数名
- `unset –f` 可以从 Shell 内存中删除函数
- `export –f` 将函数输出给 Shell

另外，函数的定义可以放到`.bash_profile` 文件中，也可以放到使用函数的脚本中，还可以直接放到命令行中，还可以使用内部的 `unset 命令`删除函数。一旦用户注销，Shell 将不再保持这些函数。
 
补充一下，就是：

- `$0`：是脚本本身的名字；
- `$#`：是传给脚本的参数个数；
- `$@`：是传给脚本的所有参数的列表，即被扩展为 `"$1"` `"$2"` `"$3"` 等；
- `$*`：是以一个单字符串显示所有向脚本传递的参数，与位置变量不同，参数可超过 9 个，即被扩展成 `"$1c$2c$3"`，其中 `c` 是 `IFS` 的第一个字符；
- `$$`：是脚本运行的当前进程 ID 号；
- `$?`：是显示最后命令的退出状态，0 表示没有错误，其他表示有错误；
 
举例说：

脚本名称叫 `test.sh` 入参三个: 1 2 3

运行 `test.sh 1 2 3` 后

- `$*` 为 "1 2 3"（一起被引号包住）
- `$@`为 "1" "2" "3"（分别被包住）
- `$#`为 3（参数数量）
 
其中 exit 是用来结束一个程序的执行的，而 return 只是用来从一个函数中返回。exit(0) 表示正常退出执行程序，如果加其它的数值：1，2，.... 可以表示由于不同的错误原因而退出。那么，1，2，3 怎么对应不同的原因？   －－你自己想让它是什么意思，它就是什么意思。但一般都有常用的、通用的含义：比如   0   一般都表示正常返回、退出。因此，在 main 函数中 `exit(0)` 等价于 `return 0`。
 
**全局变量**

这种类似与 C 语言中的全局变量 (或环境变量)

案例：

```
\#!/bin/bash
g_var=
function mytest()
{
        echo "mytest"
        echo "args $1"
        g_var=$1
        return 0
}
mytest 1
echo "return $?"
\ 
echo
echo "g_var=$g_var"
```

分析: 主要是 `echo "return $?"` 输出时 0 不好理解，这个是说上一行的 mytest 这个函数执行成功了，所以值为 0，但是在这个函数的内部给全局变量重新赋值。所以结果为：

mytest  
args 1  
return 0  
g_var=1  
 
函数没有提供局部变量。因此所有的函数都与父脚本共享变量；即，你必须小心留意不要修改父脚本里不期望改变的东西，例如：path。不过这也表示其他状态是共享的，例如当前木与捕捉信号。