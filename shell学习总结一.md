## shell 学习总结一

### 本章小结

变量在正是一点的程序里是必备项目。shell 的变量会保留字符串值，而大量的运算符可以在 `${var...}` 里使用，让你控制变量的结果。
 
shell 提供了许多的特殊变量 (例如 `#?` 与 `$!`)，用来访问特殊信息，例如，命令退出状态。shell 也有许多预定义的特殊变量，例如 `PS1`----用来设置主要提示符。位置参数与 `$*` 和 `$@` 这类的特殊变量，则用来在脚本 (或函数) 被引用是，让用户可以访问被使用的参数。`env`，`export` 以及 `readonly` 则用来控制环境。
 
`$((...))` 的算术展开提供完整的算术运算能力，且使用与 C 相同的运算符与优先级。
 
程序的退出状态是一个小的整数，可以在程序完成后，攻饮用者使用；shell 脚本使用 `exit` 命令来做这件事，而 shell 函数则使用 `return` 命令。shell 脚本可以取得在特殊变量 `$?` 内执行的最后一个命令的退出状态。
 
退出状态可以搭配 if，while 与 until 语句来进行流程控制，也可以与`!`，`&&`，以及 `||` 运算符搭配使用。
 
`test` 命令及其别名 `[...]`，可测试文件属性和字符串与数值，在 `if`，`while` 以及 `until` 语句里，他也相当有用。
 
`for` 提供遍历整组值的的循环机制，这整组的值可以是字符串，文件名或其他等等。`while` 与 `until` 提供比较传统的循环方式，加上 `break` 和 `continue` 提供额外的循环控制。`case` 语句提供一个多重比较功能，类似 C 与 C++ 里面的 `switch` 语句。

`getopts`，`shift` 与 `$#` 提供处理命令行的工具。
 
最后 shell 函数可将相关命令组织到一起，之后再将它视为一个单独调用使用。他们有点像 shell 脚本，只不过他将命令存放在内存里，这样更有效率，且他们还能影响引用脚本的变量与状态。