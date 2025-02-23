---

title: 命令行

published: 2025-02-23

description: 'rt'

image: ''

tags: [tutorial]

category: 'tools'

draft: false 

---

## tee 管道传递到文件

`tee` 是一个常用的 Unix/Linux命令，用于**从标准输入读取数据，并将其复制到标准输出和一个或多个文件**。这个命令的名称“tee” 来自于 T形管道，它的功能类似于将数据分成两部分，一部分继续通过管道传递，另一部分写入到文件中。

基本的语法是： `command | tee [options] [file ...]`

命令会将标准输入的数据输出到屏幕上，并同时写入到名为“output.txt” 的文件中：`echo "Append this" | tee -a output.txt`

## xargs

`xargs` 是一个强大的命令行工具，用于构建和执行命令行，它从标准输入（stdin）接收数据，然后将这些数据转换成命令行参数。`xargs` 能够处理大量的输入，并将其分割成安全长度的命令行，从而避免了命令行参数过长的问题。这使得 `xargs` 在处理需要对大量文件或结果进行操作的命令时非常有用。

`xargs` 最常见的用法是与管道（`|`）结合使用，将一个命令的输出作为另一个命令的输入。例如，你可以使用 `find` 命令查找所有的 `.txt` 文件，并使用 `xargs` 将这些文件传递给 `grep` 命令搜索特定的文本：

find . -name "*.txt" | xargs grep "特定文本"

