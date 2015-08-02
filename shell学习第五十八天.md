## shell 学习五十八天----/proc 文件系统

### /proc 文件系统

前言：linux 中的 `/proc` 文件系统，由一组目录和文件组成，挂载 `(mount)` 与 `/proc` 目录下。
`/proc` 文件系统是一种虚拟文件系统，以文件系统目录和文件形式，提供一个指向内核数据结构的接口。 这为查看和改变各种系统属性开启了方便之门。 此外，还能通过一组以 `/proc/PID` 形式命名的目录 (PID 是进程的 ID) 查看系统汇总运行各进程的相关信息。
 
通常，`/proc` 目录下的文件内容都采取可读的文本形式，shell 脚本也能对其进行解析。 程序可以打开，读取和写入 `/proc` 目录下的既定文件。 大多数情况下，只有特权进程才能修改 `/proc` 目录下的文件内容。
 
1. proc 文件系统初步

	- `/proc` 文件系统
	`/proc` 文件系统是一种特殊的，由软件创建的文件系统，内核使用它向外界到处信息。 `/proc` 下面的每个文件都绑定一个内核文件，用户读取其中的文件时，该函数动态的生成文件的“内容”。

		由于 `/proc` 文件系统已经被添加了大量的信息。因此，最好的办法是使用 `sysfs` 而不是 `/proc` 文件系统想歪导出信息。

		`/proc` 文件不仅可以用于读数据，也可以用于写数据，不过写数据比较麻烦一些，这里只描述数据的用法。 写数据的方法可以在看完读数据的过程后参考 kernel 源码
 
	- 创建 `/proc` 文件的函数
		前面说了 `/proc` 下的文件都是在访问实时生成文件内容的，那么为了创建 `/proc` 下的一个只读的文件，我们必须实现一个函数用于在读取文件时生成数据，万幸，该函数接口设计好了，我们只要按照函数接口实现自己需要的功能就可以了。 函数原型如下：  
		```int (*read_proc)(char *page，char **start，off_t offset，int count，int *eof，void *data);```
		
		参数说明：
		```参数名
		说明
		page
		用来写入数据的缓冲区; 也就是说从 /proc 文件中独到的数据都写入到 page 指向的缓冲区中
		start
		用于指定事迹的数据写入到 page 指向的内存也的具体的那个位置
		offset
		和 read 函数中的参数意义相同
		count
		和 read 函数中的参数意义相同
		eof
		当没有数据返回时，必须设置该参数为一个整数，例如:*eof=1;
		data
		该参数是内核提供给驱动程序的专用指针，可以用于内部记录```
 
		**创建制度的 `/proc` 文件的函数**
		```struct proc_dir_entry *create_proc_read_entry(const char *name，mode_t mode，struct proc_dir_entry *base，read_proc_t *read_proc，void * data)``` 
		参数说明：

		```参数名
		说明
		name
		要创建 `/proc` 下的文件名
		mode
		创建的文件权限的掩码，若为 0，则使用系统默认的权限
		base
		该文件所在的父目录，若该参数为 null，则该文件将会被创建在 /proc 的根目录下
		read_proc
		读取 /proc 下的文件时调用的函数，也就是前面讲解的那个函数
		data
		内核会忽略 date，但会把该参数传递给 read_proc 函数```
		**删除 /proc 系统文件的函数**：
		`void remove_proc_entry(const char *name，struct proc_dir_entry *parent)`
		
		参数说明：
		
		```参数名
		说明
		name
		在 /proc 文件系统中创建的文件名
		parent
		父目录名```

	- 使用 `/proc` 文件系统的缺点

		1. 删除调用可能在 `/proc` 文件系统的文件正在被使用时发生
		2. 同一个文件名可能注册两次，这将会发生错误
 
 
2. 创建简单的 `/proc` 文件

```\#cd /proc ; vi read_proc  //read_proc 的内容如下:
\#include <linux/kernel.h>
\#include <linux/init.h>
\#include <linux/module.h>
\#include <linux/proc_fs.h>
\ 
int read_proc(char *page，char **start，off_t offset，int count，int *eof，void *data);
\ 
static int __init test_proc_init(void)
{
    create_proc_read_entry("read_proc"，0，NULL，read_proc，NULL);
    return 0;
}
\ 
static void __exit test_proc_exit(void)
{
    remove_proc_entry("read_proc"，NULL);
}
\ 
int read_proc(char *page，char **start，off_t offset，int count，int *eof，void *data)
{
    int len = sprintf(page，"%s\n"，"hello world");
    return len;
}
\ 
module_init(test_proc_init);
module_exit(test_proc_exit);
\ 
MODULE_LICENSE("GPL");
MODULE_AUTHOR("wangxq");
\ 
\#cat /proc/read_proc
hello world
```

**/proc 目录的应用**

对此文件系统的访问同一般文件相同。

例：

1. 统计 cpu 个数：  
`cat /proc/cpuinfo | grep'physical id'|uniq -c|wc –l`
2. cpu 型号  
`cat /proc/cpuinfo|grepname|cut -f2 -d:|uniq`
3. 计算每个 cpu 的内核数  
```cat /proc/cpuinfo | grep'physical id'|awk -F':' '{count[$2]++;}END{sum=0;for(a in count){cc++;sum+=count[a]}printsum/cc;}'```
4. 内核版本  
`cat /proc/version|cut-f1 -d'('`    
5. 内核执行的上下文转换次数  
`cat /proc/stat|grep ctxt|awk'{print $2}'`
6. 系统创建的进程数  
`cat /proc/stat|grep processes|awk'{print $2}'`
7. 当前可用的内存数量  
`cat /proc/meminfo|grep MemFree`