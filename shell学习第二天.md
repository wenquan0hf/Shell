## shell学习第二天

### 脚本位于第一行的 #!

当 shell 执行一个程序时，会要求 linux 内核启动一个新的进程，以便在该进程里执行所指定的程序。 内核知道如何为编译性程序做这件事。 但是我们的nusers Shell 脚本并非编译性程序; 当 shell 要求内核执行它的时候，内核无法处理，并且回应“not executable format file”，接着会启动一个新的 `/bin/sh`(标准 shell) 副本来执行该程序。
 
当系统只有一个 shell 是，“退回到 /bin/sh” 的机制很方便。 但是现在的 linux 都拥有好几个 shell，因此需要通过一宗方式，告知 linux 内核用哪个 shell 来执行所指定的 shell 及哦啊本。

linux 有多个 shell 带来的好处是有助于执行机制通用化，让用户得以直接引用任何程序语言解释器，而非只是一个命令 shell。

例如：文件开头存在 `\#!/bin/csh` 则说明执行的是 csh 脚本，相同的，例如我们可以这样引用独立的 awk 程序：
  
`\#! /bin/awk -f`  
此处是 awk 程序  
shell 脚本的第一行通常是 `#!/bin/sh`。如果不这样是不符合标准的，自觉修改这个路径，将其改为符合 POSIX 标准的 shell。  
 
以下是几个初级的陷阱：

1. 对 `#!` 这一行的长度尽量不要超过 64 个字符  
2. 脚本的可移植性取决于是否有完整的路径名称  
3. 不要在选项之后放置任何空白，因为空白也会跟着选项一起传递给被引用的程序  
4. 需要知道解释器的完成路径的名称。 这样可以规避可移植性的问题， 厂商不同，同样的东西可能放在不同的地方  
5. 一些较久的系统，内核不具备 `#!` 的能力，有些 shell 会自行处理，这些 shell 对于 `#!` 与紧随其后的解释器名称之间是否可以有空白，可能有不同的解释
  
查看当前发行版本可以使用的 `shell;cat/etc/shells` 查看系统默认的 `shell;echo $SHELL；` 一般情况下是输出 `/bin/bash`。如果想切换 shell 的版本，只需要直接输入 shell 的版本。 例如想使用 csh，直接输入 csh 即可，使用 exit 退出当前 shell 回到原 shell。

