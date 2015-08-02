## shell 学习四十五天----xargs

执行命令：xargs  
当 find 产生一个文件列表时，该列表提供给另一个命令有时是很有用的。  

案例：  

```$touch abc.c erd.c oiy.c
$ll
./erd.c
./abc.c
./oiy.c
$find -name '*.c'| rm
rm：缺少操作数
请尝试执行 "rm --help" 来获取更多信息。
$find -name '*.c'| xargs rm
$find -name '*.c'
```

无任何显示，说明已成功删除。

1. 简介，之所以能用到这个命令，关键是由于很多命令不支持管道 (|) 来传递参数，而日常工作中有这个必要，所以就有了 xargs 命令，如上例。
xargs 可以读入 stdin 的资料，并且以空白子元或断行子元作为分辨，将 stdin 的资料分隔成为 atguments，因为是以空白子元作为分隔，所以，如果有一些文件名或者其他有意义的名词内含空白子元的时候，xargs 就可能会出现误判了。  
```$touch 'file 1.log' ‘file 2.log’
$ll
总用量 0
-rw-r--r-- 1 root root 0 7 月  13 10：18 file 1.log
-rw-r--r-- 1 root root 0 7 月  13 10：18 file 2.log
$find -name '*.log'
./file 2.log
./file 1.log
$find -name '*.log' | xargs rm
rm：无法删除 "./file"：没有那个文件或目录
rm：无法删除 "2.log"：没有那个文件或目录
rm：无法删除 "./file"：没有那个文件或目录
rm：无法删除 "1.log"：没有那个文件或目录
```  
	- 原因很简单，`xargs` 默认是以空白字符 (空格，tab，换行符) 来分割记录的，因此文件名 `./file 1.log` 被解释成了两个记录`./file` 和 `1.log`，不幸的是 `rm` 找不到这两个文件。  
	- 为了解决此类问题，聪明的人类想出了一个办法，让 `find` 在打印出一个文件名之后接着输出一个 `null` 字符 (' ') 而不是换行符，然后再告诉 `xargs` 也用 `null` 字符来作为记录的分隔符，这就是 `find` 的 `-print 和 xargs` 的 `-0` 选项。
`$find -name '*.log' -print0 | xargs -0 rm`

2. 主要选项

```
选项
含义
-0
当 stdin 含有特殊子元的时候，将其当成一般字符
-a file
从文件中读入作为 stdin
-e flag
注意有的时候可能会是 -E，flag 必须是一个以空格分割的标志，当 xargs 分析到含有 flag 这个标志的时候就停止
-p
当每次执行一个 argument 的时候咨询问一次用户。
-n num
后面加次数，表示命令在执行的时候一次用 arguments 的个数，默认是用所有的。
-t
便是先打印命令，然后在执行
-i
或者是 -I，将 xargs 的每项名称，一般是一行一行的赋值给 {}，可以用 {} 代替
-r no-run-if-enpty
当 xargs 的输入为空的时候则停止 xargs，不用再去执行了
-s num
命令行的最大字符数
-d delim
分隔符，默认的 xargs 分隔符是回车，argument 的分隔符是空格，这里修改的是 xargs 的分隔符
-x
exit 的意思，主要是匹配 -s 使用
-P
```

修改最大的进程数，默认是 1，为 0 的时候 as mang as it can
 
find -print 和 -print0 的区别：

- `-print` 每一个输出后会添加一个回车换行符，而 `-print0` 则不会。