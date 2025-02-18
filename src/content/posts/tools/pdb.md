---

title: pdb--python调试器

published: 2024-11-02

description: 'rt'

image: ''

tags: [tutorial]

category: 'tools'

draft: false 

---

```
import pdb; pdb.set_trace()
```

打断点

1. `l(ist)` - 展示当前行附近的 11 行，或者是继续之前的展示。

   ll来看当前函数或者是堆栈帧的源代码；l 1, 13查看范围内行

2. `s(tep)` - 执行当前行，会停在第一个可能停下的地方。（step in）

3. `n(ext)` - 继续执行程序到当前函数的下一行或者是到它返回结果。

4. `b(reak)` - 设置程序断点 (取决于提供的参数)。b+行数，不加参数显示已有断点

5. `r(eturn)` - 继续执行到当前函数返回结果。

6. h（elp）查看所有参数

7. continue

8.  `cl(ear)` 命令删除断点，没有参数删除所有断电

可以在 `pdb` 中用 `len` 方法来看看 `runner.dice` 的长度	`dir(die)`查看属性和方法，die.value查看某个属性的值，如果你想要换行符 `\n` 真的有换行的效果，你可以执行 `print(die.show())`

`! `命令是在告诉 `pdb` 接下来的语句是 Python 命令而不是 `pdb` 命令。在带有变量名为 `c` 的 `run()` 方法中这很有用。就像我在教程一开始说的，直接在 `pdb` 里面输入 `c` 会被认为是要执行 `continue` 指令。进入 `pdb` 调试模式，停在 `runner.py` 的 `:26` 行，然后可以运行 `!c` 看看会发生什么。

```
>>> import pdb
>>> from main import main
>>> main()
[...]
Would you like to play again?[Y/n]: Y
Traceback (most recent call last):
  File "main.py", line 12, in <module>
    main()
  File "main.py", line 8, in main
    GameRunner.run()
  File "/Users/Development/pdb-tutorial/dicegame/runner.py", line 62, in run
    i_just_throw_an_exception()
  File "/Users/Development/pdb-tutorial/dicegame/utils.py", line 13, in i_just_throw_an_exception
    raise UnnecessaryError("You actually called this function...")
dicegame.utils.UnnecessaryError: You actually called this function...
```

pdb.pm()停在了最后一个抛出异常的地方的前面。在这里我们可以在程序出错之前检查程序的状态。

也可以使用 `python -m pdb main.py` 来启动 `main.py` 脚本，然后输入 `continue` 运行程序直到抛出异常，Python 会在没有捕获的异常上自动进入 `post_mortem` 模式。