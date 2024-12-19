---

title: missing semester

published: 2024-04-19

description: 'rt'

image: ''

tags: [notes]

category: 'learn'

draft: false 

---

[ms](https://missing-semester-cn.github.io/)

- [x]  数据整理
- [x]  命令行
- [ ]  元编程
- [x]  调试

## 导语

`echo` 程序将该参数打印出来

```
missing:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
missing:~$ which echo
/bin/echo
missing:~$ /bin/echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

`pwd` present working directory `ls -l`
列表查看目录下文件 （`rwx`）.
它们分别代表了文件所有者（`missing`），用户组（`users`）
以及其他所有人具有的权限 `touch example.txt` ## tee
`tee` 是一个常用的 Unix/Linux
命令，用于从标准输入读取数据，并将其复制到标准输出和一个或多个文件。这个命令的名称
“tee” 来自于 T
形管道，它的功能类似于将数据分成两部分，一部分继续通过管道传递，另一部分写入到文件中。

基本的语法是： `command | tee [options] [file ...]`

- `command`：表示前一个命令的输出会作为 `tee`
命令的输入。
- `tee`：命令名称。
- `options`：可以是一些选项，用来控制 `tee`
命令的行为。例如，`a`
选项表示追加写入文件而不是覆盖写入。
- `file ...`：表示一个或多个文件名，数据会写入到这些文件中。如果不指定文件名，`tee`
将只输出到标准输出。

举例来说，以下命令会将标准输入的数据输出到屏幕上，并同时写入到名为
“output.txt” 的文件中：
`echo "Hello, world!" | tee output.txt`

你还可以通过选项来改变行为，例如追加写入到文件：
`echo "Append this" | tee -a output.txt`

总之，`tee`
命令在处理管道数据时非常有用，它允许你同时查看输出并将数据保存到文件中。

## xargs

`xargs` 是一个强大的命令行工具，用于构建和执行命令行，它从标准输入（stdin）接收数据，然后将这些数据转换成命令行参数。`xargs` 能够处理大量的输入，并将其分割成安全长度的命令行，从而避免了命令行参数过长的问题。这使得 `xargs` 在处理需要对大量文件或结果进行操作的命令时非常有用。

### **基本用法**

`xargs` 最常见的用法是与管道（`|`）结合使用，将一个命令的输出作为另一个命令的输入。例如，你可以使用 `find` 命令查找所有的 `.txt` 文件，并使用 `xargs` 将这些文件传递给 `grep` 命令搜索特定的文本：

find . -name "*.txt" | xargs grep "特定文本"

- 如果你删除了一个文件，但通过 `du` 发现没有释放预期的磁盘空间，请检查文件是否被进程占用： `lsof | grep deleted | grep "filename-of-my-big-file"`
- 使用 `grep . *`（每行都会附上文件名）或者 `head -100 *`（每个文件有一个标题）来阅读检查目录下所有文件的内容。这在检查一个充满配置文件的目录（如 `/sys`、`/proc`、`/etc`）时特别好用。
- 如果你想在文件树上查看大小/日期，这可能看起来像递归版的 `ls -l` 但比 `ls -lR` 更易于理解：`find . -type f -ls`

### **指定命令**

默认情况下，`xargs` 会将接收到的输入作为 `echo` 命令的参数。但是，你可以使用 `-I{}` 选项指定一个替换字符串，然后在命令中使用这个替换字符串来明确地指定输入应该如何被命令使用：

find . -name "*.txt" | xargs -I{} mv {} /path/to/destination/

在这个例子中，`{}` 作为一个占位符，表示 `find` 命令的每个输出结果，然后 `xargs` 会将每个结果替换 `{}` 并执行 `mv` 命令将文件移动到指定目录。

## others

- 在 Bash 脚本中，使用 `set -x` 去调试输出（或者使用它的变体 `set -v`，它会记录原始输入，包括多余的参数和注释）。尽可能地使用严格模式：使用 `set -e` 令脚本在发生错误时退出而不是继续运行；使用 `set -u` 来检查是否使用了未赋值的变量；试试 `set -o pipefail`，它可以监测管道中的错误。当牵扯到很多脚本时，使用 `trap` 来检测 ERR 和 EXIT。一个好的习惯是在脚本文件开头这样写，这会使它能够检测一些错误，并在错误发生时中断程序并输出信息：

      `set -euo pipefail
      trap "echo 'error: Script failed: see failed command above'" ERR`

- 通过使用 `<(some command)` 可以将输出视为文件。例如，对比本地文件 `/etc/hosts` 和一个远程文件：

      `diff /etc/hosts <(ssh somehost cat /etc/hosts)`

ssh -L本地转发，-D使用远程服务器代理，-R远程转发

# shell 工具脚本

在bash中为变量赋值的语法是`foo=bar`，访问变量中存储的数值，其语法为 `$foo`。
需要注意的是，`foo = bar` （使用空格隔开）是不能正确工作的，因为解释器会调用程序`foo` 并将 `=` 和 `bar`作为参数。
总的来说，在shell脚本中使用空格会起到分割参数的作用，有时候可能会造成混淆，请务必多加检查。

Bash中的字符串通过`'` 和 `"`分隔符来定义，但是它们的含义并不相同。以`'`定义的字符串为原义字符串，其中的变量不会被转义，而 `"`定义的字符串会将变量值进行替换。

tldr/man很好的工具脚本

```
foo=bar
echo "$foo"
# 打印 bar
echo '$foo'
# 打印 $foo
```

- `$0` - 脚本名
- `$1` 到 `$9` -
脚本的参数。 `$1` 是第一个参数，依此类推。
- `$@` - 所有参数
- `$#` - 参数个数
- `$?` - 前一个命令的返回值
- `$$` - 当前脚本的进程识别码
- `!!` -
完整的上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。
- `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式
shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。

`$( CMD )` 这样的方式来执行`CMD` 这个命令时，它的输出结果会替换掉 `$( CMD )`

```
#!/bin/bash

echo "Starting program at $(date)" # date会被替换成日期和时间

echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # 如果模式没有找到，则grep退出状态为 1
    # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

- 花括号`{}` -
当你有一系列的指令，其中包含一段公共子串时，可以用花括号来自动展开这些命令。这在批量移动或转换文件时非常方便。

```
#!/usr/local/bin/python
import sys
for arg in reversed(sys.argv[1:]):
    print(arg)
```

内核知道去用 python 解释器而不是 shell
命令来运行这段脚本，是因为脚本的开头第一行的 [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))。。 ***
shell函数和脚本有如下一些不同点：

