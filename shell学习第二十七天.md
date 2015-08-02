## shell 学习第二十七天----退出状态和 if 语句

**退出状态**

每一条命令；不管是内置的，shell 函数，还是外部的，当它退出时，都会返回一个小的整数值给引用它的程序，这就是大家所熟知的程序的退出状态。在 shell 下执行进程是，有很多方式可取用程序的退出状态。

以管理来说，退出状态为 0 表示 “成功”，也就是，程序执行完成且为遭遇到任何问题。其他任何的退出退出状态都为失败。内置变量?(使用命令 `echo $?`) 查看上一条命令的退出状态。

案例：当你输入 `ls -l /dev/null` 时。  
输出：crw-rw-rw- 1 root root 1,3 6 月  25 15:41 /dev/null  
接着使用命令：`echo $?`  
输出为 0  
接着使用命令：`ls foo` 
输出：ls：无法访问 foo：没有那个文件或目录  
`echo $?`  
输出：2  
表示没有成功的执行。 
 
**POSIX 的结束状态**  

```
值
意义
0
命令成功地退出
\>0
在重定向或单词展开期间 (~，变量，命令，算数展开，以及单词切割) 失败
1-125
命令不成功的退出。特定的退出值的含义，是由各个单独的命令定义的。
126
命令找到了，但文件无法执行
127
命令没找到
\>128
命令因受到信号而死亡
```

POSIX 留下退出状态 128 未定义，仅要求他表示某种失败。因为只有低位的 8 个位会返回给父进程，所以大于 255 的退出状态都会替换成该值除以 256 之后的余数。

在 shell 脚本可以使用 exit 命令传递一个退出之给踏的调用者。只要将一个数字传递给它，作为一个参数即可。脚本会立即退出，并且调用者会受到该数字且作为脚本的退出值。

说白了 exit 就是退出当前的 shell，在 shell 脚本中可以终止当前脚本执行。

**exit**

语法：  
`exit [exit-value]`  
用途：
目的是从 shell 脚本返回一个退出状态给脚本的调用者。  
主要选项:  
无  
行为模式： 
如果没有提供，则以最后一个执行命令的退出状态作为默认的退出状态。如果这就是你要的，则最好明白的在 shell 脚本里这么写：exit $?  

案例一：exit  
输出为 logout，表示退出当前 shell  

案例二：脚本代码 `cd $(dirname $0) || exit 1`  
进入脚本所在目录，否则退出  

案例三：脚本中判断参数数量，不匹配就打印使用方式，退出  
代码:

```  
if ["$#" -ne "2"]; then  
          echo "usage：$0 <area> <hours>"  
              exit 2  
          fi  
```

案例四：在脚本里，退出时删除临时文件  
代码:`trap “入门 -rf tempfile;echo Bye.” exit`  

案例五：检查上一行的退出码
代码:

```
EXCODE=$?
 if ["$EXCODE" == "0"]; then
         echo "O.K"
      fi
```

**if-elif-else-fi 语句**  
if 语法：  

1. 单分支的 if 语句
if 条件测试命令
then
命令序列
fi
2. 双分支的 if 语句
if 条件测试命令
then
命令序列 1
else
命令序列 2
fi
3. 多分支的 if 语句 (elif 可以嵌套多个，一般多了用 case 表达)
if 条件测试命令 1
then
命令序列 1
elif 条件测试命令 2
then
命令序列 2
.........
else
命令序列 n
fi

```
if pipeline
[pipeline...]
then
statement-if-true-1
elif pipeline
[pipeline...]
then 
statement-iftrue2
else
statement-if-all-else-fails
if
```

使用方括号作为开始与结束的关键字将语句组织起来。
 
案例 1：提示用户指定备份目录的路径，若目录存在则显示信息跳过，否则显示相应提示信息，并创建该目录。
bash 代码:

```
\#!/bin/bash
read -p "what is your backup directoy :" BakDir
if [-d $BakDir];then
        echo "$BakDir alerdy exist"
else
        echo "$BakDir is not exist,will make it"
        mkdir $BakDir
fi
```
 
案例 2：统计当前登录到系统中的用户数量，若判断是否超过三个，若是则显示实际数量并给出警告信息，否则列出登录的用户账户名称及所在终端

