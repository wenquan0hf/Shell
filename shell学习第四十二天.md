## shell 学习四十二天----使用 touch 更新文件时间

### 使用 touch 更新文件时间

`$ll new.txt`  
保证输出：ls：无法访问 new.txt：没有那个文件或目录  

```$touch new.txt  
$ll new.txt  
-rw-r--r-- 1 root root 0 7 月  12 16：56 new.txt
```
 
如果此文件已经存在的情况下。更改文件时间为当前时间  

```$touch new.txt  
-rw-r--r-- 1 root root 0 7 月  12 16：57 new.txt
```
 
案例：更改文件时间为指定时间  

`$date`

2015 年 07 月 12 日 星期日 16：59：10 CST

```$touch -t 11111111 new.txt
$ll new.txt
-rw-r--r-- 1 root root 0 11 月 11 2015 new.txt
```

分析：此处指定文件的时间格式为：yyyy(年)MM(月)DD(日)hh(时)mm(分)，省略在表示使用当前系统的时间。
 
案例：将文件改正与别的文件相同的时间

```$ll new.txt 
-rw-r--r-- 1 root root 0 7 月  12 17：03 new.txt
$ll /etc/passwd
-rw-r--r-- 1 root root 1804 6 月  10 23：27 /etc/passwd
$touch -r /etc/passwd new.txt
$ll new.txt 
-rw-r--r-- 1 root root 0 6 月  10 23：27 new.txt
```
 
总结：linux 中 `touch` 命令参数不常用，一般在使用 `make` 的时候可能会用到，用来修改文件时间戳，或者新建一个不存在的文件。
 
语法：`touch [-acdmt] 文件参数`

```$find /tmp -exec touch -t 11111111 {} \；
$ll /tmp
总用量 12
drwxr-xr-x 2 root root 4096 11 月 11 2015 hidden
-rw-r--r-- 1 root root    0 11 月 11 2015 new.txt
drwxr-xr-x 2 root root 4096 11 月 11 2015 test
-rwxr-xr-x 1 root root  385 11 月 11 2015 touch.sh
```

分析：可把 `/tmp` 下的所有文件和目录都改变修改时间。
 
主要选项和作用

```
参数
作用
-a
仅修改文件的最后访问时间
-c
仅修改时间，而不创建文件
-d
后面可以接日期，也可以使用 -date=” 如期或时间”
-m
仅修改文件的修改时间
-t
后面可接时间，格式为 [yyyyMMDDhhmm]
```