- 函数只能与shell使用相同的语言，脚本可以使用任意语言。因此在脚本中包含 `shebang` 是很重要的。
- 函数仅在定义时被加载，脚本会在每次被执行时加载。这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次。
- 函数会在当前的shell环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。脚本需要使用 `[export](https://man7.org/linux/man-pages/man1/export.1p.html)` 将环境变量导出，并将值传递给环境变量。
- 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell脚本中往往也会包含它们自己的函数定义。
## find `find`命令会递归地搜索符合条件的文件，例如：

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

find 可用fd代替，其语法更人性化 

## grep

`grep` 有很多选项，这也使它成为一个非常全能的工具。其中我经常使用的有 `-C` ：获取查找结果的上下文（Context）；`-v` 将对结果进行反选（Invert），也就是输出不匹配的结果。举例来说， `grep -C 5` 会输出匹配结果前后五行。当需要搜索大量文件的时候，使用 `-R` 会递归地进入子目录并搜索所有的文本文件。

但是，我们有很多办法可以对 `grep -R` 进行改进，例如使其忽略`.git` 文件夹，使用多CPU等等。

因此也出现了很多它的替代品，包括 [ack](https://beyondgrep.com/), [ag](https://github.com/ggreer/the_silver_searcher) 和 [rg](https://github.com/BurntSushi/ripgrep)。它们都特别好用，但是功能也都差不多，我比较常用的是
ripgrep (`rg`)
，因为它速度快，而且用法非常符合直觉。例子如下：

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

## 查找命令

`history | grep find` 会打印包含find子串的命令。

这里用history显示的话，用!(index)就可以直接运行那行指令

对于大多数的shell来说，您可以使用 `Ctrl+R` 对命令历史记录进行回溯搜索。敲 `Ctrl+R` 后您可以输入子串来进行匹配，查找历史命令行。

**broot/tree**

## 作业

在命令行中，你可以使用重定向操作符来改变这些标准流的行为。

- `>`：将标准输出重定向到指定文件，覆盖已有内容。
- `>>`：将标准输出重定向到指定文件，追加到已有内容之后。
- `2>`：将标准错误重定向到指定文件，覆盖已有内容。
- `2>>`：将标准错误重定向到指定文件，追加到已有内容之后。
- `&>`：将标准输出和标准错误都重定向到指定文件，覆盖已有内容。
- `&>>`：将标准输出和标准错误都重定向到指定文件，追加到已有内容之后。
*** 用[xargs](https://man7.org/linux/man-pages/man1/xargs.1.html) 命令，它可以使用标准输入中的内容作为参数。
例如 ls | xargs rm 会删除当前目录中的所有文件。

`xargs -d '\n' tar -cvzf html.zip`

这个命令的意图是将以换行符为分隔的输入作为参数，然后使用
`tar` 命令将这些文件压缩成一个名为 `html.zip`
的压缩文件。 如果你要将当前目录下的所有 `.html` 文件压缩到
`html.zip` 中，可以这样做：

`find . -name "*.html" -print0 | xargs -0 tar -cvzf html.zip`

在这个命令中：

`find . -name "*.html" -print0`
会找到当前目录及其子目录中的所有 `.html` 文件，并使用
`-print0` 选项将文件名用 null 分隔。`|`
管道将上述命令的输出传递给 `xargs` 命令的输入。
`xargs -0 tar -cvzf html.zip` 则将以 null
分隔的文件名列表作为参数，传递给 `tar` 命令，从而创建名为
`html.zip` 的压缩文件。 ***
`find . -type f -print0 | xargs -0 ls -lt | head -1`

命令使用了 `find`
查找当前目录下的所有文件，然后将这些文件名通过 `xargs` 传递给
`ls -lt` 命令，以列出文件并按时间排序。最后，使用
`head -1`
命令显示排序后的列表中的第一行，即最新修改的文件。

# vim

你可以按下 `<ESC>`（退出键）从任何其他模式返回正常模式。在正常模式，
键入 `i` 进入插入 模式， `R` 进入替换模式，
`v` 进入可视（一般）模式，
`V` 进入可视（行）模式， `<C-v>` （Ctrl-V,
有时也写作 `^V`）进入可视（块）模式，
`:` 进入命令模式。 ***

- `:q` 退出（关闭窗口）
- `:w` 保存（写）
- `:wq` 保存然后退出
- `:e {文件名}` 打开要编辑的文件
- `:ls` 显示打开的缓存
- `:help {标题}` 打开帮助文档
    - `:help :w` 打开 `:w` 命令的帮助文档
    - `:help w` 打开 `w` 移动的帮助文档 *** ##
    移动

多数时候你会在正常模式下，使用移动命令在缓存中导航。在 Vim
里面移动也被称为 “名词”， 因为它们指向文字块。

- 基本移动: `hjkl` （左， 下， 上， 右）
- 词： `w` （下一个词）， `b` （词初）， `e` （词尾）
- 行： `0` （行初）， `^` （第一个非空格字符）， `$` （行尾）
- 屏幕： `H` （屏幕首行）， `M` （屏幕中间）， `L` （屏幕底部）
- 翻页： `Ctrl-u` （上翻）， `Ctrl-d` （下翻）
- 文件： `gg` （文件头）， `G` （文件尾）
- 行数： `:{行数}<CR>` 或者 `{行数}G` ({行数}为行数)
- 杂项： `%` （找到配对，比如括号或者 /* */
之类的注释对）
- 查找： `f{字符}`， `t{字符}`， `F{字符}`， `T{字符}`
    - 查找/到 向前/向后 在本行的{字符}
    - `,` / `;` 用于导航匹配
- 搜索: `/{正则表达式}`, `n` / `N` 用于导航匹配

## 选择

可视化模式:

- 可视化：`v`
- 可视化行： `V`
- 可视化块：`Ctrl+v`

可以用移动命令来选中。

## 编辑

所有你需要用鼠标做的事，
你现在都可以用键盘：采用编辑命令和移动命令的组合来完成。 这就是 Vim
的界面开始看起来像一个程序语言的时候。Vim 的编辑命令也被称为 “动词”，
因为动词可以施动于名词。

- `i` 进入插入模式
    - 但是对于操纵/编辑文本，不单想用退格键完成
- `O` / `o` 在之上/之下插入行
- `d{移动命令}` 删除 {移动命令}
    - 例如，`dw` 删除词, `d$` 删除到行尾, `d0` 删除到行头。
- `c{移动命令}` 改变 {移动命令}
    - 例如，`cw` 改变词
    - 比如 `d{移动命令}` 再 `i`
- `x` 删除字符（等同于 `dl`）
- `s` 替换字符（等同于 `xi`）
- 可视化模式 + 操作
    - 选中文字, `d` 删除 或者 `c` 改变
- `u` 撤销, `<C-r>` 重做
- `y` 复制 / “yank”
（其他一些命令比如 `d` 也会复制）
- `p` 粘贴
- 更多值得学习的: 比如 `~` 改变字符的大小写

## 计数

你可以用一个计数来结合“名词”和“动词”，这会执行指定操作若干次。

- `3w` 向前移动三个词
- `5j` 向下移动5行
- `7dw` 删除7个词

## 修饰语

你可以用修饰语改变“名词”的意义。修饰语有 `i`，表示“内部”或者“在内“，和 `a`，
表示”周围“。

- `ci(` 改变当前括号内的内容
- `ci[` 改变当前方括号内的内容
- `da'` 删除一个单引号字符串， 包括周围的单引号 ## vim
进阶

这里我们提供了一些展示这个编辑器能力的例子。我们无法把所有的这样的事情都教给你，但是你
可以在使用中学习。一个好的对策是: 当你在使用你的编辑器的时候感觉
“一定有更好的方法来做这个”， 那么很可能真的有：上网搜寻一下。

## 搜索和替换

`:s` （替换）命令（[文档](http://vim.wikia.com/wiki/Search_and_replace)）。

- `%s/foo/bar/g`
    - 在整个文件中将 foo 全局替换成 bar
- `%s/\[.*\](\(.*\))/\1/g`
    - 将有命名的 Markdown 链接替换成简单 URLs

## 多窗口

- 用 `:sp` / `:vsp` 来分割窗口
- 同一个缓存可以在多个窗口中显示。

## 宏

- `q{字符}` 来开始在寄存器`{字符}`中录制宏
- `q`停止录制
- `@{字符}` 重放宏
- 宏的执行遇错误会停止
- `{计数}@{字符}`执行一个宏{计数}次
- 宏可以递归
    - 首先用`q{字符}q`清除宏
    - 录制该宏，用 `@{字符}` 来递归调用该宏
    （在录制完成之前不会有任何操作）
- 例子：将 xml 转成 json ([file](https://missing-semester-cn.github.io/2020/files/example-data.xml))
    - 一个有 “name” / “email” 键对象的数组
    - 用一个 Python 程序？
    - 用 sed / 正则表达式
        - `g/people/d`
        - `%s/<person>/{/g`
        - `%s/<name>\(.*\)<\/name>/"name": "\1",/g`
        - …
    - Vim 命令 / 宏
        - `Gdd`, `ggdd` 删除第一行和最后一行
        - 格式化最后一个元素的宏 （寄存器 `e`）
            - 跳转到有 `<name>` 的行
            - `qe^r"f>s": "<ESC>f<C"<ESC>q`
        - 格式化一个的宏
            - 跳转到有 `<person>` 的行
            - `qpS{<ESC>j@eA,<ESC>j@ejS},<ESC>q`
        - 格式化一个标签然后转到另外一个的宏
            - 跳转到有 `<person>` 的行
            - `qq@pjq`
        - 执行宏到文件尾
            - `999@q`
        - 手动移除最后的 `,` 然后加上 `[` 和 `]` 分隔符

# 数据整理

```
$ ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' > ssh.log
$ less ssh.log
```

多出来的引号先在远端机器上过滤文本内容，然后再将结果传输到本机。 
`less` 为我们创建来一个文件分页器，使我们可以通过翻页的方式浏览较长的文本。
我们可以将当前过滤出的日志保存到文件中。

## sed

`sed` 是一个基于文本编辑器`ed`构建的”流编辑器”
。在 `sed` 中，您基本上是利用一些简短的命令来修改文件，而不是直接操作文件的内容（尽管您也可以选择这样做）。相关的命令行非常多，但是最常用的是 `s`，即_替换_命令，例如我们可以这样写：

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

问题还没有完全解决，日志的内容全部被替换成了空字符串，整个日志的内容因此都被删除了。我们实际上希望能够将用户名_保留_下来。对此，我们可以使用“捕获组（capture
groups）”来完成。被圆括号内的正则表达式匹配到的文本，都会被存入一系列以编号区分的捕获组中。捕获组的内容可以在替换字符串时使用（有些正则表达式的引擎甚至支持替换表达式本身），例如`\1`、 `\2`、`\3`等等，因此可以使用如下命令：

```
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

## homework

我们希望按照出现次数排序，过滤出最常出现的用户名：

```
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
```

`sort` 会对其输入数据进行排序。`uniq -c` 会把连续出现的行折叠为一行并使用出现次数作为前缀。
`sort -n` 会按照数字顺序对输入进行排序（默认情况下是按照字典序排序 `-k1,1` 则表示“仅基于以空格分割的第一列进行排序”。`,n` 部分表示“仅排序到第n个部分”，默认情况是到行尾。就本例来说，针对整个行进行排序也没有任何问题，我们这里主要是为了学习这一用法！

```
sed -E "s/.*=\ (.*)s\.$/\1/"
```

这个 `sed`
命令使用正则表达式进行文本替换。让我解释这个命令的每个部分：

- `E`：这是 `sed`
命令的选项，表示使用扩展的正则表达式语法。
- `"s/.*=\ (.*)s\.$/\1/"`：这是一个正则表达式模式，用于匹配并替换文本。
    - `s/`：这是 `sed` 命令的替换操作。
    - `.*`：匹配任意字符零次或多次。
    - `=`：匹配等号字符。
    - `\` ：匹配一个空格字符。
    - `(.*)`：这是一个捕获组，用于匹配任意字符零次或多次，并将其捕获。
    - `s`：匹配字母 “s”。
    - `\.`：匹配句点字符。
    - `$`：匹配行的结尾。
    - `\1`：在替换中，这表示使用捕获组1的内容进行替换。

总之，这个 `sed` 命令的目的是从一行文本中匹配类似
“something = Xs.” 这样的模式，并将 “X”
提取出来。替换操作使用捕获组1中的内容进行替换，因此只保留了 “X”。

需要注意的是，如果你想要将这个命令应用于特定的文本，你需要将命令中的
`.*=\ (.*)s\.$` 替换为你实际要匹配的模式。 ***
`sed 's/ &nbsp;/ -/g'`： 这个命令用于替换 ”  ” 为 ”
-“。解释如下：

- `sed`: 流编辑器的命令。
- `'s/ &nbsp;/ -/g'`:
这是替换操作。让我们分解这个表达式：
    - `s/`: 替换操作的开始。
    - `&nbsp;`: 要匹配的字符串，即 “ ”。
    - `/`: 分隔符，将模式和替换部分分开。
    - : 替换为一个空格字符和一个连字符。
    - `/`: 替换部分的结束。
    - `g`:
    进行全局替换，不仅替换第一个匹配，而是所有匹配。

所以，这个命令的作用是将 ”  ” 替换为 ” -“。

# [命令行环境](https://missing-semester-cn.github.io/2020/command-line/)

信号可以让进程做其他的事情，而不仅仅是终止它们。例如，`SIGSTOP` 会让进程暂停。在终端中，键入 `Ctrl-Z` 会让
shell 发送 `SIGTSTP` 信号，`SIGTSTP`是 Terminal
Stop 的缩写（即`terminal`版本的SIGSTOP）。

我们可以使用 `[fg](https://www.man7.org/linux/man-pages/man1/fg.1p.html)` 或 `[bg](http://man7.org/linux/man-pages/man1/bg.1p.html)` 命令恢复暂停的工作。它们分别表示在前台继续或在后台继续。

`[jobs](http://man7.org/linux/man-pages/man1/jobs.1p.html)` 命令会列出当前终端会话中尚未完成的全部任务。您可以使用
pid
引用这些任务（也可以用 `[pgrep](https://www.man7.org/linux/man-pages/man1/pgrep.1.html)` 找出
pid）。更加符合直觉的操作是您可以使用百分号 +
任务编号（`jobs` 会打印任务编号）来选取该任务。如果要选择最近的一个任务，可以使用 `$!` 这一特殊参数。
**那就是命令中的 `&` 后缀可以让命令在直接在后台运行，这使得您可以直接在**shell 中继续做其他操作，不过它此时还是会使用 shell的标准输出，这一点有时会比较恼人（这种情况可以使用 shell重定向处理）。

让已经在运行的进程转到后台运行，您可以键入`Ctrl-Z` ，然后紧接着再输入`bg`。注意，后台的进程仍然是您的终端进程的子进程，一旦您关闭终端（会发送另外一个信号`SIGHUP`），这些后台的进程也会终止。为了防止这种情况发生，您可以使用 `[nohup](https://www.man7.org/linux/man-pages/man1/nohup.1.html)` (一个用来忽略 `SIGHUP` 的封装)
来运行程序。

```
1. `kill -SIGHUP`：这个命令会向当前终端会话中的所有进程发送 SIGHUP 信号，通常用于重新加载配置等操作。
2. `kill %2`：这个命令会向作业列表中的第二个作业发送 SIGTERM 信号，请求它终止。
3. `kill -STOP`：这个命令会向当前终端会话中的所有进程发送 SIGSTOP 信号，暂停它们的执行。
```

## tmux

`tmux` 的快捷键需要我们掌握，它们都是类似 `<C-b> x` 这样的组合，即需要先按下`Ctrl+b`，松开后再按下 `x`。`tmux` 中对象的继承结构如下：

- **会话** -
每个会话都是一个独立的工作区，其中包含一个或多个窗口
    - `tmux` 开始一个新的会话
    - `tmux new -s NAME` 以指定名称开始一个新的会话
    - `tmux ls` 列出当前所有会话
    - 在 `tmux` 中输入 `<C-b> d` ，将当前会话分离
    - `tmux a` 重新连接最后一个会话。您也可以通过 `t` 来指定具体的会话
- **窗口** -
相当于编辑器或是浏览器中的标签页，从视觉上将一个会话分割为多个部分
    - `<C-b> c` 创建一个新的窗口，使用 `<C-d>`关闭
    - `<C-b> N` 跳转到第 *N* 个窗口，注意每个窗口都是有编号的
    - `<C-b> p` 切换到前一个窗口
    - `<C-b> n` 切换到下一个窗口
    - `<C-b> ,` 重命名当前窗口
    - `<C-b> w` 列出当前所有窗口
- **面板** - 像 vim
中的分屏一样，面板使我们可以在一个屏幕里显示多个 shell
    - `<C-b> "` 水平分割
    - `<C-b> %` 垂直分割
    - `<C-b> <方向>` 切换到指定方向的面板，<方向>
    指的是键盘上的方向键
    - `<C-b> z` 切换当前面板的缩放
    - `<C-b> [` 开始往回卷动屏幕。您可以按下空格键来开始选择，回车键复制选中的部分
    - `<C-b> <空格>` 在不同的面板排布间切换

扩展阅读： [这里](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) 是一份 `tmux` 快速入门教程， [而这一篇](http://linuxcommand.org/lc3_adv_termmux.php) 文章则更加详细，它包含了 `screen` 命令。您也许想要掌握 `[screen](https://www.man7.org/linux/man-pages/man1/screen.1.html)` 命令，因为在大多数
UNIX 系统中都默认安装有该程序。

## 别名

输入一长串包含许多选项的命令会非常麻烦。因此，大多数 shell
都支持设置别名。shell 的别名相当于一个长命令的缩写，shell
会自动将其替换成原本的命令。例如，bash 中的别名语法如下：

```
alias alias_name="command_to_alias arg1 arg2"
```

注意， `=`两边是没有空格的，因为 `[alias](https://www.man7.org/linux/man-pages/man1/alias.1p.html)` 是一个
shell 命令，它只接受一个参数。

## 端口转发

分为本地端口转发和远程端口转发

[Mosh](https://mosh.org/)（即 mobile shell ）对 ssh 进行了改进，它允许连接漫游、间歇连接及智能本地回显。
kill -0 则不会发送信号，但是会在进程不存在时返回一个不为0的状态码

# git

[https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)

## Git 的命令行接口

为了避免重复信息，我们将不会详细解释以下命令行。强烈推荐您阅读 [Pro Git
中文版](https://git-scm.com/book/zh/v2)或可以观看本讲座的视频来学习。

### 基础

- `git help <command>`: 获取 git 命令的帮助信息
- `git init`: 创建一个新的 git
仓库，其数据会存放在一个名为 `.git` 的目录下
- `git status`: 显示当前的仓库状态
- `git add <filename>`: 添加文件到暂存区
- `git commit`: 创建一个新的提交
    - 如何编写 [良好的提交信息](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
    - 为何要 [编写良好的提交信息](https://chris.beams.io/posts/git-commit/)
- `git log`: 显示历史日志
- `git log --all --graph --decorate`:
可视化历史记录（有向无环图）
- `git diff <filename>`: 显示与暂存区文件的差异
- `git diff <revision> <filename>`:
显示某个文件两个版本之间的差异
- `git checkout <revision>`: 更新 HEAD
和目前的分支

### 分支和合并

- `git branch`: 显示分支
- `git branch <name>`: 创建分支
- `git checkout -b <name>`: 创建分支并切换到该分支
    - 相当于 `git branch <name>; git checkout <name>`
- `git merge <revision>`: 合并到当前分支
- `git mergetool`: 使用工具来处理合并冲突
- `git rebase`: 将一系列补丁变基（rebase）为新的基线

### 远端操作

- `git remote`: 列出远端
- `git remote add <name> <url>`:
添加一个远端
- `git push <remote> <local branch>:<remote branch>`:
将对象传送至远端并更新远端引用
- `git branch --set-upstream-to=<remote>/<remote branch>`:
创建本地和远端分支的关联关系
- `git fetch`: 从远端获取对象/索引
- `git pull`: 相当于 `git fetch; git merge`
- `git clone`: 从远端下载仓库

### 撤销

- `git commit --amend`: 编辑提交的内容或信息
- `git reset HEAD <file>`: 恢复暂存的文件
- `git checkout -- <file>`: 丢弃修改
- `git restore`: git2.32版本后取代git reset
进行许多撤销操作

## Git 高级操作

- `git config`: Git 是一个 [高度可定制的](https://git-scm.com/docs/git-config) 工具
- `git clone --depth=1`: 浅克隆（shallow
clone），不包括完整的版本历史信息
- `git add -p`: 交互式暂存
- `git rebase -i`: 交互式变基
- `git blame`: 查看最后修改某行的人
- `git stash`: 暂时移除工作目录下的修改内容
- `git bisect`: 通过二分查找搜索历史记录
- `.gitignore`: [指定](https://git-scm.com/docs/gitignore) 故意不追踪的文件 # [安全和密码学](https://missing-semester-cn.github.io/2020/security/)
熵等于`log_2(所有可能的个数，即n)`

## 对称密码学

```
openssl aes-256-cbc -salt -in afile -out secfile #加密文件

openssl aes-256-cbc -d -in secfile -out notsafefile #解密
```

## 非对称密码学：

```
keygen() -> (public key, private key)  (这是一个随机方法)

encrypt(plaintext: array<byte>, public key) -> array<byte>  (输出密文)
decrypt(ciphertext: array<byte>, private key) -> array<byte>  (输出明文)

sign(message: array<byte>, private key) -> array<byte>  (生成签名)
verify(message: array<byte>, signature: array<byte>, public key) -> bool  (验证签名是否是由和这个公钥相关的私钥生成的)
```

## SSH

我们在[之前的一堂课](https://missing-semester-cn.github.io/2020/command-line/#remote-machines)讨论了SSH和SSH密钥的使用。那么我们今天从密码学的角度来分析一下它们。

当你运行`ssh-keygen`命令，它会生成一个非对称密钥对：公钥和私钥`(public_key, private_key)`。
生成过程中使用的随机数由系统提供的熵决定。这些熵可以来源于硬件事件(hardware
events)等。 公钥最终会被分发，它可以直接明文存储。
但是为了防止泄露，私钥必须加密存储。`ssh-keygen`命令会提示用户输入一个密码，并将它输入密钥生成函数
产生一个密钥。最终，`ssh-keygen`使用对称加密算法和这个密钥加密私钥。

在实际运用中，当服务器已知用户的公钥（存储在`.ssh/authorized_keys`文件中，一般在用户HOME目录下），尝试连接的客户端可以使用非对称签名来证明用户的身份——这便是[挑战应答方式](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication)。
简单来说，服务器选择一个随机数字发送给客户端。客户端使用用户私钥对这个数字信息签名后返回服务器。
服务器随后使用`.ssh/authorized_keys`文件中存储的用户公钥来验证返回的信息是否由所对应的私钥所签名。这种验证方式可以有效证明试图登录的用户持有所需的私钥。

## 常见命令行标志参数及模式

命令行工具的用法千差万别，阅读 `man` 页面可以帮助你理解每种工具的用法。即便如此，下面我们将介绍一下命令行工具一些常见的共同功能。

- 大部分工具支持 `help` 或者类似的标志参数（flag）来显示它们的简略用法。
- 会造成不可撤回操作的工具一般会提供“空运行”（dry
run）标志参数，这样用户可以确认工具真实运行时会进行的操作。这些工具通常也会有“交互式”（interactive）标志参数，在执行每个不可撤回的操作前提示用户确认。
- `version` 或者 `V` 标志参数可以让工具显示它的版本信息（对于提交软件问题报告非常重要）。
- 基本所有的工具支持使用 `verbose` 或者 `v` 标志参数来输出详细的运行信息。多次使用这个标志参数，比如 `vvv`，可以让工具输出更详细的信息（经常用于调试）。同样，很多工具支持 `quiet` 标志参数来抑制除错误提示之外的其他输出。
- 大多数工具中，使用 `` 代替输入或者输出文件名意味着工具将从标准输入（standard
input）获取所需内容，或者向标准输出（standard
output）输出结果。
- 会造成破坏性结果的工具一般默认进行非递归的操作，但是支持使用“递归”（recursive）标志函数（通常是 `r`）。
- 有的时候你可能需要向工具传入一个 *看上去* 像标志参数的普通参数，比如：
    - 使用 `rm` 删除一个叫 `r` 的文件；
    - 在通过一个程序运行另一个程序的时候（`ssh machine foo`），向内层的程序（`foo`）传递一个标志参数。
    
    这时候你可以使用特殊参数 `--` 让某个程序 *停止处理* `--` 后面出现的标志参数以及选项（以 `-` 开头的内容）：
    
    - `rm -- -r` 会让 `rm` 将 `r` 当作文件名；
    - `ssh machine --for-ssh -- foo --for-foo` 的 `` 会让 `ssh` 知道 `for-foo` 不是 `ssh` 的标志参数。
    
    # 性能分析
    
    - **通用监控** - 最流行的工具要数 [`htop`](https://htop.dev/),了，它是 [`top`](http://man7.org/linux/man-pages/man1/top.1.html)的改进版。`htop` 可以显示当前运行进程的多种统计信息。`htop` 有很多选项和快捷键，常见的有：`<F6>` 进程排序、 `t` 显示树状结构和 `h` 打开或折叠线程。 还可以留意一下 [`glances`](https://nicolargo.github.io/glances/) ，它的实现类似但是用户界面更好。如果需要合并测量全部的进程， [`dstat`](http://dag.wiee.rs/home-made/dstat/) 是也是一个非常好用的工具，它可以实时地计算不同子系统资源的度量数据，例如 I/O、网络、 CPU 利用率、上下文切换等等；
    - **I/O 操作** - [`iotop`](http://man7.org/linux/man-pages/man8/iotop.8.html) 可以显示实时 I/O 占用信息而且可以非常方便地检查某个进程是否正在执行大量的磁盘读写操作；
    - **磁盘使用** - [`df`](http://man7.org/linux/man-pages/man1/df.1.html) 可以显示每个分区的信息，而 [`du`](http://man7.org/linux/man-pages/man1/du.1.html) 则可以显示当前目录下每个文件的磁盘使用情况（ **d**isk **u**sage）。`h` 选项可以使命令以对人类（**h**uman）更加友好的格式显示数据；[`ncdu`](https://dev.yorhel.nl/ncdu)是一个交互性更好的 `du` ，它可以让您在不同目录下导航、删除文件和文件夹；
    - **内存使用** - [`free`](http://man7.org/linux/man-pages/man1/free.1.html) 可以显示系统当前空闲的内存。内存也可以使用 `htop` 这样的工具来显示；
    - **打开文件** - [`lsof`](http://man7.org/linux/man-pages/man8/lsof.8.html) 可以列出被进程打开的文件信息。 当我们需要查看某个文件是被哪个进程打开的时候，这个命令非常有用；
    - **网络连接和配置** - [`ss`](http://man7.org/linux/man-pages/man8/ss.8.html) 能帮助我们监控网络包的收发情况以及网络接口的显示信息。`ss` 常见的一个使用场景是找到端口被进程占用的信息。如果要显示路由、网络设备和接口信息，您可以使用 [`ip`](http://man7.org/linux/man-pages/man8/ip.8.html) 命令。注意，`netstat` 和 `ifconfig` 这两个命令已经被前面那些工具所代替了。
    - **网络使用** - [`nethogs`](https://github.com/raboof/nethogs) 和 [`iftop`](http://www.ex-parrot.com/pdw/iftop/) 是非常好的用于对网络占用进行监控的交互式命令行工具。
    - 基本所有的工具支持使用 `-verbose` 或者 `v` 标志参数来输出详细的运行信息。多次使用这个标志参数，比如 `vvv`，可以让工具输出更详细的信息（经常用于调试）。同样，很多工具支持 `-quiet` 标志参数来抑制除错误提示之外的其他输出。
    - 大多数工具中，使用 `` 代替输入或者输出文件名意味着工具将从标准输入（standard input）获取所需内容，或者向标准输出（standard output）输出结果。
    - 会造成破坏性结果的工具一般默认进行非递归的操作，但是支持使用“递归”（recursive）标志函数（通常是 `r`）。
    - 有的时候你可能需要向工具传入一个 *看上去* 像标志参数的普通参数，比如：
        - 使用 `rm` 删除一个叫 `r` 的文件；
        - 在通过一个程序运行另一个程序的时候（`ssh machine foo`），向内层的程序（`foo`）传递一个标志参数。
        
        这时候你可以使用特殊参数 `--` 让某个程序 *停止处理* `--` 后面出现的标志参数以及选项（以 `-` 开头的内容）：
        
        - `rm -- -r` 会让 `rm` 将 `r` 当作文件名；
        
        ## **Hammerspoon (macOS 桌面自动化)**
        
        [Hammerspoon](https://www.hammerspoon.org/) 是面向 macOS 的一个桌面自动化框架。它允许用户编写和操作系统功能挂钩的 Lua 脚本，从而与键盘、鼠标、窗口、文件系统等交互。
        
        下面是 Hammerspoon 的一些示例应用：
        
        - 绑定移动窗口到的特定位置的快捷键
        - 创建可以自动将窗口整理成特定布局的菜单栏按钮
        - 在你到实验室以后，通过检测所连接的 WiFi 网络自动静音扬声器
        - 在你不小心拿了朋友的充电器时弹出警告
        
        从用户的角度，Hammerspoon 可以运行任意 Lua 代码，绑定菜单栏按钮、按键、或者事件。Hammerspoon 提供了一个全面的用于和系统交互的库，因此它能没有限制地实现任何功能。你可以从头编写自己的 Hammerspoon 配置，也可以结合别人公布的配置来满足自己的需求。
        
        ### **资源**
        
        - [Getting Started with Hammerspoon](https://www.hammerspoon.org/go/)：Hammerspoon 官方教程
        - [Sample configurations](https://github.com/Hammerspoon/hammerspoon/wiki/Sample-Configurations)：Hammerspoon 官方示例配置
        - [Anish’s Hammerspoon config](https://github.com/anishathalye/dotfiles-local/tree/mac/hammerspoon)：Anish 的 Hammerspoon 配置