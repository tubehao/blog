---

title: git & github

published: 2024-04-19

description: 'rt'

image: ''

tags: [notes]

category: 'tools'

draft: false 

---

> 最近跟同学做项目，由于不懂git，多次搞乱分支，遂存档教程留念
> 

[https://git-scm.com/book/zh/v2](https://git-scm.com/book/zh/v2)

## 命令行接口

为了避免重复信息，我们将不会详细解释以下命令行。强烈推荐您阅读 [Pro Git
中文版](https://git-scm.com/book/zh/v2)或可以观看本讲座的视频来学习。

### 基础

- `git log --all --graph --decorate`:
  可视化历史记录（有向无环图）
- `git diff <filename>`: 显示与暂存区文件的差异
- `git diff <revision> <filename>`:显示某个文件两个版本之间的差异

### 分支和合并

- `git branch`: 显示分支
- `git branch <name>`: 创建分支
- `git checkout -b <name>`: 创建分支并切换到该分支。相当于 `git branch <name>; git checkout <name>`
- `git merge <revision>`: 合并到当前分支
- `git mergetool`: 使用工具来处理合并冲突
- `git rebase`: 将一系列补丁变基（rebase）为新的基线
- `git branch --set-upstream-to=<remote>/<remote branch>`:创建本地和远端分支的关联关系
-  `git checkout -b newBranch  origin/newBranch` 新建一个分直跟踪远程分支。加入-B可选参数后会强制创建新分支，并且会覆盖原来存在的同名分支。
-  `git checkout [<commit id>] [--] <paths> `主要用于检出某一个指定文件。如果不填写commit id，则默认会从暂存区检出该文件，如果暂存区为空，则该文件会回滚到最近一次的提交状态。
- `git checkout --orphan <new_branch>`基于当前所在分支新建一个赤裸裸的分支，没有任何的提交历史，但是当前分支的内容一一俱全。新建的分支，严格意义上说，还不是一个分支，因为HEAD指向的引用中没有commit值，只有在进行一次提交后，它才算得上真正的分支。
- `git checkout -p <branch>`主要用来比较两个分支间的差异内容，并提供交互式的界面来选择进一步的操作。这个命令不仅可以比较两个分支间的差异，还可以比较单个文件的差异
- 如果你之前已经跟踪了 **`.gitignore`** 中指定的文件，你需要先从跟踪状态中移除这些文件，这样它们才会被忽略。可以使用命令 **`git rm --cached [文件名]`** 来移除跟踪状态。

### 撤销

- `git commit --amend`: 编辑提交的内容或信息
- `git reset HEAD <file>`: 恢复暂存的文件
- `git checkout -- <file>`: 丢弃修改
- `git restore`: git2.32版本后取代git reset
  进行许多撤销操作

#### git reset:

**如果您想要在历史中保留一个“撤销”的记录，可以使用 git revert <commit>。这将创建一个新的提交，它撤销指定提交的更改。**

1. **重置暂存区（Staging Area）**：将暂存区的更改回退到工作区。
2. **重置工作区（Working Directory）**：放弃工作区的更改。
3. **重置提交历史（Commit History）**：更改提交历史。

**使用方式**

1. **`git reset [file]`**：

   - 仅重置指定文件到暂存区，不影响工作区和提交历史。

2. **`git reset`**：

   - 不带任何参数，默认重置整个暂存区，不影响工作区和提交历史。

3. **`git reset --soft [commit]`**：

   - 重置当前分支的头部到指定提交，但保留暂存区和工作区，不更改工作区的文件。
   - 可以用来撤销一次提交，但保留更改以便重新提交。

4. **`git reset [commit]`** 或 **`git reset --mixed [commit]`**：

   - 默认为 **`-mixed`**，重置暂存区与指定提交一致，但保留工作区的更改。
   - 适合于撤销添加到暂存区的更改并重新编辑。

5. **`git reset --hard [commit]`**：

   - 完全重置暂存区和工作区，使其与指定提交一致。
   - 这会丢失所有自该提交以来在暂存区和工作区的更改。
   - 应谨慎使用，特别是在共享分支上。

6. **`git reset --keep [commit]`**：

   - 与 **`-hard`** 类似，但在有未提交的本地更改时不会进行重置，以避免丢失更改。

   


### 删除分支

git branch -d <branch-name>：当分支已经完全合并到其上游分支时使用。
git branch -D <branch-name>：当分支尚未合并到其上游分支时使用。请谨慎使用，因为这可能会永久丢失未合并的更改。

### 更多

- `git clone --depth=1`: 浅克隆（shallow clone），不包括完整的版本历史信息
- `git add -p`: 交互式暂存
- `git rebase -i`: 交互式变基
- `git blame`: 查看最后修改某行的人
- `git bisect`: 通过二分查找搜索历史记录
- git log --oneline --graph
- git diagose [storage.py](http://storage.py/)
- 删除未跟踪的文件 git clean -f

## 进阶内容

### git stash

git stash save ""

git stash list, git stash show stash@{0}默认展示第一个

git stash会显示在log中，可以自由在不同的分支间切换。

### git blame

显示每一行代码的最后修改记录。它可以帮助你追踪代码的变更历史，了解每一行代码是谁在什么时间进行的修改。这对于调试、代码审查和理解代码变更的背景非常有用。

git blame [options] <file>

- **`-L <start>,<end>`**:仅显示文件中从 `<start>` 行到 `<end>` 行的变更记录。
- **`-C`**:追踪代码块的移动和复制。
- **`-M`**:追踪代码块的移动。
- **`-w`**:忽略空白字符的变化。

### git filter-branch

一个强大的 Git 命令，用于在历史提交中进行批量重写。它可以用于修改、删除或重写 Git 历史中的提交记录。以下是该命令的详细解释：

1. **`git filter-branch`**:
   - 这是一个用于重写 Git 历史的命令。它可以对多个提交进行批量修改。
2. **`--force`**:
   - 强制执行命令，即使在某些情况下 Git 会建议不要这样做。它会覆盖已经存在的重写历史。
3. **`--index-filter`**:
   - 这是一个用于修改索引（暂存区）的过滤器。它只对索引进行操作，比 `--tree-filter` 更快，因为它不需要检出工作树。
4. **`'git rm --cached --ignore-unmatch ./my_password'`**:
   - 这是一个在index filter中执行的命令：
     - `git rm --cached`：从索引中删除文件，但不删除工作目录中的文件。
     - `--ignore-unmatch`：如果文件不存在，则忽略错误。
     - `./my_password`：指定要删除的文件路径。在这个例子中，它是 `./my_password`。
5. **`--prune-empty`**:
   - 删除任何在过滤过程中变为空的提交。这样可以保持历史的整洁性。
6. **`--tag-name-filter cat`**:
   - 这个选项用于处理标签。`cat` 表示保留标签名不变。
7. **`-- --all`**:
   - 这部分指定过滤的范围：
     - `--`：表示选项的结束，接下来的参数是要处理的分支或其他引用。
     - `--all`：表示对所有引用（包括所有分支和标签）进行操作。

这个命令的整体作用是：

- 在 Git 仓库的所有历史记录中，强制删除索引中名为 `./my_password` 的文件。
- 如果某个提交在删除该文件后变为空提交，则该提交也会被删除。
- 标签名称保持不变。
- 该操作会影响仓库中的所有分支和标签。

### 缩写

参考 [https://www.atlassian.com/blog/git/advanced-git-aliases](https://www.atlassian.com/blog/git/advanced-git-aliases)

```
caa = commit -a --amend -C HEAD
```

Which will take all uncommitted and un-staged changes currently in the working directory and add them to the previous commit, amending it before pushing the change up

`my_alias = "!f() { 〈your complex command〉 }; f"`

类似脚本的写法。

`new = !sh -c 'git log $1@{1}..$1@{0} "$@"'`

Which will list to screen all new commits have been created with the previous pull.

### git subtree add

假设你正在处理一个项目，并希望将另一个 Git 仓库作为子目录引入你的项目中。以下是这个过程的步骤：

**示例场景**

- 你的主项目名为 **`MyMainProject`**。
- 你想添加的外部仓库的 URL 是 **`https://github.com/example/ExternalLibrary.git`**。
- 你想将这个外部仓库的内容添加到 **`MyMainProject`** 的 **`external_lib`** 子目录中。
- 你想引入外部仓库的 **`master`** 分支。

**具体步骤**

1. **打开终端**：打开你的命令行工具。
2. **导航到你的项目目录**：
   
    ```bash
    cd path/to/MyMainProject
    ```
    
3. **添加外部仓库作为子树**：
   
    ```bash
    git subtree add --prefix=external_lib https://github.com/example/ExternalLibrary.git master --squash
    ```
    
    解释：
    
    - **`-prefix=external_lib`** 指定了外部仓库内容将被放置在你的项目中的哪个子目录。
    - **`https://github.com/example/ExternalLibrary.git`** 是外部仓库的 URL。
    - **`master`** 指定了你要添加的分支。
    - **`-squash`** 是一个可选项，它会将外部仓库的所有提交压缩成一个提交，以简化你的项目历史。
4. **执行命令后**，Git 会将指定仓库的内容添加到 **`MyMainProject`** 的 **`external_lib`** 目录中，并创建一个新的提交来记录这个变更。
5. **查看子目录**：你现在可以在 **`MyMainProject`** 的 **`external_lib`** 目录中看到外部仓库的内容。

## git lfs大文件

[https://zhuanlan.zhihu.com/p/146683392](https://zhuanlan.zhihu.com/p/146683392)

## `git push <remote> <branch>`

其中 <remote> 是远程仓库的名称，<branch> 是你要推送的本地分支的名称。但 git push 命令还可以带有一些可选参数，以控制其行为。以下是常用的 git push 参数：

**-u（或 --set-upstream）：**用于将当前分支与远程分支关联起来，使得后续的 git push 或 git pull 命令可以不需要指定分支名称。
**--force（或 -f）**：这个参数用于强制推送
**--all：**这个参数用于推送所有分支到远程仓库。例如：git push --all origin 将所有本地分支推送到远程仓库。

**--tags：**这个参数用于推送标签（tags）到远程仓库。例如：git push --tags origin 将所有本地标签推送到远程仓库。

**-n（或 --dry-run）：模拟推送操作，不会实际执行推送，但会显示将要推送的更改。**

### 删除远程分支

你可以使用 git push 命令来向远程仓库推送一个空分支，这会导致远程分支被删除。具体步骤如下：
假设要删除名为 remote-branch 的远程分支。
git fetch --prune获取最新的远程分支信息
这将从远程仓库获取最新的分支信息，并清理掉已经不存在的远程分支。
git push origin :remote-branch

### 将远程分支连接到本地仓库

检查可用的远程分支： 首先，你可以运行以下命令来查看远程仓库中存在哪些分支
git ls-remote --heads <remote>将 <remote> 替换为你的远程仓库的名称（通常是 origin）。这会列出所有远程分支的引用。
创建本地分支并关联远程分支： 一旦你知道了要连接的远程分支的名称，你可以使用以下命令创一个本地分支并将其关联到远程分支：
git checkout -b <local-branch-name> <remote>/<remote-branch-name>

## 查看分支的合并状态

1. 使用 git branch -r --merged这个命令会显示所有已经合并到当前分支的远程分支
2. 使用 git log
您也可以检查特定分支的提交是否出现在另一个分支中：
git fetch origin
git log origin/<your-branch>..origin/<target-branch>
使用 git fetch origin 获取最新的远程仓库数据。
使用 git log 命令比较两个分支。如果输出为空，那么 <your-branch> 上的提交已经存在于 <target-branch> 上。
3. 使用 git merge-base
另一种方法是使用 git merge-base：
bashCopy code
git fetch origin
git merge-base origin/<your-branch> origin/<target-branch>
这将显示两个分支的最近公共祖先。然后您可以用这个提交哈希与两个分支的最新提交进行比较。
注意事项
在执行这些命令之前，请确保您的本地仓库与远程仓库是同步的，这可以通过 git fetch origin 实现。
检查分支是否已经被合并到远程分支时，您需要明确知道您要比较的是哪两个分支。
这些命令对于理解分支之间的关系非常有用，尤其是在处理复杂的合并和分支策略时。
git branch --merged





