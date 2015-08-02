## shell 学习第十一天----sed 正则的精确控制

有多少文本会改动，在使用 sed 的时候我们来看这么两个问题: 第一个问题是有多少人文本会匹配，第二个问题是从哪里开始匹配。
  
回答是: 正则表达式可以匹配整个表达式的输入文本中最长的，最左边的子字符串。除此之外，匹配的空 (null) 字符串，则被认为是比完全不匹配的还长。  

- `echo syx is a good body | sed 's/syx/zsf/'`   使用固定字符串
- `sed` 可以使用完整的正则表达式。但是应该知道” 从最长的最左边” 规则的重要性。
- `echo Tolstoy is worldly | sed 's/T.*y/Camus/' `   
- `Camus`
  
很明显，我们只想要匹配 Tolstoy，但是由于匹配会扩展到可能的最长长度的文本量，所以出现了这样的结果。
 
这就需要我们精确定义：

`echo Tolstoy is worldly | sed 's/T[[:alpha:]]*y/Camus/'`  
`Camus is worldly`

在文本查找中，有事喊可能会匹配到 null 字符串，而在执行文本替代时，也允许插入文本。

```
ehco abc | sed 's/b*/l/'
labc
ehco abc | sed 's/b*/l/g'
lalcl
```

请留意，b*shi 如何匹配 abc 的前面与结尾的 null 字符串。