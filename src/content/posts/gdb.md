---

title: gdb

published: 2024-10-31

description: 'rt'

image: ''

tags: ["tutorial", "cheatsheet"]

category: 'tools'

draft: false 

---
参考[ipads调试教程](https://sjtu-ipads.github.io/OS-Course-Lab/Appendix/kernel-debugging.html)（注意如果使用vscode连接远端服务器需要配置laucher.json） [gdb介绍](https://sjtu-ipads.github.io/OS-Course-Lab/Appendix/toolchains/gdb)

在调试中，GDB 提供了许多常用命令来查看和控制程序的执行。以下是一些常用的 GDB 命令及其用途：

## [断点控制](https://sjtu-ipads.github.io/OS-Course-Lab/Appendix/toolchains/gdb/usage.html#断点控制)

- break `<expr>` : 在 `<expr>` 处创建一个断点。 `<expr>` 是一个最终可以求值为某条指令的地址的表达式。例如，如果想直接在某个地址处设置断点，可以写成 *`<address>` ，如果被调试程序有符号表，可以直接使用函数名。如果被调试程序有调试信息，可以指定在某个源文件的某一行设置断点，甚至可以使用更复杂的C表达式，更详细的信息可以参考手册。但需要注意，不论使用何种表达式，最终其实都是求值到一条指令的地址，gdb只是利用调试信息并帮助程 序员简化了这一步骤。
- info breakpoints : 列出当前的所有断点
- disable/enable `<NUM>` : 禁用/启用编号为 `<NUM>` 的断点，断点编号可以通过前述 info 命令查看。
- delete `<NUM>` : 删除编号为 `<NUM>` 的断点。

## [执行控制](https://sjtu-ipads.github.io/OS-Course-Lab/Appendix/toolchains/gdb/usage.html#执行控制)

- 不输入任何命令，直接按下回车键：重复上一条命令
- 在触发断点后，可以通过下列命令控制如何恢复程序的执行
- continue : 恢复程序执行，直到触发下一个断点
- continue `<NUM>` : 恢复程序执行，且忽略此断点 `<NUM>` 次
- kill : 终止程序执行
- quit : 退出gdb
- stepi : 执行下一条机器指令，随后继续暂停执行（单步调试）
- stepi `<NUM>` : 执行接下来 `<NUM>` 条指令
- step : 执行下一条语句，这属于源代码级调试，需要调试信息
- nexti : 执行下一条指令，且不跟踪（step through）函数调用。与 stepi 不同，如果当前指令是一条 bl 等指令，那么 stepi 会在被调用函数的第一条指令暂停（step in），而 nexti会在 bl 指令之后的那条指令，即被调用函数返回后的那条指令上暂停。
- nexti `<NUM>` , next : 与 stepi , step 类似，只是不跟踪函数调用。
- finish : 恢复执行直到当前被调用的函数返回

## [显示信息](https://sjtu-ipads.github.io/OS-Course-Lab/Appendix/toolchains/gdb/usage.html#显示信息)

- backtrace : 显示栈跟踪信息，可以看到当前函数是如何一步步被调用到的。但需要注意，由 于编译器的优化等因素，如果没有调试信息，栈跟踪信息可能是不准确的，甚至无法提供栈跟 踪信息。
- print`<mods>` `<expr>` : 打印表达式 `<expr>` 执行的值。其中 `<expr>` 可以是寄存器，如 `$pc , $sp , $x0 `，也可以是C表达式等。如果没有调试信息，则所使用的C表达式不能涉及到程序中 变量的值，但仍可以使用常量或寄存器中的值，例如，可以直接将某个内存地址作为数字打印： `print *(int *)0x1234`，或使用寄存器中的值参与运算等： `print $x0+$x1`。 `<mods>` 是可选的修饰符，可以用来控制打印的格式，详细信息可以参考手册。表达式求值 时，寄存器、变量、内存中的值都基于触发断点时程序的状态。
- x/NFU `<address>` : 打印内存中指定长度的内容。与 print 命令不同，该命令只能打印内存 中的内容，且不需要如print一样将内存地址转换为指针并求值。这是因为此命令只是将内存 视为字节数组，打印出其中给定数目的字节的内容，而不对字节内容做任何解释。相反， print 在打印指针求值表达式时，会根据指针的类型信息和 `<mods>` 来计算需要打印多少字 节，且会对多字节的内容进行解释，例如将它们显示为一个完整的整数，而非整数在内存中的 各个字节。此命令的修饰符中， N 表示需要打印的单元数目， U 的取值可以为 b , h , w , g ，分 别表示一个单元大小为1，2，4，8字节。 F 表示每个单元的打印格式，如 i 表示作为指令反 汇编， x 表示十六进制， d 表示十进制等，详细信息可以查看手册。
- display`<mods>` `<expr>` : 在每次程序暂停执行时，自动根据 `<mods>` 打印 `<expr>` 的值，适 用于一些频繁更改的表达式。利用这条命令可以在单步调试时自动查看所需关注的值，而无需 反复输入print命令。
- info display : 列出所有的自动打印。
- delete display `<NUM>` : 删除编号为 `<NUM>` 的自动打印

## [TUI](https://sjtu-ipads.github.io/OS-Course-Lab/Appendix/toolchains/gdb/usage.html#tui)

note

TUI(text UI)是gdb内置的高级命令行界面功能。TUI在gdb内部引入了类似窗口的概念，允许用户在使用gdb命令的同时查看被调试程序的汇编指令、源代码等，也允许用户设置自定义的UI布局，提高工作效率。

- lay asm : 进入TUI默认汇编布局，此布局下会同时开启一个gdb命令窗口以及一个汇编指令 窗口，用户可以直观看到当前正在执行的汇编指令。
- lay src : 进入TUI默认源代码布局，此布局下会同时开启gdb命令窗口和源代码窗口。需要 具备调试信息和源代码文件。
- tui disable : 退出TUI。
