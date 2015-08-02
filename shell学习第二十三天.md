## shell 学习第二十三天----打印

如果希望打印文件，最好预先处理一下，包括调整边距，设置行高，设置标题等，这样打印出来的文件更加美观。当然，不处理也能打印，但是可能会比较丑陋。

**pr 命令**

pr 命令就是转换文件格式的，可以把较大的文件分割成多个页面进行打印，并未每个页面添加标题。

语法:

`pr option(s) filename(s)`

`pr` 命令仅仅改变在屏幕上的输出样式，不改变文件本身，和 sed 有点类似。常见选项如下:

- `-k`：分成激烈打印，默认为 1。
- `-d`：两倍行距 (并不是所有版本的 pr 都有效)。
- `-h`：“title” 设置每个文件的标题。
- `-l`：PAGE_LENGTH ：每页显示多少行。默认是每个页面一共 66 行。
- `-o`：MARGIN：每行缩进的空格数。
- `-w`：PAGE_WIDTH：多列输出时，设置页面宽度，默认是 72 个字符。

例如我有一个文件 food，里面的内容为:

```
Sweet Tooth
Bangkok Wok
Mandalay
Afghani Cuisine
Isle of Java
Big Apple Deli
Sushi and Sashimi
Tio Pepe's Peppers
```

使用命令：`pr -2 -h "food" food`

输出结果为:

```
2015-06-22 12:27                      food                       第 1 页
weet Tooth                          Isle of Java
Bangkok Wok                         Big Apple Deli
Mandalay                            Sushi and Sashimi
Afghani Cuisine                     Tio Pepe's Peppers'
```

解释:`pr` 会以文件的修改时间作为页面标题的时间戳；如果输入时自管道而来，则使用当前的时间，接上文件名称 (如果输入的数据内容在管道中，则为空) 以及页码。

`lp` 和 `lpr` 命令将文件传送到打印机进行打印。使用 pr 命令将文件格式化后就可以使用这两个命令来打印。例如:

`pr -2 -h "food" food | lpr`

命令成功执行会返回一个表示打印任务的 ID，通过这个 ID 可以取消打印或者查看打印状态。

如果你希望打印多份文件，可以使用 `lp` 的 `-nNum` 选项，或者 `lpr` 命令的 `-Num` 选项。Num 是一个数字，可以随意设置。

如果系统连接了多台打印机，可以使用 `lp` 命令的 `-dprinter` 选项，或者 `lpr` 命令的 `-Pprinter` 选项来选择打印机。printer 为打印机名称。

`lpstat` 和 `lpq` 命令

`lpstat` 命令可以查看打印机的缓存队列（有多少个文件等待打印），包括任务 ID、所有者、文件大小、请求时间和请求状态。

提示：等待打印的文件会被放到打印机的的缓存队列中。

使用 `lpstat -o` 命令查看打印机中所有等待打印的文件，`lpstat -o` 命令按照打印顺序输出队列中的文件。

`cancel` 和 `lprm` 分别用来终止 `lp` 和 `lpr` 的打印请求。使用这两个命令，需要指定 ID（由 `lp` 或 `lpq` 返回）或打印机名称。

`lprm` 命令用来取消当前用户的正在等待打印的文件，使用任务号作为参数可以取消指定文件，使用横线 (-) 作为参数可以取消所有文件。`lprm` 会返回被取消的文件名。