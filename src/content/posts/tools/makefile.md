---

title: Makefile

published: 2024-04-19

description: 'rt'

image: ''

tags: [notes]

category: 'tools'

draft: false 

---

# 介绍

- 可以声明变量并通过$()使用
- 可以自动将.o文件的. c加在依赖
- 通过清空目标文件来维持整洁
- #注释
- `include foo.make *.mk $(bar)`
- -I指定寻找文件目录  - 代表找不到也没关系

# 书写规则

`objects := **$(**wildcard *.o**)` ：object包含所有.o文件**

**`vpath <pattern> <directories>`**

为符合模式<pattern>的文件指定搜索目录<directories>。

**`vpath <pattern>`**

清除符合模式<pattern>的文件的搜索目录。

**`vpath`**

清除所有已被设置好了的文件搜索目录。

```
.PHONY : clean　＃声明ｃｌｅａｎ是伪目标
clean :
    -rm edit$(objects)
```

all是默认目标，所以一定被执行，但是是伪目标，所以不会生成文件，从而一口气生成三个文件

```
all : prog1 prog2 prog3
.PHONY : all

prog1 : prog1.o utils.o
    cc -o prog1 prog1.o utils.o

prog2 : prog2.o
    cc -o prog2 prog2.o

prog3 : prog3.o sort.o utils.o
    cc -o prog3 prog3.o sort.o utils.o
```

此外，伪目标也可以作为依赖

## 静态模式

```
<targets ...> : <target-pattern> : <prereq-patterns ...>
    <commands>
    ...
```

```
files = foo.elc bar.o lose.o

$(filter %.o,$(files)): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@
$(filter %.elc,$(files)): %.elc: %.el
    emacs -f batch-byte-compile $<
```

其中，`$<` 表示第一个依赖文件， `$@` 表示目标集，`$(filter %.o,$(files))`表示调用Makefile的filter函数，过滤“$files”集

## 自动依赖

`cc -M main.c`自动找寻依赖，但是会包含标准库，-MM不会

.d文件用于表示依赖

```
%.d: %.c
    @set -e; rm -f $@;\$(CC) -M$(CPPFLAGS) $< > $@.$$$$;\    sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@;\    rm -f $@.$$$$
```

## 显示命令

通常，make会把其要执行的命令行在命令执行前输出到屏幕上。当我们用 `@` 字符在命令行前，那么，这个命令将不被make显示出来，最具代表性的例子是，我们用这个功能来向屏幕显示一些信息。如:

**`@echo** 正在编译XXX模块......`

如果make执行时，带入make参数 `-n` 或 `--just-print` ，那么其只是显示命令，但不会执行命令，这个功能很有利于我们调试我们的Makefile，看看我们书写的命令是执行起来是什么样子的或是什么顺序
参数 `-s` 或 `--silent` 或 `--quiet` 则是全面禁止命令的显示。

如果你要让上一条命令的结果应用在下一条命令时，你应该使用分号分隔这两条命令。不能把这两条命令写在两行上，而应该把这两条命令写在一行上，用分号分隔。

-用于忽略错误。还有一个全局的办法是，给make加上 `-i` 或是 `--ignore-errors` 参数，那么，Makefile中所有命令都会忽略错误。而如果一个规则是以 `.IGNORE` 作为目标的，那么这个规则中的所有命令将会忽略错误。

声明变量时export会传递到下级makefile，unexport不会

`subsystem:
    cd subdir && **$(**MAKE**)**`

其等价于：（定义$(MAKE)宏变量的意思是，也许我们的make需要一些参数，所以定义成一个变量比较利于维护

`subsystem:
    **$(**MAKE**)** -C subdir`

总控Makefile的变量可以传递到下级的Makefile中（如果你显示的声明），但是不会覆盖下层的Makefile中所定义的变量，除非指定了 `-e` 参数。
有两个变量，一个是 `SHELL` ，一个是 `MAKEFLAGS` ，这两个变量不管你是否export，其总是要传递到下层 Makefile中
“嵌套执行”中比较有用的参数， `-w` 或是 `--print-directory` 会在make的过程中输出一些信息，让你看到目前的工作目录。

## 命令包