bash 代码:

```
UserNum='who | wc -l'
if [$UserNum -gt 3];
        then
        echo "Alert, too many login users (Total：$UserNum)."
else
        echo "Login Users:"
        who | awk '{print $1,$2}'
fi
```

注意：

1. if 与 [ 之间必须有空格
2. [ ] 与判断条件之间也必须有空格
3. ] 与; 之间不能有空格
 
**逻辑的 not，and 与 or**

“如果 john 不在家，则...” ，在 shell 下这种情况的做法是: 将惊叹号放在管道前：

```
if ! grep pattern myfile > /dev/null
then
模式不在这里
fi
```

“如果 john 在家，且他不忙，则....”，使用逻辑 and。

```
if grep pattern1 myfile && grep pattern2 myfile
then 
myfile 包含两种模式
fi
```
 
相对的，|| 运算符则用来测试两个条件中是否有一个为真。:

```
if grep pattern1 myfile || grep pattern2 myfile
then 
myfile 包含两种模式之一
fi
```

逻辑 and 和 or 都是快捷运算符，即当判断出整个语句块的真伪时，shell 会立即停止执行命令。举例来说，在 `command1&&command2` 下，如果 `aommand1` 失败，则整个结果不可能为真，所以 command2 也不会被执行；以此类推，`command1||command2` 指的是: 如果 `command1` 成功，那么也没有理由执行 `command2`。

不要尝试过度” 简练” 未使用 && 和 || 来取代 if 语句。我们不反对简短且简单的事情，例如:

命令：`who | grep root > /dev/null && echo root is login on               root is login on`    
输出：`root is login on`  
分析：上面的命令实际做法是: 执行 `who | grep...` 且如果成功，就显示信息。而我们曾见过厂商提供 shell 脚本，所使用的是这样的结构：

```
some_command &&{
one command
a decond command
and a third command
}
```

这个命令的意思是说将所有的语句块放在一块，只有在 `some_command` 成功时他们才被执行。使用 if 可以让他更简洁：

```
if some_command
then
one command
a second command
and a third command
fi
```

最后在判断语句中常用的运算符：

1、字符串判断

```
str1 = str2　　　　　　当两个串有相同内容、长度时为真
str1 != str2　　　　　 当串 str1 和 str2 不等时为真
-n str1　　　　　　　 当串的长度大于 0 时为真 (串非空)
-z str1　　　　　　　 当串的长度为 0 时为真 (空串)
str1   当串 str1 为非空时为真
```
 
2、数字的判断

```
int1 -eq int2　　　　两数相等为真
int1 -ne int2　　　　两数不等为真
int1 -gt int2int1 大于 int2 为真
int1 -ge int2int1 大于等于 int2 为真
int1 -lt int2int1 小于 int2 为真
int1 -le int2int1 小于等于 int2 为真
```

3、文件的判断

```
-r file　　　　　用户可读为真
-w file　　　　　用户可写为真
-x file　　　　　用户可执行为真
-f file　　　　　文件为正规文件为真
-d file　　　　　文件为目录为真
-c file　　　　　文件为字符特殊文件为真
-b file　　　　　文件为块特殊文件为真
-s file　　　　　文件大小非 0 时为真
-t file　　　　　当文件描述符 (默认为 1) 指定的设备为终端时为真
```

4、复杂逻辑判断

```
-a 　 　　　　　 与
-o　　　　　　　 或
!　　　　　　　　非
```
 
**test 命令**

test 命令可以处理 shell 脚本里的各类工作。它产生的不是一般输出，而是可使用的退出状态。test 接受各种不同的参数，可控制它要执行哪一种测试。
 
test 命令有另一种形式:[...]，这种永福的作用完全与 test 命令一样。因此，下面这两个案例表达的意思相同

```
if test "$str1"="$str2"
then
...
fi
和
if ["$str1" = "$str2" ]
then
...
fi
一样
```

test 的语法： 

`test [expression]  [[expression] ]`

用途：

为了测试 shell 脚本里的条件，通过退出状态返回其结果。要特别注意的是：这个命令的第二种形式，方括号根据字面意义逐字的输入，且必须与括起来的 expression 以空白隔开。

主要选项：
和使用用于 if 的选项一致。
其中

