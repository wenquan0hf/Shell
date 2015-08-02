## shell 学习四十四天----寻找文件

**寻找文件**

**快速寻找文件**

`locate` 将文件系统里的所有文件名压缩成数据库，以迅速找到匹配类 shell 通配字符的文件名，不必实际查找整个庞大的目录结构，这个数据库，通常是在半夜通过 `cron`，在具有权限的工作中执行 `updatedb` 建立。`locate` 对用户来说有其必要性，它可以回答用户：系统管理者究竟将要查找的 syx 放在何处?

locate syx 缺乏通配字符模式时，`locate` 汇报稿含有将参数作为子字符串的文件；这里找到 48 个匹配的文件。
 
由此可见 `locate` 的输出量可能极大，他通常会通过管道丢给分页程序 (pager)，如 less；或是查找过滤程序，例如 grep 处理：
`$locate syx | grep syx5`  

通配字符模式续杯保护，以避免 shell 展开，这么一来 `locate` 才能处理他们：`$locate '*gcc-3.3*.tar*'`  #在 locate 里，使用通配字符匹配，以寻找 `gcc-3.3`
 
注意：`locate` 或许不舍用于所有站点，因为它会将被限制访问的目录下的文件名泄露给用户。如果考虑到这一点，只需要简单的将 `updatedb` 的操作交给一般用户权限执行：这么一来，不合法的用户便无从得知原本就不该让它知道的文件名了。不过比较好的方式是使用 `secure locate` 包：`slocate`，他也会将文件的保护与所有权存储在数据库里，但只显示用户可以访问的文件名。
 
`updatedb` 提供选项，课件里文件系统里选定位置的 `locate` 数据库，例如用户的根目录树状结构，所以 `locate` 可用作个人文件的查询。
 
**寻找命令存储位置**

偶尔你也可能会想知道，调用一个没有路径的命令时，他再文件系统的位置如何。此时可以使用 `type` 命令

```
$type gcc #gcc 在哪?
$type type #type 是什么?
$type newgcc #newgcc 是什么?
$type mypwd #mypwd 是什么?
$type hahaha #这个不存在的命令是什么?
-bash：type：hahaha：not found
```
 
注意：type 是内部 `shell` 命令，所以它认得别名与函数。

假定你想选择大于某个大小的文件，或是三天前修改的文件，属于你的文件，或者拥有三个或三个以上直接链接的文件，此时就会用到 `find` 命令。
 
如果你需要在整个目录树状结构分支里绕来绕去去寻找某个东西，`find` 可以帮助你完成此工作。
 
**find 命令**

语法：  
`find [files-or-directories] [options] `
 
用途：  
寻找与制定名称模式匹配于或具有给定属性的文件
  
**主要选项**

```  
选项  
用途
-atime n
选定 n 天前访问的文件
-ctime n
选定 n 天前改过 inode 的文件
-follow 
接着符号性连接
-group g
选定组 g 内的文件 (g 为用户组 ID 名称或数字)
-links n
选定拥有 n 个直接链接的文件
ls
产生类似 ls 冗长形式的列表，而不知有文件名。
-mtime n
选定 n 天前修改过的文件
-name ‘pattern’
选定文件名与 shell 通配字符模式匹配的文件 (通配字符模式会使用括号框起来，可避免 shell 解释)
-perm mask
选定与制定八进制权限掩码匹配的文件
-prune
不想下递归目录树状结构里
-size n
选择大小为 n 的文件
-type t
选定类型 t 的文件，类型施丹旖字母：d 为目录，l 为符号性连接。
-user u
选定用户 u 拥有的文件 (u 为用户的 ID 名称或编号)
```
 
行为：
  
