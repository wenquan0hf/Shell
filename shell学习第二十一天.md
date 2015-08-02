## shell 学习第二十一天----重新格式化段落

### 重新格式化段落

大部分功能强大的文本编辑器都提供重新格式化段落的命令；供用户切分段落，使文本行数不要超出我们看到的屏幕范围。这样我们就引入了 `fmt` 命令，虽然一些 `fmt` 的实现有较多的选项可用，但其实只用:`-s` 仅切割较长的行，但不会将短行结合成较长的行，而 `-w n` 则设置输出行宽度为 n 个字符 (默认通常是 75 个)。

fmt 命令的语法:

`fmt [option] [file-list]`

fmt 通过将所有非空白的长度设置为几乎相同，来进行简单的文本格式化。

- `-s`   截断长行，但不合并
- `-t`   除每个段落的第 1 行外都缩进
- `-u`   改变格式化，使字之间出现一个空格，句子之间出现两个空格
- `-w n` 将输出的行宽改为 n 个字符。不带该选项时，fmt 输出的行宽度为 75 个字符

例如，我有一个文件 demo，内容为:

```
A long time ago, there was a huge apple tree.A little boy loved to come and play around it every day. He climbed to the tree top, ate the apples, took a nap under the shadow… He loved the tree and the tree loved to play with him. 
```

使用命令 `fmt -s demo`，输出为:

```
A long time ago, there was a huge apple tree.A little boy loved
to come and play around it every day. He climbed to the tree top, ate
the apples, took a nap under the shadow… He loved the tree and the
tree loved to play with him.
```

该命令的含义是节段 2 长行。
 
使用 `fmt -t demo` 命令的意思是说排除首行的缩进，结果为:

```
A long time ago, there was a huge apple tree.         A little boy loved
   to come and play around it every day. He climbed to the tree top,
   ate the apples, took a nap under the shadow… He loved the tree and
   the tree loved to play with him.
```
 
使用 `fmt -u demo` 命令的意思是说格式化单词和句子的间隔。输出为:

```
A long time ago, there was a huge apple tree.  A little boy loved to come
and play around it every day. He climbed to the tree top, ate the apples,
took a nap under the shadow… He loved the tree and the tree loved to
play with him.
```

显然 A little boy 前面的多个空格变成了两个。
 
使用命令 `fmt -w 40 demo` 意思是说指定行的宽度，这里的行宽为 40 个字符。所以输出为:

```
A long time ago, there was a huge
apple tree.         A little boy
loved to come and play around it
every day. He climbed to the tree top,
ate the apples, took a nap under the
shadow… He loved the tree and the
tree loved to play with him.
```
 
仅作切割的选项： `-s`，在你想将长的行绕回，短的行保持不动时很好用，这么做也能使结果与原始版本间的差异达到最小，例如:

```
fmt -s -w 10 << EOF
one two three four five
six
seven
eight
输出为:
one two
three
four five
six
seven
eight
```

fmt 的小案例:

下面以拼音字典为例：

字典文件：`/usr/dict/words` 或者 `/usr/share/dict/words`。

- `sed -n -e 9991,10010p /usr/share/dict/words | fmt`
- `sed -n -e 9991,10010p /usr/share/dict/words | fmt -w 30`

观察上面两行命令的输出。

复习一下 `sed` 命令:

sed 是一个很好的文件处理工具，本身是一个管道命令，主要是以行为单位进行处理，可以将数据行进行替换、删除、新增、选取等特定工作。假设我们有一个文件 demo

```
sed '1d' demo    删除第一行
sed -n '1p' 显示第一行
sed -n '/root/p' demo 查询包括关键字 root 所在所有行
sed '1a hahaha' demo  第一行后增加字符串 hahaha
sed '1,3a hahaha' demo 第一行到第三行后增加字符串 hahaha
sed '1c hihihi' demo  第一行代替为 hihihi
sed '1,2c hihihi' demo  第一行和第二行替换为 hihihi
```

替换一行中的某部分

```
格式：sed 's/ 要替换的字符串 / 新的字符串 /g'   （要替换的字符串可以用正则表达式），案例:
sed 's/root/hahaha/g'  替换 root 为 hahaha
sed 's/root//g' 删除 root 
sed -i '$a bye' ab         #在文件 ab 中最后一行直接输入 "bye"
```
