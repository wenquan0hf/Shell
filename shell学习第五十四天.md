## shell 学习五十四天----进程系统调用的追踪 strace

### strace

前言：`strace` 常用来跟踪进程执行时的系统调用的所接受的信号。在 linux 世界，进程是不能直接访问硬件设备，当进程需要访问硬件 (比如读取磁盘文件，接收网络数据等等) 时，必须由用户态模式切换至内核态模式，通过系统调用访问硬件设备。`strace` 可以跟踪到一个进程产生的系统调用，包括参数，返回值，执行消耗的时间，有其在调试的时候，`strace` 能帮助你追踪到一个程序所执行的系统调用。当你想知道程序和操作系统是如何交互的时候，这是极其方便的，比如你想知道执行了哪些系统调用，并且以何种顺序执行。
 
**strace 详解**

格式：  
```strace [-dffhiqrtttTvxx] [-acolumn] [-eexpr] ... [-ofile] [-ppid] ... [-sstrsize] [-uusername] [-Evar=val] ... [-Evar]... [command [ arg ...] ]``` 
 
`strace -c [-eexpr] ... [-Ooverhead] [-Ssortby] [command [ arg...] ]`

选项：

```选项名
说明
-f ，-F
告诉 strace 同时跟踪 fork 和 vfork 出来的信号
-o(字母)xxxx.txt
输出到某个文档
-e execve
只记录 execve 这类系统调用。
```
 
案例：

首先使用 `vim` 编写一个 C 语言的程序，代码如下：

```\#filename test.c
\#include <stdio.h>
int main()
{
int a；
scanf(“%d”，&a)；
printf(“%09d\n”，a)；
return 0；
}
```

- 然后使用命令：`#gcc -o test test.c`，这样会得到一个可执行的文件。
- 我们执行这个可执行文件 (先不使用 strace)：`#./test`
- 执行期间这个程序会要求我们输入一个整数，我们输入 99，会得到以下结果：000000099

接着我们使用 `strace：#strace ./test`

输出很多，我就不复制，我一看这个输出的是我想到了黑客帝国这部电影，我第一次看的时候差不多就是这样... 当时觉得好炫酷，不过看见我自己电脑上的这一坨，你够了，我不想看到你!

输出的这一些内容称为 `strace` 的 `trace` 结构，从 `trace` 结构可以看到，系统首先调用 `execve` 开始一个新的进程，接着进行环境的初始化操作，最后停顿在 “read(0，” 上面，这就是执行到了我们的 `scanf` 函数，等待我们输入数字，在输入完 99 之后，在调用 `write` 函数将格式化后的数值 “000000099” 输出到屏幕上，最后掉用 `wxit_group` 退出进行，完成整个程序的执行过程。
 
**跟踪信号传递**
 
我们还是使用上面那个编译好的 test 程序，来观察进程接收信号的情况。还是先：`#strace ./test`，等到等待输入的画面的时候不要输入任何东西，然后打开另一个窗口，输入如下命令：

`\#killall test`

这个时候我们就能看到我们的 test 程序退出了，最后的 trace 结果的最后两行：

- `--- SIGTERM (Terminated) @ 0 (0) ---`
- `+++ killed by SIGTERM +++`

`trace` 中很清楚的告诉你 `tes`t 进程`“+++ killed by SIGTERM +++”`，其中 `SIGTERM` 信号为程序结束信号，与 `SIGKILL` 不同的是该信号可以被阻塞和处理。通常用来要求程序自己正常退出。`shell` 命令 `kill` 缺省产生 `SIGTERM`。
 
**系统调用统计**

`strace` 不光能追踪系统调用，通过使用 `-c` 选项，它还能江金城所有的系统调用做一个统计分析给你，案例如下：

```\#strace -c ./test(需要按下 Ctrl+C)
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
  -nan    0.000000           0         2           read
  -nan    0.000000           0         1           write
  -nan    0.000000           0         2           open
  -nan    0.000000           0         2           close
  -nan    0.000000           0         4           fstat
  -nan    0.000000           0        10           mmap
  -nan    0.000000           0         3           mprotect
  -nan    0.000000           0         1           munmap
  -nan    0.000000           0         1           brk
  -nan    0.000000           0         1         1 access
  -nan    0.000000           0         1           execve
  -nan    0.000000           0         1           arch_prctl
------ ----------- ----------- --------- --------- ----------------
100.00    0.000000                    29         1 total
```
 
- `-c` 选项的含义是统计每一系统调用的所执行的时间，次数和出错的次数等。
 
**常用参数说明**

除了 -c 参数之外，strace 还提供了其他有用的参数给我们，让我们方便的得到自己想要的信息，介绍如下：

重定向输出：

- 参数 -o 用在将 strace 的结果输出到文件中，如果不指定 -o 参数的话，默认的输出设备是 STDERR，也就是说使用 “-o filename” 和 “2>filename” 的结果是一样的。

以下这两个命令都是讲 strace 结果输出到文件 test.txt 中

`\#strace -c -o test.txt ./test`  
`#strace -c ./test 2>test.txt`

**对系统调用进行计时**

strace 可以使用参数 -T 将每个系统调用所花费的事件打印出来，每个掉用的时间花销现在在调用行最右边的尖括号里面。

```read(0，1
"1\n"，1024)                    = 2 <7.195016>
fstat(1，{st_mode=S_IFCHR|0620，st_rdev=makedev(136，0)，...}) = 0 <0.000010>
mmap(NULL，4096，PROT_READ|PROT_WRITE，MAP_PRIVATE|MAP_ANONYMOUS，-1，0) = 0x7f1431bd1000 <0.000014>
write(1，"000000001\n"，10000000001
)             = 10 <0.000011>
exit_group(0)                           = ?
```
 
表示看不懂，不懂得东西，现在不要深究。
 
**系统调用的时间**

这是一个很有用的功能，strace 会将每次系统调用的发生时间记录下来，只要使用 -t/tt/ttt 三个参数就可以看到效果了，具体案例如下：

```参数名
输出样式
说明
-t
10：50：10 exit_group(0)
输出结果精确到秒
-tt 
10：50：48.463555 exit_group(0)
输出结果精确到微秒
-ttt
1438138307.923671 exit_group(0)
精确到微妙，而且时间表示为 unix 时间戳
``` 
 
**截断输出**

- `-s` 参数用于指定 `trsce` 结果的每一行输出的字符串的长度，下面看看 `test` 程序中 `-s` 参数对结果有什么影响，现指定 `-s` 为 10，然后在 `read` 的地方输入一个超过 10 个字符的字符串：

```\#strace -s 10 ./test
read(0，123456789011
"1234567890"...，1024)          = 13
```

部分输出结果

分析：
  
- 我们一共输入了 12 个字符，而我们看到的结果只有 10 个  
- trace 一个现有的进程  
- strace 不光能自己初始化一个进程进行 trace，还能追踪现有的进程，参数 -p 就是去的这个用法，用法简单，具体如下：
`\#strace -p pid   // 跟踪指定的进程 PID`