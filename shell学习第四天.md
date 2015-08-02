## shell 学习第四天----华丽的 printf 输出

printf 命令模仿 C 程序库里的 printf() 库程序。几乎复制了该函数的所有功能，如同 echo 命令，printf 命令可以输出简单的字符串:  

`printf  “hello world\n”`

通过观察 echo 和 printf 的输出的不同，可以发现 echo 会提供自动换行，printf 不会提供自动换行，所以那些转移序列在 printf 发挥的很好。  

`printf` 命令的完整语法分为两部分:  

`printf format-string [arguments....]`

分析:`printf` 是命令，不解释。`format-string` 为格式控制字符串，`arguments` 为参数列表。  
printf 命令不用加括号。
format-string 可以没有引号，但是最好加上，单双引号均可。参数多于格式控制符(%)时，format-string 可以重用，可以将所有参数都转换
arguments:使用空格分割，不用逗号。
`printf “%d,%s\n” 1 abc`  这里输出的是 1, abc。有没有引号都可以。
 
如果没有 `arguments %s` 用 `NULL` 表示，`%d` 用 0 表示   

例如 :`printf “%s  ,  %d\n”` 输出结果为 0  

format-string 的可重用性:`printf “%s” abc def==>abcdef ` 
  
如果以 %d 来显示字符串，会有警告，提示无效的数字，此时的默认值为 0。 例如:`printf "%d\n" abc==>bash: printf: abc: invalid number 0;` 既然 shell 的 `printf` 和 C 的 `printf` 差不多，那么他们也都支持 %。例如:`printf  “%s\n” hello` 输出 hello 换行。因为各种版本的 liunx 的各种版本对 echo 的移植性不好，所以引入了 printf,printf 可以说是 echo 的加强版，是由 POSIX 标准定义。