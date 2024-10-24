---

title: tmux

published: 2024-10-24

description: 'rt'

image: ''

tags: ["tutorial", "tips"]

category: 'tools'

draft: false 

---

参考链接 [https://www.ruanyifeng.com/blog/2019/10/tmux.html](https://www.ruanyifeng.com/blog/2019/10/tmux.html)

`tmux`进入窗口

`Ctrl+`b后可以使用快捷键，类似vim的esc

`tmux new -s name`新建会话

`Ctrl+b d`退出，但仍保持后台运行

`tmux ls`

`tmux attach -t 0/name`接入

`tmux kill-session -t 0/name`杀死

`tmux switch -t 0`切换

`tmux rename-session -t 0 newname`

- `Ctrl+b d`：分离当前会话。
- `Ctrl+b s`：列出所有会话。
- `Ctrl+b $`：重命名当前会话。
1. **水平分割窗格**：如果您的窗口中只有一个面板，您可以使用以下快捷键将其水平分割成两个面板：
    
    ```css
    cssCopy code
    Ctrl-b %（按住 Ctrl 键，然后按 b 键，接着按 % 键）
    ```
    
    这会在当前面板的下方创建一个新的水平分割面板。
    
2. **垂直分割窗格**：您还可以使用以下快捷键将窗格垂直分割成两个面板：
    
    ```css
    cssCopy code
    Ctrl-b "（按住 Ctrl 键，然后按 b 键，接着按 " 键）
    ```
    
    这会在当前面板的右侧创建一个新的垂直分割面板。
    
3. **切换到上一个窗格**：要在不同的窗格之间切换，您可以使用以下快捷键：
    
    ```css
    cssCopy code
    Ctrl-b 方向键（上、下、左、右箭头键）
    ```
    
    按住 Ctrl 键，然后按方向键以切换到相邻的窗格。
    
4. **切换到指定窗格**：如果有多个窗格，您还可以使用以下快捷键切换到特定的窗格：
    
    ```css
    cssCopy code
    Ctrl-b q（按住 Ctrl 键，然后按 q 键）
    ```
    
    这会在窗格上显示数字标签，然后您可以输入相应数字以切换到对应的窗格。
    
5. **关闭当前窗格**：如果要关闭当前的窗格，可以使用以下快捷键：
    
    ```css
    cssCopy code
    Ctrl-b x（按住 Ctrl 键，然后按 x 键）
    ```
    

```bash
# 划分上下两个窗格$ tmux split-window
# 划分左右两个窗格$ tmux split-window -h# 光标切换到上方窗格$ tmux select-pane -U# 当前窗格上移$ tmux swap-pane -Utmux new-window
# 新建一个指定名称的窗口$ tmux new-window -n <window-name>tmux select-window -t <window-number>$ tmux rename-window <new-name>
```

```bash
Ctrl+b c：创建一个新窗口，状态栏会显示多个窗口的信息。
Ctrl+b p：切换到上一个窗口（按照状态栏上的顺序）。
Ctrl+b n：切换到下一个窗口。
Ctrl+b <number>：切换到指定编号的窗口，其中的<number>是状态栏上的窗口编号。
Ctrl+b w：从列表中选择窗口。
Ctrl+b ,：窗口重命名。
```

```jsx
# 列出所有快捷键，及其对应的 Tmux 命令
$ tmux list-keys
# 列出所有 Tmux 命令及其参数
$ tmux list-commands
# 列出当前所有 Tmux 会话的信息
$ tmux info
# 重新加载当前的 Tmux 配置
$ tmux source-file ~/.tmux.conf
```