- `find` 向下深入目录树状结构，寻找所有在这些目录树下的文件。接下来，应用其命令行选项所定义的选定器，选择文件攻进一步操作，通常是显示他们的名称，或产生类似 `ls` 的冗长列出。
- `find` 不同于 `ls` 与 shell 的地方时：他没有隐藏文件的概念，也就是说：就算是点号开头的文件名，`find` 还是能找到他。
- 另一点不同于 `ls` 的地方时：当 `find` 处理的是目录时，他会自动递归深入目录结构，需找在那之下的任何东西，除非你使用 `-prune` 选项要求不这么做。
- 当 `find` 找到文件，他会先执行命令行选项所设置的选项限制，如果这些测试成功，则将名称交给内部的操作程序处理。默认操作是将名称打印在标准输出上，不过如果使用 `-exec` 选项，则可提供命令模板，在其中名称可以被替换，并再执行该命令。
- 在选定的文件上自动执行命令是很强的功能。单页极度危险。如果该命令具有破坏性，那么最好是让 `find` 先将列表产生在临时性文件中，再有可胜任的人小心的确认，决定是否将命令进一步自动化处理。
- 使用 `find` 进行破坏性目的的 shell 脚本，在编写时必须格外小心，之后，也必须彻底执行调试，例如在破坏性命令开始前插入 `echo`，这么一来你可以看看会有哪些操作，而不必真的执行它。

案例：

```
$touch MybashProgram.sh mycprogram.c MyCProgram.c Program.c 
$mkdir backup
$cd backup
$touch MybashProgram.sh mycprogram.c MyCProgram.c Program.c 
```
 
1. 查找文件  
```$find -name MyCProgram.c(保证不再 backup 目录下)
./tmp/MyCProgram.c
./tmp/backup/MyCProgram.c
```
上面的命令展示了，在当前目录下查询文件名为 `MyCProgram.c` 的文件。如果要指定查找目录，例如从根目录开始查找文件名为 `MyCProgram.c` 的文件：
```$ find / -name MyCProgram.c 
/tmp/MyCProgram.c
/tmp/backup/MyCProgram.c
```

2. 查找文件，忽略文件名的大小写  
```$ find -iname mycprogram.c 
./mycprogram.c
./MyCProgram.c
./backup/mycprogram.c
./backup/MyCProgram.c
```   
3. 指定搜索目录的深度
 
	- 在 root 目录及其子目录下查找 passwd 文件：  
```$find / -name passwd
/etc/passwd
/etc/pam.d/passwd
/usr/bin/passwd
```  
	- 在 root 目录及其一层深的子目录中查找：     
```passwd $find / -maxdepth 2 -name passwd
/etc/passwd```
	- 在第二层子目录和第四层子目录之间查找 psswd 文件：  
```$find / -mindepth 3 -maxdepth 5 -name passwd
/etc/pam.d/passwd
/usr/bin/passwd```  
 
4. 相反匹配  
显示所有名字不是 `MyCProgram.c` 的文件或者目录。由于 `maxdepth` 是 1，所以只会显示当前目录下的文件和目录：  
```$find -maxdepth 1 -not -iname "mycprogram.c"
.
./MybashProgram.sh
./create_sample_files.sh
./backup
./Program.c
```

5. 对查找到的文件执行特定的命令  
对当前目录及其子目录下文件名为“test.c”，文件名不区分大小写执行`'rm(删除)'`操作：  
`$find -iname test.c -exec rm {} \；`
 
6. 查找空文件  
	- 查找当前目录及其子目录下的所有空文件：
`$find -empty`
	- 查找当前目录 (不包括其子目录) 下的空文件：
`$find -maxdepth 1 -empty`  
 
7. 根据权限查找文件  
文件权限在 linux 系统是一个很重要的概念，新建两个文件：test.c，test1.c，权限修改为如下：  
```----r----- 1 root root      0 7 月  12 20：30 test1.c
-rw------- 1 root root      0 7 月  12 20：30 test.c
```

	- 查找只有文件所有者有读写权限的文件：
`$find -perm 600`
	- 查找与文件所有者同用户组的用户具有读权限的文件：
`$find -perm 040`

8. 查找制定类型的文件  
	- 查找 socket 文件：
`$find /tmp -type s`
	- 查找目录：
`find /tmp -type d` 
	- 查找普通文件：
`find /tmp -type f`
	- 查找隐藏文件：
`$find /tmp -type s -name ".*"`
	- 查找隐藏目录：
`$find /tmp -type d -name ".*"`
 
**寻找问题文件**

我们注意到包含特殊字符 (如换行字符) 的文件名有点麻烦。`find` 具有 `-print0` 选项，以显示文件名为 `nul` 终结的字符串。由于路径名称可包含任何字符，除了 `NUl` 以外，所以这个选项，可产生能够被清楚解析的文件名列表。

详细资料：参考
[http：//www.jb51.net/LINUXjishu/205761.html](http：//www.jb51.net/LINUXjishu/205761.html)