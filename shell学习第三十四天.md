## shell 学习三十四天----printf 详解

### printf

先来看一个简单的例子： 使用命令 `printf "hello，world\n"`，  
输出：hello，world  

再使用 `echo "hello，world\n"`，  
输出为：hello，world\n

案例二： 使用命令 `printf "%s\n" hello，world`  
输出结果为：hello，world  

**printf 命令的完整语法有两个部分**：

`printg format-string [arguments]`

- 第一部分为描述格式规格的字符串，他的嘴尖提供方式是放在引号内的字符串常熟。
- 第二部分为参数列表，例如字符串或变量值的列表，该列表需与格式规格相对应。
格式字符串结合要以字面意义输出的文本，它使用的规格是描述如何在 `printf` 命令行上格式化一连串的参数。一般字符都按照字面上的意义输出。主义序列会被解释 (与 ehco 相似)，然后输出为相应的字符。格式指示符是以 `%` 字符开头且由已定义的字母集之一作为结尾，用来控制接下来想对应参数的输出。
 
 
printf 的语法：`printf format [string]`

用途：为了从 shell 脚本中产生输出。由于 printf 的行为是由 POSIX 标准所定义，因此使用 printg 的脚本比使用 echo 更具可移植性。

主要选项：无

行为：printf 使用 format 字符串控制输出。字符串里的纯字符都会如实打印。echo 的转义序列会被解释。包括 % 与一个字母的格式指示符。用来指示相对应的参数字符串的格式化。 

**printf 的转义序列**

```
序列
说明
\a
警告字符，通常为 ASCII 的 BEL 字符
\b
后退
\c
抑制 (不显示) 输出结果中任何结尾的换行字符；而且，任何留在参数里的字符，任何接下来的参数以及任何留在格式字符串中的字符，都被忽略
\f
换页
\n
换行
\r
回车
\t
水平制表符
\v
垂直制表符
\\
一个字面上的反斜杠字符
\ddd
表示 1 到 3 位数八进制的字符。尽在格式字符串中有效
\0ddd
表示 1 到 3 位的八进制字符
```
 
转义序列只在格式字符串中会被特别对待，也就是说，出现在参数字符串里的专利序列不会被解释：

使用命令：`printf "%s\n" "abc\ndef"` 
 
输出结果：abc\ndef
 
 
**printf 格式指示符**

```
%c
ASCII 字符。显示相对应参数的第一个字符
%d，%i
十进制整数
%e
浮点格式 ([-d].precisione [+-dd])
%E
浮点格式 ([-d].precisionE [+-dd])
%g
%e 或 %f 转换，看哪一个较短，则删除结尾的零
%G
%E 或 %f 转换，看哪一个较短，则删除结尾的零
%s
字符串
%u
不带正负号的十进制值
%x
不带正负号的十六进制。使用 a 至 f 表示 10 至 15
%%
字面意义的 %
%X
不带正负号的十六进制。使用 A 至 F 表示 10 至 15
``` 
 
**精度的含义**

```
转换
精度含义
%d，%i，%o，%u，%x，%X
要打印的最小位数。当值的位数少于此数字时，会在前面补零。默认精度为 1
%e，%E
要打印的最小位数。当值的位数少于此数字时，会在小数点后面补零，默认为精度为 6。精度为 0 则表示不显示小数点小数点右边的位数
%f
小数点右边的位数
%g，%G
有效位数的最大数目
%s
要打印字符的最大数目
```

案例一：  
使用命令：`printf "%.5d\n" 15`  
输出：00015  

案例二：  
使用命令：`printf "%.10s\n" "a very long string"`  
输出：a very lon  

案例三：   
使用命令：`printf "%.2f\n" 123.4567`  
输出：123.46  
 
**printf 的标志**

```
字符
意义
-
将字段里已格式化的值向左对齐
空格 (space)
在正值前置一个空格，在负值前置一个负号
+
总是在数值之前放置一个正号或负号，即便是正值也是
#
下列形式选择其一：%o 有一个前置的 o；%x 与 %X 分别前置的 0x 与 0X.%e，%E 与 %f 总是在结果中有一个小数点；%g 与 %G 为没有结尾的零。
0
以零填补输出，而非空白。这仅发生在字段宽度大于转换后的情况。在 C 语言里，该标志应用到所有输出格式，及时是非数字的值也是一样。对于 printf 命令而言，它仅应用到数值格式
```

案例一：  
使用命令：`printf "%-20s%-15s%10.2f\n" "Shan" "zhang" 35`         
输出：Shan                zhang               35.00  
分析：
  
- `%-20s` 表示一个左对齐、宽度为 20 个字符字符串格式，不足 20 个字符，右侧补充相应数量的空格符。
- `%-15s` 表示一个左对齐、宽度为 15 个字符字符串格式。
- `%10.2f` 表示右对齐、10 个字符长度的浮点数，其中一个是小数点，小数点后面保留两位。

案例二：  
使用命令：`printf "|%10s|\n" hello`  
输出：|     hello|  
分析：`%10s` 表示右对齐，宽度为 10 的字符串，如不足是个字符串，左侧补充相应数量的空格数。  

案例三：  
使用命令：`printf "|%-10s|\n" hello`  
输出：|hello     |  
分析： 和案例二比较一下  

案例四：   
使用命令：`printf "%x %#x\n" 15 15`  
输出：f 0xf  
分析： 

- 如果#标志和 `%x`，`%X` 搭配使用，在输出十六进制数字时，前面回家 `0x` 或者 `0X` 前缀。  
- 使用标志符的作用主要是为了动态的指定宽度和精度。
 
综合案例分析：  
字符串向左向右对齐案例：  
使用命令：`printf "|%-10s| |%10s|\n" hello world`  
输出 |hello     | |     world|  

空白标志案例：  
使用命令：`printf "|% d| |% d|\n" 15 -15`                   
输出：| 15| |-15|

`+` 标志案例：
使用命令：`printf "|%+d| |%+d|\n" 15 -15`  
输出：|+15| |-15|

`#`标志案例：
使用命令：`printf "%x || %#X\n" 15 15`
输出：f || 0XF

`0` 标志案例：
使用命令：`printf "%05d\n" 15`
输出：00015
 
对于转换指示符 `%b`，`%c` 与 `%s` 而言，相对应的参数都是为字符串。否则，他们会被解释为 C 语言的数字常数 (开头的 0 位八进制，以及开头的 `0x` 与 `0X` 为十六进制)。更进一步说，如果参数的第一个字符为单引号或双引号，则县桂英的数值是字符串的第二个字符的 ASCII 值： 
 
命令：`printf "%s is %d \n" a "'a"`  
输出：a is 97   

当参数多于格式指示符时，格式指示符会根据需要再利用。这种做法在参数列表长度未知时时很方便的，例如来自通配符表达式。如果留在格式字符串里剩下的指示符比参数多时，如果是数值转换，则遗漏的值会被看做是零，如果是字符串转换，则被视为空字符串 (虽然可以这么用，但比较好的方式应该是一一对应关系，即提供的参数数目和格式字符串数目相同)。如果 printf 无法进行格式的转换，便返回一个非零的退出状态。