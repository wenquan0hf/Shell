## shell 学习第二十天----sort 的其他内容以及 uniq 命令

### sort 的其他内容以及 uniq 命令

在排序算法里有个重要的问题: 是否稳定? 这个问题指的是: 相同的记录输入顺序是否在输出时也可保持原状? 当你以多键值为记录进行排序，或是以管道处理时，排序稳定性就非常重要了。我们先来验证一下。

```
sort -t_ -k1,1 -k2,2 <<EOF
\> one_two
\> one_two_three
\> one_two_four
\> one_two_five
\>EOF
```

输出为:

```
one_two
one_two_five
one_two_four
one_two_three
```

每条记录内的排序字段都相同，但是输出与输入不一致，所以我们说 sort 不是稳定的排序。幸好: 我们可以通过--stable 选项补救这个问题，设置此选项，就会稳定了。

`sort --stable -t_ -k1,1 -k2,2 << EOF`

输出为:

```
one_two
one_two_three
one_two_four
one_two_five
```

`sort` 命令的重要性绝对能在 linux 中拍到前十。
 
### 删除重复

有时，将数据流里连续重复的记录删除是必要的。使用 `sort -u` 的消除操作十一局匹配的键值，而非匹配的记录。

`uniq` 命令提供了另外一种过滤数据的方式: 常用于管道中，用来删除已使用 sort 排序完成的重复记录。

```
sort ... | uniq | ...  
uniq [OPTION]… [INPUT [OUTPUT]]
```

从文件中去除或删除重复的行，在功能上和 `sort -u` 类似。  

常用选项：

- `-u`: 只显示不重复的行
- `-d`: 只显示重复的行
- `-c`: 打印每一行出现的次数
- `-fn`: 忽略前 n 个域

例如: 我有一个文件:unip.txt

```
tres
unus
duo
tres
duo
tres
```

使用 `sort uniq.txt |uniq` 命令显示唯一的，排序后的记录，重复则仅取唯一行。

输出为:

```
duo
tres
unus
```

使用 `sort uniq.txt | uniq -c` 命令计数唯一的，排序后的记录，说白了就是统计各行文本出现的次数

```
2 duo
3 tres
1 unus
```

代表的意思是说 duo 出现了两次，tres 出现了三次，unus 出现了一次。

使用 `sort uniq.txt | uniq -d` 命令仅显示重复的记录

输出结果为:
```
duo
tres
```

使用 `sort uniq.txt | uniq -u` 命令仅显示未重复的记录

输出结果为:unus
 
- 并集：`cat file1.txt file2.txt | sort | uniq > file.txt`
- 交集：`cat file1.txt file2.txt | sort | uniq -d >file.txt`
- 差集：求 file1.txt 相对于 file2.txt 的差集，可先求出两者的交集 temp.txt，然后在 file1.txt 中除去 temp.txt 即可。  
`cat file1.txt file2.txt | sort | uniq -d >temp.txt`  
`cat file1.txt temp.txt | sort | uniq -u >file.txt`
 
### uniq 到底是干嘛用的?

文本中的重复行，基本上不是我们所要的，所以就要去除掉。linux 下有其他命令可以去除重复行，但是我觉得 uniq 还是比较方便的一个。使用 uniq 的时候要注意以下二点

1. 对文本操作时，它一般会和 `sort` 命令进行组合使用，因为 uniq 不会检查重复的行，除非它们是相邻的行。如果您想先对输入排序，使用 `sort -u`。
2. 对文本操作时，若域中为先空字符 (通常包括空格以及制表符)，然后非空字符，域中字符前的空字符将被跳过
 
uniq 的所有选项:  

- `-c`, --count              // 在每行前加上表示相应行目出现次数的前缀编号
- `-d`, --repeated          // 只输出重复的行
- `-D`, --all-repeated      // 只输出重复的行，不过有几行输出几行
- `-f`, --skip-fields=N     //-f 忽略的段数，-f 1 忽略第一段
- `-i`, --ignore-case       // 不区分大小写
- `-s`, --skip-chars=N      // 根 -f 有点像，不过 -s 是忽略，后面多少个字符 -s 5 就忽略后面 5 个字符
- `-u`, --unique            // 去除重复的后，全部显示出来，根 mysql 的 distinct 功能上有点像
- `-z`, --zero-terminated   end lines with 0 byte, not newline
- `-w`, --check-chars=N      // 对每行第 N 个字符以后的内容不作对照
- `--help`              // 显示此帮助信息并退出
- `--version`              // 显示版本信息并退出