```
define run-yacc
yacc $(firstword $^)
mv y.tab.c $@
endef
```

# 变量

真实的 `$` 字符，那么你需要用 `$$` 来表示。
`:=`不能使用后面定义的变量，避免=的循环定义

`MAKELEVEL`记录调用层数

```
nullstring :=
space :=$(nullstring) # end of the line
```

注意变量是定义到#的

`FOO ?= bar`含义是，如果FOO没有被定义过，那么变量FOO的值就是“bar”，如果FOO先前被定义过，那么这条语将什么也不做

```
foo := a.o b.o c.o
bar :=$(foo:%.o=%.c)
```

```
ifdef do_sort
    func := sort
else
    func := strip
endif

bar := a d b g q c

foo :=$($(func)$(bar))
```

如果有变量是通过make的命令行参数设置的，那么Makefile文件中对这个变量的赋值会被忽略。

```
<target ...> : <variable-assignment>;

<target ...> : overide <variable-assignment> #overide可以使比纳凉不被命令行中覆盖。
```

模式变量将目标变量的目标设置为符合一种模式的变量：

# 条件

ifeq，ifneq，ifdef（判断变量是否为空，但是不会将变量展开），ifndef

make是在读取Makefile时就计算条件表达式的值，并根据条件表达式的值来选择语句，所以，你最好不要把自动化变量（如 `$@` 等）放入条件表达式中，因为自动化变量是在运行时才有的。

# 使用函数

[https://seisman.github.io/how-to-write-makefile/functions.html](https://seisman.github.io/how-to-write-makefile/functions.html)

将空格替换为，

```
comma:= ,
empty:=
space:=$(empty)$(empty)foo:= a b c
bar:=$(subst$(space),$(comma),$(foo))
```

### **patsubst**

**`$(**patsubst <pattern>,<replacement>,<text>**)**`

### **strip**

**`$(**strip <string>**)**`

- 名称：去空格函数。

### **findstring[¶](https://seisman.github.io/how-to-write-makefile/functions.html#findstring)**

**`$(**findstring <find>,<in>**)**`

### **filter[¶](https://seisman.github.io/how-to-write-makefile/functions.html#filter)**

**`$(**filter <pattern...>,<text>**)**`

### **filter-out（去除符合的**

**`$(**filter-out <pattern...>,<text>**)**`

### **sort[¶](https://seisman.github.io/how-to-write-makefile/functions.html#sort)（会去重**

**`$(**sort <list>**)**`

# make运行

- all:这个伪目标是所有目标的目标，其功能一般是编译所有的目标。
- clean:这个伪目标功能是删除所有被make创建的文件。
- install:这个伪目标功能是安装已编译好的程序，其实就是把目标执行文件拷贝到指定的目标中去。
- print:这个伪目标的功能是例出改变过的源文件。
- tar:这个伪目标功能是把源程序打包备份。也就是一个tar文件。
- dist:这个伪目标功能是创建一个压缩文件，一般是把tar文件压成Z文件。或是gz文件。
- TAGS:这个伪目标功能是更新所有的目标，以备完整地重编译使用。
- check和test:这两个伪目标一般用来测试makefile的流程。
- **`n`, `-just-print`, `-dry-run`, `-recon`**

不执行参数，这些参数只是打印命令，不管目标是否更新，把规则和连带规则下的命令打印出来，但不执行，这些参数对于我们调试makefile很有用处。

- **`t`, `-touch`**

这个参数的意思就是把目标文件的时间更新，但不更改目标文件。也就是说，make假装编译目标，但不是真正的编译目标，只是把目标变成已编译过的状态。

- **`q`, `-question`**

这个参数的行为是找目标的意思，也就是说，如果目标存在，那么其什么也不会输出，当然也不会执行编译，如果目标不存在，其会打印出一条出错信息。

- **`W <file>`, `-what-if=<file>`, `-assume-new=<file>`, `-new-file=<file>`**

这个参数需要指定一个文件。一般是是源文件（或依赖文件），Make会根据规则推导来运行依赖于这个文件的命令，一般来说，可以和“-n”参数一同使用，来查看这个依赖文件所发生的规则命令。

# 隐含规则

-r取消所有预设规则。