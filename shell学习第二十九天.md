## shell 学习第二十九天----循环

### 循环

bash shell 中主要提供了三种循环:for,while,until

**for 循环** 
 
for 循环的运作方式是将串行的元素取出, 依次放入指定的变量中, 然后重复执行在 do 和 done 之间的命令, 知道所有元素取尽为止.其中, 串行是一些字符串的组合, 彼此使用 $IFS 所定义的分隔符 (如空格符) 隔开, 这些字符串成为为字段.

语法：  
for 变量 in 串行 // 将串行中的字段迭代放入变量中  
do  
执行命令 // 重复执行, 知道串行中的每一个字段处理过为止.  
done  

案例：用 for 循环在 tmp 目录下创建 aaa1-aaa10，然后在 aaa1-aaa10 创建 bbb1-bbb10 的目录

```
\#!/bin/bash
mkdir hahaha
for k in $(seq 1 10)
do
        mkdir /tmp/hahaha/aaa${k}
        cd /tmp/hahaha/aaa${k}
        for j in $(seq 1 10)
        do
                mkdir bbb${j}
                cd /tmp/hahaha/aaa${k}
        done
        cd ..
done
```

说明：

- 行 3，seq 用于产生从某个数到另外一个数之间的所有整数。
- 行 5，在 tmp 目录下创建文件夹。
- 行 7，在使用一个 for 循环创建文件夹
 
案例二：列出 var 目录下各子目录占用磁盘空间的大小。

```
\#!/bin/bash
dir="/var"                                                           
cd $dir
for k in $(ls $dir)
do
        if [-d $k]
        then
                du -sh $k
        fi
done
```
 
说明：

- 行 4，对 /var 目录中每一个文件，进行 for 循环处理。
- 行 6，如果 /var 下的文件是目录，则使用 du -sh 计算该目录占用磁盘空间的大小。
 
**while 循环**

语法：  
while 条件测试  
do  
执行命令  
done  

说明：  

- 行 1 , 首先进行条件测试, 如果传回值为 0(条件测试为真), 则进入循环, 执行命令区域, 否则不进入循环
- 行 3, 执行命令区域, 这些命令中, 应该要有改变天剑测试的命令, 这样, 才有机会在有限步骤后结束之星 while 循环.
- 行 4, 回到行 1, 执行 while 命令.

案例：求 1 到 100 的和  

```
\#!/bin/bash
sum=0
i=1
while ["$i" -le "100"]
do
        sum=$(($sum+$i))
        i=$(($i+1))
done
echo "sum(1-100):" $sum
```

**until 循环**

语法:  
until 条件测试  
do  
执行命令  
done  

说明：

- 行 1, 如果条件测试结果为假 (传回值不为 0), 则进入循环
- 行 3, 执行命令区域. 这些命令中, 应该有改变条件测试的内容, 这样才不会出现死循环.
- 行 4, 会到行 1, 执行 until 命令

案例：计算 1-100 的和

```
\#!/bin/bash
sum=0
i=1
until ((i>100))
do
        sum=$(($sum+$i))
        i=$(($i+1))
done
echo $sum
```

分析：只要条件测试未超过 100，就进入循环，其他的和 while 类似。
 
其实 for 循环还有一种方式：

```
for((初始值； 条件； 执行步长))
do  程序段
done
```

**注意细节**：for((初始值； 条件； 执行步长)) 里面的预压和 c 语言一样了，但是一点不同双括号。
 
for 循环案例：列出指定目录下的所有文件

```
\#!bin/bash
read -p "Please enter the dir name:" dirname
for file in $(ls $dirname)
do
            echo $file
done
```

**复习一下 seq 命令**

seq 选项 参数

主要选项：

- `-s` 指定分隔符，默认是换行
- `-w` 等位补全，就是宽度相等，不足的前面补 0
- `-f` 格式化输出，就是指定打印的格式

案例：

使用命令：`seq 2`

输出：  
1  
2

使用： `seq -s “--” 2`  
输出 ：1--2  

案例： 
 
`[root@localhost tmp]# seq -f %05g 1 10`

00001  
00002  
00003  
00004  
00005  
00006  
00007  
00008  
00009  
00010  