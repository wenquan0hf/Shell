## shell 学习四十一天----列出文件 ls 和 od 命令

### 列出文件

首先恶臭命令提供简单的方式列出匹配模式的文件：

命令：`echo /bin/*sh #显示 /bin 下的 shell`  
输出：/bin/bash /bin/csh /bin/dash /bin/sh /bin/tcsh  
分析：shell 将通配符字符模式替换为匹配的文件列表，echo 以空格区分文件列表，在单一行上显示他们。echp 不会更近一部解释他的参数，因此与文件系统里的文件也没有任何关系。
 
`ls` 命令则比 echo 能做更多的处理，因为他纸袋自己的参数应该是文件。未提供命令行选项时，`ls` 只会验证其参数是否存在，并显示它们，如果输出并非终端，则以一行一个的方式显示，如果是终端，则为多栏显示模式。下例：

命令：`ls /bin/*sh | cat`  
输出：  
/bin/bash  
/bin/csh  
/bin/dash  
/bin/sh  
/bin/tcsh  
分析：在输出管道里显示 shell
 
命令：`ls /bin/*sh`  
输出：  
/bin/bash  
/bin/csh  
/bin/dash  
/bin/sh  
/bin/tcsh  
分析：以 80 个字幅宽度的终端窗口，显示 shell
 
命令：`ls /bin/*sh`  
输出：    
/bin/bash    
/bin/csh  
/bin/dash  
/bin/sh  
/bin/tcsh    
分析：以 40 个字幅宽度的终端端口，显示 shell。
 
为了终端输出时，`ls` 会使用刚好适合的多栏，将数据以栏加以排列，这只是为了人们方便检查，如果你要单栏输出到终端，可以使用 `ls -1`(数字 1) 强制执行。另外，处理 `ls` 的管道输出的程序，可预期得到一个文件明一行的模式。
 
ls 的语法：  

`ls [options] [file(s)]`

用途：列出文件目录的内容

主要选项：

```
选项
作用
-1 
数字 1. 强制为单栏输出。在交互式模式下，ls 一般会以适用于当前窗口的最小宽度，使用多个列
-a
显示所有文件，包括隐藏文件 (文件名以点号其实的文件)
-d
显示于目录本身相关的信息，而非他们包含的文件的信息
-F
使用特殊结尾字符，标记特定的文件类型
-g
仅适用于组：省略所有者名称
-i
列出 inode 编号
-L
紧接着符号性连接，列出他们指向的文件
-l
小写的 l(字母)。以冗长形式列出，带有类型，全乡保护，所有者，组，字节计数，最后修改事件和文件名
-r
倒置默认的排序顺序
-R
递归列出，下延进入每个子目录
-S
按照由大到小的文件大小计数排序。仅 GNU 版本支持
-s
以块 (一系统有关) 为单位，列出文件的大小
-t
按照最后修改时间戳排序
--full-time
显示完整的时间戳。仅 GNu 版本支持
``` 
 
行为模式：`ls` 通常只显示文件名称：如要取得与文件属性相关的信息，必须提供额外选项。文件是以辞典编排的顺序排序，不过可通过 `-S` 或 `-t` 选项改变他。排序的顺序是按照系统的语言环境而定。
 
不同于 echo 的是：`ls` 要求他的文件参数要存在，而且如果他们不存在的话，则会出现提示：

命令：`ls dfrdsgjfgkjd`  
输出：ls：无法访问 dfrdsgjfgkjd：没有那个文件或目录  
然后使用 `echo $?` 查看一下 `ls` 的退出码：  
输出：2  
 
无参时，`echo` 只会显示一个空行，但 `ls` 会列出当前目录的内容。

案例：依次输入下列命令  

```
mkdir test
cd test
touch one two three
```

然后应用 `echo` 和 `ls`：  
命令：`echo *`    
输出：one three two  
命令：`ls *`  
输出：one three two  
命令：`echo`  
输出：空行  
命令：`ls`  
输出：one three two  
 
以一个点号为开头的文件名，在正规 shell 模式匹配中会被隐藏。

依次执行下列命令：

```
mkdir hidden
cd hidden
toucho .uno .dos .tres
```

接着使用下列命令尝试显示他的内容：

```
$echo
*
$ls
不显示任何东西
$ls *
ls：无法访问 *：没有那个文件或目录
```
 
当没有匹配模式的文件时，shell 会将模式视为参数：在这里 `echo` 看到星号并打印他，而 `ls` 则试图寻找名为 `*` 的文件，然后报告寻找失败。
  
现在，如果我们提供匹配前置点号的模式：

```
$echo #回应隐藏文件
. .. .dos .tres .uno
$ls .* #列出隐藏文件
.dos  .tres  .uno
\ 
.：
\ 
..：
hidden  test
```
 
linux 目录总是包含特殊实例..(父目录)。(当前目录)，且 shell 会床底所有的匹配给这两个程序。`echo` 只报告他们，但 `ls` 会做更多的事：当命令行参数为目录时，他会列出该目录的内容。
 
可以显示目录本身的相关信息，而非其内容，只要使用 `-d` 选项即可：

```
$ls -d .*
.  ..  .dos  .tres  .uno
$ls -d ../*
../hidden  ../test
```
 
由于你通常要的不是显示父目录，因此，`ls` 还提供了 `-a` 选项，提供打印当前目录的所有文件，包含隐藏文件：

```
$ls -a
.  ..  .dos  .tres  .uno
```
 
**长的文件列出**

