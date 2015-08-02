## shell 学习第十天----sed 查找与替换

### 在文本文件离进行替换

在很多 shell 脚本的工作都从通过 grep 或 egrep 去除所需的文本开始。 正则表达式查找的最初结果，往往就成了要拿来作进一步处理的” 原始数据”。 通常，文本替换至少需要做一件事，就是讲一些字以另一些字取代，或者删除匹配行的某个部分。执行文本替换的正确程序应该是 sed----流编辑器。

sed 的设计就是用来批处理而不是交互的方式编辑文件。 当药做好几个变化的时候，不管是对一个还是对数个文件，比较简单的方式就是将这些变更部分写到一个编辑的脚本里，再将此脚本应用到所有必须修改的文件，sed 的存在目的就在这里。
 
在 shell 里，sed 主要用于一些简单的文本替换，所以我们先从他开始。
 
基本用法： 

我们经常在管道中间使用 sed，用来执行替换操作，做法是使用 s 命令 ---- 要求正则表达式寻找，用替换文本替换匹配的文本呢，以及可选的标志:

- `sed ‘s’:.*//’  /etcpasswd  |`     删除第一个冒号之后所有的东西
- `sort -u`          排序列表并删除重复部分

sed 的语法：

- `sed [-n] ‘editing command’ [file...]`
- `sed [-n] -e ‘editing command’ [file...]`
- `sed [-n] -f script-file...  [file...]`

用途：

sed 可删除 (delete)、改变 (change)、添加 (append)、插入 (insert)、合、交换文件中的资料行，或读入其它档的资料到文 > 件中，也可替换 (substuite) 它们其中的字串、或转换 (tranfer) 其中的字母等等。例如将文件中的连续空白行删成一行、"local" 字串替换成 "remote"、"t" 字母转换成 "T"、将第 10 行资料与第 11 资料合等。

总合上述所言，当 sed 由标准输入读入一行资料并放入 `pattern space` 时,sed 依照 `sed script` 的编辑指令逐一对 `pattern space` 内的资料执行编辑，之後，再由 `pattern space` 内的结果送到标准输出，接着再将下一行资料读入。 如此重执行上述动作，直至读完所有资料行为止。

### 小结

**记住**:

1. sed 总是以行对输入进行处理
2. sed 处理的不是原文件而是原文件的拷贝
 
主要参数：

- -e: 执行命令行中的指令，例如：`sed -e 'command' file(s)`
- -f: 执行一个 sed 脚本文件中的指令，例如： `sed -f  scriptfile file(s)`
- -i: 与 -e 的区别在于：当使用 -e 时，sed 执行指令并不会修改原输入文件的内容，只会显示在 bash 中，而使用 -i 选项时，sed 执行的指令会直接修改原输入文件。
- -n: 读取下一行到 `pattern space`。
 
 
**行为模式**：

读取输入文件的每一行。 假如没有文件的话，则是标准输入。 以每一行来说,sed 会执行每一个应用倒数入行的 `esiting command`。 结果会写到标准输出 (默认情况下，或是显式的使用 p 命令及 -n 选项)。 若无 -e 或 -f 选项，则 sed 会把第一个参数看做是要使用的 `editing command`。
 
- `find  /home/tolstoy  -type -d -print` // 寻找所有目录
- `sed ‘s;/home/tolstor;/home/lt/;’` // 修改名称; 注意: 这里使用分号作为定界符
- `sed ‘s/^/mkdir /’` // 插入 mkdir 命令
- `sh -x `                          // 以 shell 跟踪模式执行
 
上述脚本是说将 `/home/tolstoy` 目录结构建立一份副本在 `/home.lt` 下 (可能是为备份) 而做的准备
 
 
**替换案例**：

Sed 可替换文件中的字串、资料行、甚至资料区。其中，表示替换字串的指令中的函数参数为 s; 表示替换资料行、或资料区 > 的指令中的函数参数为 c。上述情况以下面三个例子说明。

**\* 行的替换**

`sed -e '1c/#!/bin/more' file (把第一行替换成#!/bin/more)`

 思考: 把第 n 行替换成 just do it

`sed -e 'nc/just do it' file`  
`sed -e '1,10c/I can do it' file  (把 1 到 10 行替换成一行:I can do it)`

思考: 换成两行 (I can do it! Let's start)

`sed -e '1,10c/I can do it!/nLet'"/'"'s start' file`

**\* 字符的替换**

- `$ sed 's/test/mytest/g' example`----- 在整行范围内把 test 替换为 mytest。如果没有 g 标记，则只有每行第一个匹配的 test 被替换成 mytest。
- `$ sed -n 's/^test/mytest/p' example`-----(-n) 选项和 p 标志一起使用表示只打印那些发生替换的行。也就是说，如果某一行开头的 test 被替换成 mytest，就打印它。
- `$ sed 's/^192.168.0.1/&localhost/' example`-----& 符号表示替换换字符串中被找到的部份。所有以 192.168.0.1 开头的行都会被替换成它自已加 localhost，变成 192.168.0.1localhost。
- `$ sed -n 's/loveable/\1rs/p' example-----love` 被标记为 1，所有 loveable 会被替换成 lovers，而且替换的行会被打印出来。
- `$ sed 's#10#100#g' example`----- 不论什么字符，紧跟着 s 命令的都被认为是新的分隔符，所以，“#” 在这里是分隔符，代替了默认的 “/” 分隔符。表示把所有 10 替换成 100。
 
 
**替换与查找**

在 s 命令里以 g 结尾表示的是: 全局性，意即” 替代文本取代正则表达式中每一个匹配的”。 如果没有设置 gsed 指挥取代第一个匹配的。
 
鲜为人知的是: 可以在结尾指定数字，只是第 n 个匹配出现才要被取代:

- `sed ‘s/Tom/Lisy/2’ < Test.txt`   仅匹配第二个 Tom
通过给 sed 增加一个 -e 选项的方式能让 sed 接受多个命令。
- `sed -e ‘s/foo/bar/g’ -e ‘s/chicken/cow/g’  myfile.txt 1>myfile2.txt`
用 shell 命令将 test.log 文件中第 3-5 行的第 2 个“filter” 替换成“haha”
- `sed -i '3，5s/filter/haha/2' test.log`

