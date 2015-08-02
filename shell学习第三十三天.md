## shell 学习三十三天----关于重定向

### 关于重定向

- 额外的重定向运算符
- 使用 `set -C` 搭配
- POSIX shell 提供了防止文件意外截断的选项: 执行 `set -C` 命令可打开 shell 所谓的禁止覆盖选项，当它再打开状态时，单纯的 `>` 重定向遇到目标文件已存在时，就会失败。`>|` 运算符则可以另 noclobber 选项失效。
 
提供行内输入的 `<<` 和 `<<-:` 使用 `program<<` 得力 `miter`，可以在 shell 脚本正文内提供输入数据；这样数据叫嵌入文件。在默认情况下，shell 可以在嵌入文件正文内做变量。命令和算数替换。

```
\#!/bin/bash
cd /home
du -s * |
        sort -nr |
                sed 10q |
                        while read amount name
                        do
                                mail -s "disk usage waring" $name<<EOF
                                Greetings ，You are one of the  top of 10 consumers of disk ...
                            Please clean up unneeded files ,as soon as possible
                            Thanks .
                            EOF
                        done
```
 
分析：其中邮件内容就是输入数据。
 
如果界定符以任何一种形式的引号括起来，shell 便不会处理输入的内文，案例:

```
[root@localhost tmp]# i=5
[root@localhost tmp]# cat <<'E'OF
\> this is the calue if i : $i
\> here is a command sub :$(echo hello,world)
\> EOF
this is the calue if i : $i
here is a command sub :$(echo hello,world)
```

界定符没有任何引号隔开:

```
cat <<EOF
\> this is the calue if i : $i
\> here is a command sub :$(echo hello,world)
\> EOF
this is the calue if i : 5
here is a command sub :hello,world
```

- 嵌入文件重定向器的第二种形式有一个负号结尾。这种情况下，所有开头的制表符 (Tab) 在传递给程序作为输入之前，都从嵌入文件与结束定界符中删除 (注意: 只有开头的制表符会被删除，开头的空格不会删除)。这么做，让 shell 脚本更易于阅读了。
- 以 `<>` 打开一个文件作为输入输出只用
- 使用 `program<>file`，可供读取与写入操作。默认是在标准输入上打开 file。一般来说，`<` 以只读模式打开文件，而 `>` 以只读模式打开。`<>` 运算符则是以读取与写入两种模式打开给定的文件。这交由 program 确定并充分利用；实际上，使用这个操作符并不需要太多的支持。
 
**文件描述符处理**

linux 用文件描述符来标示每一个文件对象。文件描述符是一个非负整数，可以唯一的标示回话中打开的文件。
 
bash 保留了 3 个文件描述符

```
文件描述符
缩写
描述
0
STDIN
标准输入
1
STDOUT
标准输出
2
STDERR
标准错误
```
 
- `STDIN` 文件描述符代表 shell 的标准输入，对于终端来说，白鸟准输入就是键盘。在使用输入重定向符号 (<) 时,linux 会用重定向指定的文件来替换标准输入文件描述符。
- `STDOUT` 文件描述符代表标准的 shell 输出。在终端上，标准输出就是显示器。使用输出重定向符号 (>，>>)，可以将要输出到显示上的内容从定向到指定的文件中。 
- `STDERR` 文件描述符用来处理错误消息，它代表 shell 的标准错误输出。
- 默认情况下 `STDOUT` 和 `STDERR` 指向同样的地方，默认情况下，错误消息也会输出到显示器输出。
 
**重定向错误输出**

只重定向错误，如下：在上面的表中看到 STDERR 文件描述符被设置成 2

```
[root@localhost tmp]# ls t 2>error
[root@localhost tmp]# cat error 
```

ls: 无法访问 t：没有那个文件或目录

重定向错误和数据：

```
[root@localhost tmp]# mkdir task
[root@localhost tmp]# cd task/
[root@localhost task]# mkdir task
[root@localhost task]# ls task t 2>error 1>list
[root@localhost task]# cat list
task：
[root@localhost task]# cat error 
```

ls：无法访问 t：没有那个文件或目录

分析: 

- 如果出现错误，就将错误信息放入 error；如果正确，就将输出信息放到 list 中。
- 也可以将 `STDOUT` 和 `STDERR` 输出到同一个文件：

```
[root@localhost tmp]# mkdir task
[root@localhost tmp]# ls task t&>out
[root@localhost tmp]# cat out       
```

ls: 无法访问 t：没有那个文件或目录

task：

在脚本中重定向输出有两种方式：

1. 临时重定向每行输出
2. 永久重定向脚本中的所有命令
 
**先看第一种----临时重定向**

如要故意在脚本中生成错误消息，需要将单独的一行 (`echo &”error msg>” &2`) 输出重定向到 `STDERR`。

案例：

```
\#!/bin/bash
echo "error msg" >&2
echo "normal msg"
执行脚本，输出结果:
error msg
normal msg
```

分析: 

- 在重定向文件描述符时，你必须在文件描述符数字和输出重定向符号之间加上一个 `&` 符号。
- 默认情况下，linux 会将 `STDERR` 定向到 `STDOUT`。但是，如果在运行脚本时重定向了 `STDERR`，脚本中所有定向到 `STDERR` 的文本都会被重定向，案例：

``` 
[root@localhost tmp]# ./test.sh 2>test
normal msg
[root@localhost tmp]# cat test
error msg
```

分析：把执行脚本的标准错误输出重定向到 test，而在上一步中，将 “error msg” 定向到了标准错误输出。
 
**第二种 ---- 永久重定向**

如果在脚本中有大量数据需要重定向，可以使用 `exec` 命令告诉 shell 在脚本执行期间重定向到某个特定文件描述符：

bash 代码：

```
\#!/bin/bash
exec 1>testout
echo "error msg2"
echo "normal msg2"
echo "error msg1"
echo "normal msg1"
```

执行：

```
[root@localhost tmp]# ./test1.sh  
[root@localhost tmp]# cat testout 
error msg2
normal msg2
error msg1
normal msg1
```
 
在脚本中重定向输入：

使用 `exec` 命令将 `STDIN` 重定向到 linux 系统上的文件中：

`exec 0< testfile`

这个命令告诉 shell 从文件 testfile 中获得输入，而不是 `STDIN`。
 
**扩展 exec 命令**：

语法：
`exec [program [arguments...] ]`

用途：
以新的程序取代 shell，或改变 shell 本身的 I/O 设置。

主要选项:
无

行为：
搭配参数----也就是使用指定的程序取代 shell，以传递参数给它。如果只是用 I/O 重定向，则会改变 shell 本身的文件描述符。
 
具体的参考:
[http://www.cnblogs.com/peida/archive/2012/11/14/2769248.html](http://www.cnblogs.com/peida/archive/2012/11/14/2769248.html)