``` 
选项
含义
string 
如果... 则为真
-b file
file 是块设备文件
-d file
file 是目录
-c file
file 是字符设备文件
-e file
file 存在
-f file
file 为一般文件
-g file
file 有设置他的 setgid 位
-h file
file 是一符号链接
-L file
file 是一符号链接 (等同于 -h)
-n string
string 是非 null
-p file
file 是一命名的管道 (FIGO 文件)
-r file
file 是可读的
-S file
file 是 socket
-s file
file 不是空的
-t n
文件描述符 n 指向一终端
-u file
file 有设置它的 setuid 位
-w file
file 是可写入的
-x file
file 是可执行的，或 file 是可被查找的目录
-z string
string 为 null
s1=s2 或者 s1!=s2
字符串相不相等
n1 -eq n2
整数 n1 等于 n2
n1 -ne n2
整数 n1 不等于 n2
n1 -lt n2 
n1 小于 n2
n1 -gt n2
n1 大于 n2
n1 -le n2
n1 小于或等于 n2
n1 -ge n2
n1 大于或等于 n2
```
 
案例：

bash 代码：

```
\#!/bin/bash
cd /bin
if test -e ./bash   // 其实这里相当于 if [-e ./bahs]
then
        echo 'the file already exist!'
else
        echo 'the file not exist!'
fi
```

输出结果为:the file already exist!
 
另外，shell 还提供了 `-a`(逻辑 AND)，`-o`(逻辑 OR)，`-a` 的优先级高于 `-o`，而 = 与!= 优先级则高于其他的二元运算符。

注意：在使用 `-a` 和 `-o`(这两个事 test 运算符) 与 `&&` 和 `||`(这两个事 shell 运算符) 之间有一个差异:

- `if [-n "$str" -a -f "$file" ]` 一个 test 命令，两种条件
- `if [-n "str"] && [-f "$file" ]` 两个命令，一块接方式计算
- `if [-n "$str" && -f "$file"]` 语法错误

第一个案例，test 会计算两种条件。而第二个案例，shell 执行第一个 test 命令，且只有在第一个命令是成功的情况下，才会执行第二个命令。最后一个案例，`&&` 为 shell 运算符，所以它会终止第一个 test 命令，然后这个命令会抱怨它找不到结束的] 字符，且以失败的值退出。即使 test 可以成功的退出，接下来的检查还会失败，因为 shell(最有可能) 找不到一个名为 `-f` 的命令
 
精简表达式:  
使用命令:`[1 eq1] &&echo'OK'`  
输出:ok  

使用命令:`[2 < 1] &&echo 'OK'`  
输出:-bash：1：No such file or directory 
 
使用命令:`[2 \< 1] &&echo 'OK'`这样就可以了  
使用命令:`[2 -gt 1 -a 3 -lt 4]&&echo 'Ok' ` 
输出:Ok  

使用命令:`[2 -gt 1 && 3 -lt 4]&&echo 'Ok'`  
输出:-bash：[：missing `]'  

**注意**：在 `[]` 表达式中，常见的 `>`，`<` 需要加转义字符，表示字符串大小比较，以 acill 码位置作为比较。不直接支持 `<>` 运算符，还有逻辑运算符 `||` 和 `&&` 它需要用 `-a[and] –o[or]` 表示。  
刚才使用的 `[]`，现在再来看使用 `[[]]`  

案例：

使用命令:`[[2 < 3]]&&echo 'OK'`  
输出 OK。 
 
使用命令:`[[2 < 3 && 4 < 5]] && echo 'ok' `   
输出:ok  

注意：`[[]]` 运算符只是 `[]` 运算符的扩充。能够支持 `<`，`>` 符号运算不需要转义符，它还是以字符串比较大小。里面支持逻辑运算符 `||` 和 `&&`。 bash 的条件表达式中有三个几乎等效的符号和命令：`test`，`[]` 和 `[[]]`。通常，大家习惯用 `if []`；`then` 这样的形式。而 `[[]]` 的出现，根据 ABS 所说，是为了兼容 `><` 之类的运算符。  
不考虑对低版本 bash 和对 sh 的兼容的情况下，用 `[[]]` 是兼容性强，而且性能比较快，在做条件运算时候，可以使用该运算符。  