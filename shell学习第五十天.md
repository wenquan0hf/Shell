## shell 学习五十天----查看进程 ps 命令

### 进程列表

- 列出进程中最重要的命令便是进程状态命令：`ps`。
- `ps` 命令是进程状态 (Process Status) 的缩写。`ps` 命令用来列出系统中当前运行的那些进程。`ps` 命令列出的是当前那些进程的快照，就是执行 `ps` 命令的那个时刻的那些进程，如果想要动态的显示进程信息，就可以使用 `top` 命令。
 
要对进程进行检测和控制，首先必须要了解当前进程的情况，也就是需要查看当前进程，而 `ps` 命令就是最基本同时也是非常强大的进程查看命令。 使用该命令可以确定有哪些进程正在运行和运行的状态，进程是否结束，进程有没有僵尸，哪些进程占用了过多的资源等等。 总之大部分信息都是可以通过执行该命令得到的。
 
`ps` 为我们提供了进程的一次性 (不要给性加重音) 的查看，他所提供的查看结果并不动态连续；如果想对进程进行时间监控，应该使用 `top` 工具。
 
- `kill` 命令用来杀死进程
 
Linux 上进程有五种状态

1. 运行 (正在运行或在运行队列中的等待)
2. 中断 (休眠中，受阻，在等待某个条件的形成或接收到信号)
3. 不可中断 (收到信号不唤醒和不可运行，进程必须等待直到有中断发生)
4. 僵尸 (进程已终止，但进程描述符存在，直到父进程调用 `wait()` 系统调用后释放)
5. 停止 (进程收到 `SIGSTOP`，`SIGSTP`，`SIGTIN`，`SIGTOU` 信号后停止运行)
 
ps 工具标识进程的物种状态码：

```
状态码
说明
D
不可中断
R
运行
S
中断
T
停止
Z
僵尸
``` 
 
ps 详解：

1. 命令格式：
`ps [参数]`
2. 功能
用来显示当前进程的状态
3. 命令参数
```参数
说明
a
显示所有进程
-a
显示统一终端下的所有程序
-A
显示所有进程
c
显示进程的真实名称
-N
反向选择
-e
等于”-A”
e
显示环境变量
f
显示程序间的关系
-H
显示树状结构
r
显示当前中断的进程
T
显示当前终端的所有进程
u
指定用户的所有进程
-au
显示较详细的资讯
-aux
显示所有包含其他使用者的进程
-C< 命令 >
列出指定命令的状态
--lines< 行数 >
每页显示的行数
--width< 字符数 >
每页显示的字符数
--help
显示帮助信息
--version
显示版本信息
``` 
4. 简单的使用

案例 1：显示所有进程：

```#ps -A
\ 
 PID TTY          TIME CMD
    1 ?        00：00：04 init
    2 ?        00：00：00 kthreadd
    3 ?        00：00：00 migration/0```
省略部分结果
 
案例 2：显示指定用户信息：

```\#ps -u root
  PID TTY          TIME CMD
    1 ?        00：00：04 init
    2 ?        00：00：00 kthreadd
3 ?        00：00：00 migration/0```
省略部分结果
 
案例 3：显示所有进程信息，连同命令行

```\#ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 19：45 ?        00：00：04 /sbin/init
root         2     0  0 19：45 ?        00：00：00 [kthreadd]
root         3     2  0 19：45 ?        00：00：00 [migration/0]
root         4     2  0 19：45 ?        00：00：00 [ksoftirqd/0]```
省略部分结果
 
案例 4：`ps` 与 `grep` 常用组合用法，查找特定进程

命令：
```\#ps -ef|grep ssh
root      1358     1  0 19：46 ?        00：00：00 /usr/sbin/sshd
root      1650  1358  0 19：47 ?        00：00：00 sshd：root@pts/0 
root      3598  1652  0 21：27 pts/0    00：00：00 grep ssh```
 
案例 5：将目前属于您自己这次登入的 PID 与相关信息列出

```\#ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0  1652  1650  0  80   0 - 27116 wait   pts/0    00：00：00 bash
4 R     0  3600  1652  0  80   0 - 27033 -      pts/0    00：00：00 ps```
 
分析说明：各相关信息的意义：

- `F` 代表这个程序的标识 (flag)，4 代表使用者为 `super user`
- `S` 代表这个程序的状态 (STAT)。
- `UID`：程序被该 UID 所拥有
- `PID`：就是这个程序的 ID
- `PPID`：则是其上级父程序的 ID
- `C`：CPU 使用的资源百分比
- `PRI`：这个是 Priority(优先执序行) 的缩写
- `NI`：这个是 nice 值
- `ADDR`：这个是 kernel function，指出该程序在内存的那个部分。 如果是个 running 的程序，一般就是”-”。
- `SZ`：使用掉的内存大小
- `WCHAN`：目前这个程序是否正在运作当中，若为 - 表示正在运作
- `TTY`：登入这的终端机位置
- `TIME`：使用掉的 CPU 时间
- `CMD`：所下达的指令为何
 
在预设的情况下，`ps` 仅会列出与目前所在的 `bash shell` 有关的 `PID` 而已，所以当我们使用 `ps -l` 的时候，只有三个 `PID`。
 
案例 6：列出目前所有的正在内存当中的程序

```\#ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  19356  1612 ?        Ss   19：45   0：04 /sbin/init
root         2  0.0  0.0      0     0 ?        S    19：45   0：00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    19：45   0：00 [migration/0]```
省略部分结果

分析说明：  

- `USER`：该 process 是属于哪个使用者账号的
- `PID`：该 process 的号码
- `%CPU`：该 process 使用掉的 CPU 资源百分比
- `%MEM`：该 process 所占用的物理内存百分比
- `VSZ`：该 process 使用掉的虚拟内存量 (kb)
- `RSS`：该 process 占用的固定的内存量 (kb)
- `TTY`：该 process 是在哪个终端机上运行，若与终端机无关，则显示 ? ，另外，tty1-tty6 表示本机上的登入者程序，若为 pts/0 等等，则表示为由网络接进主机的程序。
- `STAT`：该程序目前的状态，主要状态有：R(该程序目前正在运行，或者是可被运行)，S(该程序目前正在睡眠中，但可被某些讯号唤醒)，T(该程序应该已经终止，但是其父进程却无法正常的终止它，造成僵死程序的状态)。
- `START`：该 process 被触发启动的时间
- `TIME`：该 process 实际使用 CPU 运作的时间
- `COMMAND`：该程序的实际命令。
  
案例 7：列出类似程序树的程序显示

```\#ps -axjf
Warning：bad syntax，perhaps a bogus '-'? See /usr/share/doc/procps-3.2.8/FAQ
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
    0     2     0     0 ?           -1 S        0   0：00 [kthreadd]
    2     3     0     0 ?           -1 S        0   0：00  \_ [migration/0]
    2     4     0     0 ?           -1 S        0   0：00  \_ [ksoftirqd/0]
    2     5     0     0 ?           -1 S        0   0：00  \_ [migration/0]```
  
其他案例：

使用：  
```\#ps -aux|more // 实现分页查看```
 
使用：  
```\#ps -aux>test.txt // 把所有进程显示出来，并输出到 test.txt 文件```
 
使用：  
```\#ps -o pid，ppid，pgrp，session，tpgid，comm// 输出指定的字段
  PID  PPID  PGRP  SESS TPGID COMMAND
 1556  1554  1556  1556  1582 bash
 1582  1556  1582  1556  1582 ps```
