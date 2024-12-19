---

title: tmux cheatsheet

published: 2024-10-24

description: 'rt'

image: ''

tags: ["tutorial", "cheatsheet"]

category: 'tools'

draft: false 

---

## tmux 使用教程

### 参考链接

- [阮一峰的网络日志](https://www.ruanyifeng.com/blog/2019/10/tmux.html)
- [ipads指南](https://sjtu-ipads.github.io/OS-Course-Lab/Appendix/toolchains/tmux.html)

`tmux` 是一个开源的终端复用器，允许用户在一个终端窗口中访问多个独立的终端会话。

### 命令

- `tmux new -s name`：新建一个命名的会话。
- `tmux ls`：列出所有会话。
- `tmux attach -t 0/name`：接入编号为0或名称为`name`的会话。
- `tmux kill-session -t 0/name`：杀死编号为0或名称为`name`的会话。
- `tmux switch -t 0`：切换到编号为0的会话。
- `tmux rename-session -t 0 newname`：将编号为0的会话重命名为`newname`。
- `tmux split-window`：垂直分割窗格。
- `tmux split-window -h`：水平分割窗格。
- `tmux select-pane -U`：光标切换到上方窗格。
- `tmux swap-pane -U`：当前窗格上移。
- `tmux new-window`：新建一个窗口。
- `tmux new-window -n <window-name>`：新建一个指定名称的窗口。
- `tmux select-window -t <window-number>`：切换到指定编号的窗口。
- `tmux rename-window <new-name>`：重命名当前窗口。
- `tmux list-keys`：列出所有快捷键及其对应的 `tmux` 命令。
- `tmux list-commands`：列出所有 `tmux` 命令及其参数。
- `tmux info`：列出当前所有 `tmux` 会话的信息。
- `tmux source-file ~/.tmux.conf`：重新加载当前的 `tmux` 配置。

### 快捷键

- `Ctrl+b d`：分离当前会话。
- `Ctrl+b s`：列出所有会话。
- `Ctrl+b $`：重命名当前会话。
- `Ctrl+b %`：水平分割窗格。
- `Ctrl+b "`：竖直分割窗格。
- `Ctrl+b u/d/l/r`：在窗格中上下左右移动光标。

### 窗口操作

- `Ctrl+b c`：创建一个新窗口，状态栏会显示多个窗口的信息。
- `Ctrl+b p`：切换到上一个窗口（按照状态栏上的顺序）。
- `Ctrl+b n`：切换到下一个窗口。
- `Ctrl+b number`：切换到指定编号的窗口，其中的`<number>`是状态栏上的窗口编号。
- `Ctrl+b w`：从列表中选择窗口。
- `Ctrl+b ,`：窗口重命名。