由于 `ls` 知道他的参数是文件，所以可以进一步的报告相关细节，尤其是文件系统的一些 metadata，这个功能通常以 `-l`(字母) 选项完成：

```
$ls -l /bin/*sh
-rwxr-xr-x. 1 root root 938832 7 月  18 2013 /bin/bash
lrwxrwxrwx. 1 root root      4 5 月  29 02：34 /bin/csh -> tcsh
-rwxr-xr-x. 1 root root 109672 10 月 17 2012 /bin/dash
lrwxrwxrwx. 1 root root      4 5 月  29 02：25 /bin/sh -> bash
-rwxr-xr-x. 1 root root 387328 2 月  22 2013 /bin/tcsh
```
 
下面来介绍一下这种输出类型是个什么东西

首字符描述文件类型：`-` 为一般文件，`d` 为目录，`l` 为符号连接，此处是文件。
接下来的 9 个字符，则报告文件权限：针对每个用户，组以及除此外的其他人。`r` 表示读权限，`w` 表示写权限，`x` 表示执行权限，如果未提供权限则是 `-`。
 
第二栏包含连接计数：在这里，只有 `/bin/zsh` 拥有直接连接到另一个文件，但是还有其他的文件未显示于这里的输出，因为他们的名称与参数模式不匹配。
 
第三栏，第四栏报告文件所有者和所属组，第五栏则是以字节为单位的文件大小。
 
接下来的三栏是最后修改的时间戳。这里显示的是一直沿用下来的形式：月，日，年。
 
最后说一下 `od` 命令

`od` 命令用于输出文件的八进制，十六进制或其他格式编码的字节，通常用于显示或查看文件中不能直接显示在终端的字符。
 
常见的文件为文本文件和二进制文件。此命令主要用来查看保存在二进制文件中的值。比如，程序可能输出大量的数据记录，每个数据是一个单精度浮点数。这些记录存放在一个文件中，如果想查看下这个数据，这时候 `od` 命令就派生用场了。个人认为：`od` 命令主要用来格式化输出文件数据，即对文件中的数据进行无二义性的解释。不管是 IEEE754 格式的浮点数还是 ASCII 码，`od` 命令都能按照需求输出他们的值。
  
语法：od [选项] [参数]
 
选项：

- `-a`：此参数的效果和同时指定 `"-ta"` 参数相同；
- `-A`：< 字码基数 >：选择以何种基数计算字码；
- `-b`：此参数的效果和同时指定 `"-toC"` 参数相同；
- `-c`：此参数的效果和同时指定 `"-tC"` 参数相同；
- `-d`：此参数的效果和同时指定 `"-tu2"` 参数相同；
- `-f`：此参数的效果和同时指定 `"-tfF"` 参数相同；
- `-h`：此参数的效果和同时指定 `"-tx2"` 参数相同；
- `-i`：此参数的效果和同时指定 `"-td2"` 参数相同；
- `-j< 字符数目 >` 或 `--skip-bytes=< 字符数目 >`：略过设置的字符数目；
- `-l`：此参数的效果和同时指定 “-td4” 参数相同；
- `-N< 字符数目 >` 或 `--read-bytes=< 字符数目 >`：到设置的字符树目为止；
- `-o`：此参数的效果和同时指定 “-to2” 参数相同；
- `-s< 字符串字符数 >` 或 `--strings=< 字符串字符数 >`：只显示符合指定的字符数目的字符串；
- `-t< 输出格式 >` 或 `--format=< 输出格式 >`：设置输出格式；
- `-v` 或 --output-duplicates：输出时不省略重复的数据；
- `-w< 每列字符数 >` 或 `--width=< 每列字符数 >`：设置每列的最大字符数；
- `-x`：此参数的效果和同时指定 `"-h"` 参数相同；
- `--help`：在线帮助；
- `--version`：显示版本信息。

参数：  
文件：指定要显示的文件
 
案例：

准备一个 test 文件

```
$ echo abcdef g >test
$cat test
abcdef g
```

使用单字节八进制解释进行输出，注意左侧的默认地址格式为八字节

```
$ od -b test
0000000 141 142 143 144 145 146 040 147 012
0000011
```
 
使用 ASCII 码进行输出，注意其中包括转义字符

```
$ od -c test
0000000   a   b   c   d   e   f       g  \n
0000011
```
 
使用单字节十进制进行解释

```$od -t d1 test  (这里是数字 1)
0000000   97   98   99  100  101  102   32  103   10
0000011
```
 
设置地址格式为十进制

```$ od -A d -c test 
0000000   a   b   c   d   e   f       g  \n
0000009
``` 
 
设置地址格式为十六进制

```$od -A x -c test 
000000   a   b   c   d   e   f       g  \n
000009
``` 
 
跳过开始的两个字节

```$ od -j 2 -c test 
od -j 2 -c test   
0000002   c   d   e   f       g  \n
0000011
``` 
 
跳过开始的两个字节，并且仅输出两个字节

```$ od -N 2 -j 2 -c test 
0000002   c   d
0000004
``` 
 
每行仅输出一个字节

```$od -w1 -c test (这里也是数字 1)
0000000   a
0000001   b
0000002   c
0000003   d
0000004   e
0000005   f
0000006    
0000007   g
0000010  \n
0000011
```  
 
每行输出两个字节：

```$ od -w2 -c test 
0000000   a   b
0000002   c   d
0000004   e   f
0000006       g
0000010  \n
0000011
``` 
 
每行输出 3 个字节，并使用八进制字节进行解释

```$ od -w3 -b test 
0000000 141 142 143
0000003 144 145 146
0000006 040 147 012
0000011
```