## shell 学习第二十五天----神器的管道符

### 神器的管道符

一、 从结构化文本文件中提取数据

`1.sed -e 's=/.*=='   去掉第一个 / 和后面的所有字符`

`jones:*:32713:899:Adrian W. Jones/OSD211/555-0123:/home/jones:/bin/ksh`

输出为 jones:*:32713:899:Adrian W. Jones


`| -e 's=^[:]∗:.∗ []∗=\1:\3, \2='`

- ^[:]∗  匹配用户名称字段
- :.∗  匹配文字到空白处 () 后面有个空格)
- []∗ 匹配记录里剩下的非空白文字


输出 jones:Jones, *:32713:899:Adrian W.

`sed -e 's=^[:]∗:[^/]*/[/]∗.*$=\1:\2=' passwd1`

得到 jones:OSD211

`sed -e 's=^[:]∗:[^/]*/[/]∗.*$=\1:\2=' passwd1`

得到 jones:OSD211
 
二、 字解谜

`cat file | tr A-Z a-z | tr -c a-z\''\n' | sort -u`

1. `tr A-Z a-z`  转换成小写
2. `tr -c a-z\''\n'`
3. `sort -u` 去除重复的
 
三、 标签列表

1. `sed -e 's#systemitem *role="url"#URL#g'`
2. `tr '(){}[]'  '\n\n\n\n\n\n\n'`