## shell 学习第十三天----sed 案例分析

### sed 的使用案例

使用 `sed` 操作 `/etc/passwd`，最好复制一份 (`cp /etc/passwd /tmp`)，操作 `tmp` 下的 `passwd`(其实不用，因为在一般情况下 `sed` 只是修改了输出结果，不会改变文件本身，除非要求这么做)。
 
### 以行为单位的新增/删除

- 要求： 将 `/etc/passwd` 的内容列出并且列印行号，同时删除 2~5 行。
- 做法：`cat /etc/passwd | sed '2,5d'`

sed 的动作是`'2,5d'`(动作需要放在单引号之间)。nl 命令在 linux 系统中用来计算文件中行号。nl 可以将输出的文件内容自动的加上行号！其默认的结果与 `cat -n` 有点不太一样，nl 可以将行号做比较多的显示设计，包括位数与是否自动补齐 0 等等的功能。 

只删除第二行

`nl /etc/passwd | sed '2d'`
 
删除第 3 行到最后一行

`cat -n /etc/passwd | sed '3,$d'` 
 
在第二行后 (就是在第三行) 加上"i am fine" 字样

`cat -n /etc/passwd | sed '2a i am fine'`
 
如果要在第二行前面

`nl /etc/passwd | sed '2i i am fine'`
 
如果是要增加两行以上，在第二行后面加入两行字，例如[hello]与[how are you]

```
nl /etc/passwd | sed '2a hello\
\>how are you’
```

每一行之间都必须要以反斜杠 (\) 来进行新行的添加，所以上面的例子，我们可以发现在第一行的最后面就有 \ 存在。(再输入的是会需要注意，单引号不要一起输完)。
 
以行为单位的替换与现实

将第 2-5 行的内容替换成"hahaha" `nl /etc/passwd | sed '2,5c hahaha' `，通过这个方法，我们就可以替换整行数据了。  
 
仅列出 `/etc/passwd` 文件的 5-7 行 `cat -n /etc/passwd | sed -n '5,7p'`，可以透过这个 sed 的以行为单位的显示功能， 就能够将某一个文件内的某些行号选择出来显示。  
 
 
### 数据的搜寻与显示
 
搜索 `/etc/passwd` 中有关 /root 关键字的行  
`nl /etc/passwd | sed '/root/p'`

**思考： 为什么会输出所有行的情况?**

使用 -n 的时候将只打印包含模板的行。

`nl /etc/passwd | sed -n '/root/p'`
 
### 数据的搜索与删除

删除 `/etc/passwd` 所有包含 root 的行，其他行输出

`nl /etc/passwd | sed '/root/d' `
 
### 数据的搜索并执行命令

搜索 `/etc/passwd`，找到 root 对应的行，执行后面花括号中的一组命令，每个命令之间用分号分隔，这里把 bash 替换为 blueshell，再输出这行：

 `nl /etc/passwd | sed -n '/root/{s/bash/blueshell/;p}'`

如果只替换 /etc/passwd 的第一个 bash 关键字为 blueshell，就退出

`nl /etc/passwd | sed -n '/bash/{s/bash/blueshell/;p;q}'    1` 
 
最后的 q 是退出。
 
### 数据的搜索并替换

除了整行的处理模式之外，sed 还可以用行为单位进行部分数据的搜寻并替换。 基本上 sed 的搜寻与替换与 vi 相当的类似。
sed 's/ 要被取代的字符串 / 新的字符串 /g'
 
先通过 `/sbin/ifconfig eth0` 查看本机的 IP 地址，我的是 (192.168.199.5) 
 
将 IP 前面部分予以删除  

`/sbin/ifconfig eth0 | grep 'inet addr'|sed 's/^.*addr：//g'`  

将 IP 后面部分予以删除

`/sbin/ifconfig eth0 | grep 'inet addr'|sed 's/^.*addr：//g' | sed 's/Bcast.*$//g'`
  
即可得到 IP
 
### 多点编辑

一条 sed 命令，删除 `/etc/passwd` 第三行到末尾的数据，并把 bash 替换成 hahaha。

`nl /etc/passwd | sed -e '3,$d' -e 's/bash/hahaha/g'`

**注意**： 每天命令前面都加入了 -e 选项
 
 
### 直接修改文件内容

最好别使用，如果使用需要加入一个 -i 选项   
例如在最后一行插入 hahaha，`nl /etc/passwd | sed -i '$i hahaha'`