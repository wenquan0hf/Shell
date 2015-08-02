## shell 学习第十五天----join 连接字段

### 使用 join 连接字段

join 命令将多个文件结合起来，每个人建立的每条记录，都共享一个键值，键值指的是记录中的珠子段，通常会是用户名称，个人形式，员工编号之类的数据。

语法：

`join [options...] file1 file2`

主要选项

- -1 field1
- -2 field2  
标明要结合的字段。   -1 field 指的是从 file1 取出 field1，而 -2field2 指的则为从 file2 取出 field2。 字段编号自 1 开始，而非 0。
- -o file.field  
输出 file 文件中的 field 字段。 一般的字段则不打印。 除非使用多个 -o 选项，即可显示多个输出字段。
- -t separator  
使用 separator 作为输入字段分割字符，而非使用空白。 次字符也为输出的字段分割字符。

### 行为模式

读取 file1 与 file2，并根据共同键值结合多笔记录。默认以空白分隔字段。输出结果则包括共同键值，来自 file1 的其余记录，接着 file2 的其余记录 (指除了键值外的记录)。若 file1 位 -，则 join 会读取标准输入，每个文件的第一个字段是用来结合的默认键值; 可以使用 -1 与 -2 更改键值。默认情况下，在两个文件中未含键值的行将不打印。

Linux join 命令用于将两个文件中，指定栏位内容相同的行连接起来。

找出两个文件中，指定栏位内容相同的行，并加以合并，再输出到标准输出设备。

例如： 

我有两个文件： 文件 aa 和文件 bb  
aa 的内容为：  
joe 100  
jane 200  
herman 300  
chris 400
bb 的内容为：  
joe 20  
jane  10  
herman 30  
chris 98  
 
每条记录都有两个字段：业务员的名字和销售量。为了让 join 运行得到正确结果，输入文件必须先完成排序.
 
编写下列脚本：

```
\#!/bin/sh
\#jointest.sh
\#删除注释并排序数据文件
sed ‘/^#/d’ aa | sort > aa.sorted
sed ‘/^#/d’ bb | sort > bb.sorted
\#以第一个键值做结合，将结果产生至标准输出
join aa.sorted bb.sorted
\#删除缓存文件
rm aa.sorted bb.sorted
保存退出
chmod +x jointest.sh
./jointest.sh
```

输出结果如下所示：  
chris 400 98  
herman 300 30  
jane 200 10  
joe 100 20  

首先使用 sed 删除注释，然后再排序个别文件。排序后的缓存缓存文件称为 join 命令的输入数据，最后删除缓存文件.sed 的删除还记得吗? 
 
`sed '/^#/d' bb`

这里的意思是说把 bb 文件里以#开头的行删除


