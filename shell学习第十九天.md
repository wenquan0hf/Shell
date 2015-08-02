## shell 学习第十九天----文本块排序

### 文本块排序

文本快排序出现的原因: 有时，你会需要将多行记录组合而成的数据排序。 地址清单就是一个很好的例子，为了方便阅读，地址记录经常会切断,以一个或数个空行批次隔开，像这种数据，没有一定的排序键值位置可供 `-k` 选项使用，所以就引入了文本快排序。

**案例**:

我有一个文件 adress.txt，内容为:

```
J Luo
Southeast University
Nanjing,China
\ 
Y Zhang
Victory University
Melbourne，Australia
\
D Hou
Beijing University
Beijing,China
\
B Liu
Shanghai Jiaotong University
Shanghai,China
\
C Lin
University of Toronto
Toronto,Canada
```

要求：对文本块根据学校的名字 (每个文本块的第二行) 进行排序，结果仍然能以文本块的格式输出。

`awk '{a[$2]=$0}END{for(i=1;i<=asorti(a,b);i++)print a[b[i]]}' ORS='\n\n' RS= FS='\n' adress.txt` 这一种方法效率高，各种牛逼，看不明白十格什么 JB 意思。

第二种方式:`awk 'BEGIN{FS="\n";RS=""}{print $1":"$2":"$3":"}' adress.txt|sort -t ":" -k2|tr ":" "\n"`，这种方式貌似比较平民，适合屌丝玩家。 那到底是什么意思呢?

首先使用 `awk` 命令将文本块转化成以下这样:

J Luo:Southeast University:Nanjing,China  
Y Zhang:Victory University:Melbourne，Australia  
D Hou:Beijing University:Beijing,China  
B Liu:Shanghai Jiaotong University:Shanghai,China  
C Lin:University of Toronto:Toronto,Canada  

然后使用 `sort` 命令按照学校 (也就是原文本的第二行) 排序。 排序后的结果为:

D Hou:Beijing University:Beijing,China  
B Liu:Shanghai Jiaotong University:Shanghai,China  
J Luo:Southeast University:Nanjing,China  
C Lin:University of Toronto:Toronto,Canada  
Y Zhang:Victory University:Melbourne，Australia  
 
最后使用 `tr “:” “\n”` 命令，将排序后的文本转化回来。  

`awk` 的 `FS`: 输入字段分隔符（缺省为`space`），相当于 `-F` 选项  
`awk -F ':' '{print}' shcool.txt` 和 `awk 'BEGIN{FS=":"}{print}' shcool.txt` 是一样的
 
RS：输入记录分隔符，缺省为 `"\n"` 缺省情况下，`awk` 把一行看作一个记录；如果设置了 RS，那么 `awk` 按照`RS` 来分割记录，此处的意思是说将原文本看成是一条记录。

例如，如果文件 c，cat c 为

`hello world; I want to go swimming tomorrow;hiahia`

运行 `awk 'BEGIN{RS =";"} {print}' c` 的结果为

```
hello world
I want to go swimming tomorrow
hiahia
```

合理的使用 `RS` 和 `FS` 可以使得 `awk` 处理更多模式的文档，例如可以一次处理多行，例如文档 `d ,cat d` 的输出为

```
1 2
3 4 5
\ 
6 7
8 9 10
11 12
\
hello
```

每个记录使用空行分割，每个字段使用换行符分割，这样的 `awk` 也很好写

`awk 'BEGIN{FS ="\n"; RS =""} {print NF}' d` 输出

```
2
3
1
```

而 `tr` 的意思是替换，将`":"` 替换成`"\n"`。