- 用[xargs](https://man7.org/linux/man-pages/man1/xargs.1.html) 命令，它可以使用标准输中的内容作为参数。例如 ls | xargs rm 会删除当前目录中的所有文件。

`xargs -d '\n' tar -cvzf html.zip`

这个命令的意图是将以换行符为分隔的输入作为参数，然后使用`tar` 命令将这些文件压缩成一个名为 `html.zip`的压缩文件。 如果你要将当前目录下的所有 `.html` 文件压缩到`html.zip` 中，可以这样做：

`find . -name "*.html" -print0 | xargs -0 tar -cvzf html.zip`

在这个命令中：`find . -name "*.html" -print0`会找到当前目录及其子目录中的所有 `.html` 文件，并使用`-print0` 选项将文件名用 null 分隔。`|`
管道将上述命令的输出传递给 `xargs` 命令的输入。`xargs -0 tar -cvzf html.zip` 则将以 null分隔的文件名列表作为参数，传递给 `tar` 命令，从而创建名为
`html.zip` 的压缩文件。 `find . -type f -print0 | xargs -0 ls -lt | head -1`命令使用了 `find`查找当前目录下的所有文件，然后将这些文件名通过 `xargs` 传递给`ls -lt` 命令，以列出文件并按时间排序。最后，使用`head -1`命令显示排序后的列表中的第一行，即最新修改的文件。

### **指定命令**

默认情况下，`xargs` 会将接收到的输入作为 `echo` 命令的参数。但是，你可以使用 `-I{}` 选项指定一个替换字符串，然后在命令中使用这个替换字符串来明确地指定输入应该如何被命令使用：

find . -name "*.txt" | xargs -I{} mv {} /path/to/destination/

在这个例子中，`{}` 作为一个占位符，表示 `find` 命令的每个输出结果，然后 `xargs` 会将每个结果替换 `{}` 并执行 `mv` 命令将文件移动到指定目录。

## 查看文件

使用 `grep . *`（每行都会附上文件名）或者 `head -100 *`（每个文件有一个标题）来阅读检查目录下所有文件的内容。这在检查一个充满配置文件的目录（如 `/sys`、`/proc`、`/etc`）时特别好用。

如果你删除了一个文件，但通过 `du` 发现没有释放预期的磁盘空间，请检查文件是否被进程占用： `lsof | grep deleted | grep "filename-of-my-big-file"`

- 如果你想在文件树上查看大小/日期，这可能看起来像递归版的 `ls -l` 但比 `ls -lR` 更易于理解：`find . -type f -ls`

- 通过使用 `<(some command)` 可以将输出视为文件。例如，对比本地文件 `/etc/hosts` 和一个远程文件：`diff /etc/hosts <(ssh somehost cat /etc/hosts)`

  ssh -L本地转发，-D使用远程服务器代理，-R远程转发

​	

## 脚本

- 在 Bash 脚本中，使用 `set -x` 去调试输出（或者使用它的变体 `set -v`，它会记录原始输入，包括多余的参数和注释）。尽可能地使用严格模式：使用 `set -e` 令脚本在发生错误时退出而不是继续运行；使用 `set -u` 来检查是否使用了未赋值的变量；试试 `set -o pipefail`，它可以监测管道中的错误。当牵扯到很多脚本时，使用 `trap` 来检测 ERR 和 EXIT。一个好的习惯是在脚本文件开头这样写，这会使它能够检测一些错误，并在错误发生时中断程序并输出信息：

      `set -euo pipefail
      trap "echo 'error: Script failed: see failed command above'" ERR`

在bash中为变量赋值的语法是`foo=bar`，访问变量中存储的数值，其语法为 `$foo`。需要注意的是，`foo = bar` （使用空格隔开）是不能正确工作的，因为解释器会调用程序`foo` 并将 `=` 和 `bar`作为参数。

Bash中的字符串通过`'` 和 `"`分隔符来定义，但是它们的含义并不相同。以`'`定义的字符串为原义字符串，其中的变量不会被转义，而 `"`定义的字符串会将变量值进行替换。

```
foo=bar
echo "$foo" # 打印 bar
echo '$foo' # 打印 $foo
```

- `$0` - 脚本名
- `$1` 到 `$9` -脚本的参数。 `$1` 是第一个参数，依此类推。
- `$@` - 所有参数
- `$#` - 参数个数
- `$?` - 前一个命令的返回值
- `$$` - 当前脚本的进程识别码
- `!!` -完整的上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。
- `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式
  shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。

+ `$( CMD )` 这样的方式来执行`CMD` 这个命令时，cmd的输出结果会替换掉 `$( CMD )`

- 花括号`{}` -当你有一系列的指令，其中包含一段公共子串时，可以用花括号来自动展开这些命令。这在批量移动或转换文件时非常方便。

```
#!/usr/local/bin/python内核知道去用 python 解释器而不是 shell
import sys
for arg in reversed(sys.argv[1:]):
    print(arg)
```


shell函数和脚本有如下一些不同点：

- 函数只能与shell使用相同的语言，脚本可以使用任意语言。因此在脚本中包含 `shebang` 是很重要的。
- 函数仅在定义时被加载，脚本会在每次被执行时加载。这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次。
- 函数会在当前的shell环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。脚本需要使用 [export](https://man7.org/linux/man-pages/man1/export.1p.html) 将环境变量导出，并将值传递给环境变量。
- 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell脚本中往往也会包含它们自己的函数定义。

## find 递归地搜索符合条件的文件

```
# 查找所有名称为src的文件夹
find . -name src -type d
# 查找所有文件夹路径中包含test的python文件
find . -path '*/test/*.py' -type f
# 查找前一天修改的所有文件
find . -mtime -1
# 查找所有大小在500k至10M的tar.gz文件
find . -size +500k -size -10M -name '*.tar.gz'
```

除了列出所寻找的文件之外，find
还能对所有查找到的文件进行操作。这能极大地简化一些单调的任务。

```
# 删除全部扩展名为.tmp 的文件
find . -name '*.tmp' -exec rm {} \;
# 查找全部的 PNG 文件并将其转换为 JPG
find . -name '*.png' -exec convert {} {}.jpg \;
```

## grep

`grep` 有很多选项，这也使它成为一个非常全能的工具。其中我经常使用的有 `-C` ：获取查找结果的上下文（Context）；**`-v` 将对结果进行反选（Invert）**，也就是输出不匹配的结果。举例来说**， `grep -C 5` 会输出匹配结果前后五行**。当需要搜索大量文件的时候，使用 `-R` 会递归地进入子目录并搜索所有的文本文件。

但是，我们有很多办法可以对 `grep -R` 进行改进，例如使其忽略`.git` 文件夹，使用多CPU等等。

因此也出现了很多它的替代品，包括 [ack](https://beyondgrep.com/), [ag](https://github.com/ggreer/the_silver_searcher) 和 [rg](https://github.com/BurntSushi/ripgrep)。

ripgrep (`rg`)，速度快，而且用法非常符合直觉。例子如下：

```
# 查找所有使用了 requests 库的文件
rg -t py 'import requests'
# 查找所有没有写 shebang 的文件（包含隐藏文件）（之查找shell
rg -u --files-without-match "^#!"（-t sh
# 查找所有的foo字符串，并打印其之后的5行
rg foo -A 5
# 打印匹配的统计信息（匹配的行和文件的数量）
rg --stats PATTERN
rg "sdf" -t py -C 5 ~/tmp
```

对于大多数的shell来说，您可以使用 `Ctrl+R` 对命令历史记录进行回溯搜索。敲 `Ctrl+R` 后您可以输入子串来进行匹配，查找历史命令行。

## 重定向操作符

- `>`：将标准输出重定向到指定文件，覆盖已有内容。
- `>>`：将标准输出重定向到指定文件，追加到已有内容之后。
- `2>`：将标准错误重定向到指定文件，覆盖已有内容。
- `2>>`：将标准错误重定向到指定文件，追加到已有内容之后。
- `&>`：将标准输出和标准错误都重定向到指定文件，覆盖已有内容。
- `&>>`：将标准输出和标准错误都重定向到指定文件，追加到已有内容之后。

## 数据整理

```
$ ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' > ssh.log
$ less ssh.log
```

多出来的引号先在远端机器上过滤文本内容，然后再将结果传输到本机。 
`less` 为我们创建来一个文件分页器，使我们可以通过翻页的方式浏览较长的文本。
我们可以将当前过滤出的日志保存到文件中。

## sed

`sed` 是一个基于文本编辑器`ed`构建的”流编辑器”
。在 `sed` 中，您基本上是利用一些简短的命令来修改文件，而不是直接操作文件的内容（尽管您也可以选择这样做）。相关的命令行非常多，但是最常用的**是 `s`，替换命令**，例如我们可以这样写：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed 's/.*Disconnected from //'
```

上面这段命令中，我们使用了一段简单的_正则表达式_。正则表达式是一种非常强大的工具，可以让我们基于某种模式来对字符串进行匹配。`s` 命令的语法如下：`s/REGEX/SUBSTITUTION/`,
其中 `REGEX` 部分是我们需要使用的 `[正则表达式](obsidian://open?vault=tubehao&file=CS50P)`，而 `SUBSTITUTION` 是用于替换匹配结果的文本。

```
| sed -E 's/.*Disconnected from (invalid |authenticating )?user .* [^ ]+ port [0-9]+( \[preauth\])?$//'
```

让我们借助正则表达式在线调试工具[regex
debugger](https://regex101.com/r/qqbZqh/2) 来理解这段表达式。OK，开始的部分和以前是一样的，随后，我们匹配两种类型的“user”（在日志中基于两种前缀区分）。再然后我们匹配属于用户名的所有字符。接着，再匹配任意一个单词（`[^ ]+` 会匹配任意非空且不包含空格的序列）。紧接着后面匹配单“port”和它后面的一串数字，以及可能存在的后缀`[preauth]`，最后再匹配行尾。

问题还没有完全解决，日志的内容全部被替换成了空字符串，整个日志的内容因此都被删除了。我们实际上希望能够将用户名_保留_下来。对此，我们可以使用“捕获组（capture groups）”来完成。被圆括号内的正则表达式匹配到的文本，都会被存入一系列以编号区分的捕获组中。捕获组的内容可以在替换字符串时使用（有些正则表达式的引擎甚至支持替换表达式本身），例如`\1`、 `\2`、`\3`等等，因此可以使用如下命令：

```
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```



我们希望按照出现次数排序，过滤出最常出现的用户名：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
```

**`sort` 会对其输入数据进行排序。`uniq -c` 会把连续出现的行折叠为一行并使用出现次数作为前缀。**
`sort -n` 会按照数字顺序对输入进行排序（默认情况下是按照字典序排序 `-k1,1` 则表示“仅基于以空格分割的第一列进行排序”。`,n` 部分表示“仅排序到第n个部分”，默认情况是到行尾。就本例来说，针对整个行进行排序也没有任何问题，我们这里主要是为了学习这一用法！

```
sed -E "s/.*=\ (.*)s\.$/\1/"
```

这个 `sed`命令使用正则表达式进行文本替换。让我解释这个命令的每个部分：

- `E`：这是 `sed`命令的选项，表示使用扩展的正则表达式语法。
- `"s/.*=\ (.*)s\.$/\1/"`：这是一个正则表达式模式，用于匹配并替换文本。
  - `s/`：这是 `sed` 命令的替换操作。
  - `.*`：匹配任意字符零次或多次。
  - `=`：匹配等号字符。
  - `\` ：匹配一个空格字符。
  - `(.*)`：这是一个捕获组，用于匹配任意字符零次或多次，并将其捕获。
  - `s`：匹配字母 “s”。
  - `\.`：匹配句点字符。
  - **`$`：匹配行的结尾。**
  - `\1`：在替换中，这表示使用捕获组1的内容进行替换。

总之，这个 `sed` 命令的目的是从一行文本中匹配类似“something = Xs.” 这样的模式，并将 “X”提取出来。替换操作使用捕获组1中的内容进行替换，因此只保留了 “X”。

需要注意的是，如果你想要将这个命令应用于特定的文本，你需要将命令中的`.*=\ (.*)s\.$` 替换为你实际要匹配的模式。

## 信号和前后台

信号可以让进程做其他的事情，而不仅仅是终止它们。例如，`SIGSTOP` 会让进程暂停。在终端中，键入 `Ctrl-Z` 会让shell 发送 `SIGTSTP` 信号，`SIGTSTP`是 Terminal Stop 的缩写（即`terminal`版本的SIGSTOP）。

我们可以使用 [fg](https://www.man7.org/linux/man-pages/man1/fg.1p.html) 或 [bg](http://man7.org/linux/man-pages/man1/bg.1p.html) 命令恢复暂停的工作。它们分别表示在前台继续或在后台继续。[jobs](http://man7.org/linux/man-pages/man1/jobs.1p.html) 命令会列出当前终端会话中尚未完成的全部任务。您可以使用pid
引用这些任务（也可以用 [pgrep](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 找出pid）。更加符合直觉的操作是您可以使用百分号 +任务编号（`jobs` 会打印任务编号）来选取该任务。如果要选择最近的一个任务，可以使用 `$!` 这一特殊参数。
**命令中的 `&` 后缀可以让命令在直接在后台运行，这使得您可以直接在**shell 中继续做其他操作，不过此时还是会使用 shell的标准输出，可以使用 shell重定向处理

让已经在运行的进程转到后台运行，您可以键入`Ctrl-Z` ，然后紧接着再输入`bg`。注意，后台的进程仍然是您的终端进程的子进程，一旦您关闭终端（会发送另外一个信号`SIGHUP`），这些后台的进程也会终止。为了防止这种情况发生，您可以使用 [nohup](https://www.man7.org/linux/man-pages/man1/nohup.1.html) (一个用来忽略 `SIGHUP` 的封装)来运行程序。


1. `kill -SIGHUP`：这个命令会向当前终端会话中的所有进程发送 SIGHUP 信号，通常用于重新加载配置等操作。
2. `kill %2`：这个命令会向作业列表中的第二个作业发送 SIGTERM 信号，请求它终止。
3. `kill -STOP`：这个命令会向当前终端会话中的所有进程发送 SIGSTOP 信号，暂停它们的执行。


## 命令行标志参数及模式

命令行工具的用法千差万别，阅读 `man` 页面可以帮助你理解每种工具的用法。即便如此，下面我们将介绍一下命令行工具一些常见的共同功能。

- 基本所有的工具支持使用 `verbose` 或者 `v` 标志参数来输出详细的运行信息。多次使用这个标志参数，比如 `vvv`，可以让工具输出更详细的信息（经常用于调试）。同样，很多工具支持 `quiet` 标志参数来抑制除错误提示之外的其他输出。

- 大多数工具中，使用代替输入或者输出文件名意味着工具将从标准输入（standard input）获取所需内容，或者向标准输出（standard output）输出结果。

- 会造成破坏性结果的工具一般默认进行非递归的操作，但是支持使用“递归”（recursive）标志函数（通常是 `r`）。

- 有的时候你可能需要向工具传入一个 *看上去* 像标志参数的普通参数，比如：

  - 使用 `rm` 删除一个叫 `r` 的文件；
  - 在通过一个程序运行另一个程序的时候（`ssh machine foo`），向内层的程序（`foo`）传递一个标志参数。

  这时候你可以使用特殊参数 `--` 让某个程序 *停止处理* `--` 后面出现的标志参数以及选项（以 `-` 开头的内容）：

  - `rm -- -r` 会让 `rm` 将 `r` 当作文件名；

## 硬件状态

- **通用监控** - 最流行的工具要数 [`htop`](https://htop.dev/),了，它是 [`top`](http://man7.org/linux/man-pages/man1/top.1.html)的改进版。`htop` 可以显示当前运行进程的多种统计信息。`htop` 有很多选项和快捷键，常见的有：`<F6>` 进程排序、 `t` 显示树状结构和 `h` 打开或折叠线程。 还可以留意一下 [`glances`](https://nicolargo.github.io/glances/) ，它的实现类似但是用户界面更好。如果需要合并测量全部的进程， [`dstat`](http://dag.wiee.rs/home-made/dstat/) 是也是一个非常好用的工具，它可以实时地计算不同子系统资源的度量数据，例如 I/O、网络、 CPU 利用率、上下文切换等等；
- **I/O 操作** - [`iotop`](http://man7.org/linux/man-pages/man8/iotop.8.html) 可以显示实时 I/O 占用信息而且可以非常方便地检查某个进程是否正在执行大量的磁盘读写操作；
- **磁盘使用** - [`df`](http://man7.org/linux/man-pages/man1/df.1.html) 可以显示每个分区的信息，而 [`du`](http://man7.org/linux/man-pages/man1/du.1.html) 则可以显示当前目录下每个文件的磁盘使用情况（ **d**isk **u**sage）。`h` 选项可以使命令以对人类（**h**uman）更加友好的格式显示数据；[`ncdu`](https://dev.yorhel.nl/ncdu)是一个交互性更好的 `du` ，它可以让您在不同目录下导航、删除文件和文件夹；
- **内存使用** - [`free`](http://man7.org/linux/man-pages/man1/free.1.html) 可以显示系统当前空闲的内存。内存也可以使用 `htop` 这样的工具来显示；
- **打开文件** - [`lsof`](http://man7.org/linux/man-pages/man8/lsof.8.html) 可以列出被进程打开的文件信息。 当我们需要查看某个文件是被哪个进程打开的时候，这个命令非常有用；
- **网络连接和配置** - [`ss`](http://man7.org/linux/man-pages/man8/ss.8.html) 能帮助我们监控网络包的收发情况以及网络接口的显示信息。`ss` 常见的一个使用场景是找到端口被进程占用的信息。如果要显示路由、网络设备和接口信息，您可以使用 [`ip`](http://man7.org/linux/man-pages/man8/ip.8.html) 命令。注意，`netstat` 和 `ifconfig` 这两个命令已经被前面那些工具所代替了。
- **网络使用** - [`nethogs`](https://github.com/raboof/nethogs) 和 [`iftop`](http://www.ex-parrot.com/pdw/iftop/) 是非常好的用于对网络占用进行监控的交互式命令行工具。