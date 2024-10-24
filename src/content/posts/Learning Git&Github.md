---

title: git & github

published: 2024-04-19

description: 'rt'

image: ''

tags: [notes]

category: 'study'

draft: false 

---

> 最近跟同学做项目，由于不懂git，多次搞乱分支，遂存档教程留念
> 

## 进阶内容

参考 [https://www.atlassian.com/blog/git/advanced-git-aliases](https://www.atlassian.com/blog/git/advanced-git-aliases)

```
caa = commit -a --amend -C HEAD
```

Which will take all uncommitted and un-staged changes currently in the working directory and add them to the previous commit, amending it before pushing the change up

`my_alias = "!f() { 〈your complex command〉 }; f"`

类似脚本的写法。

`new = !sh -c 'git log $1@{1}..$1@{0} "$@"'`

Which will list to screen all new commits have been created with the previous pull.

**`git subtree add`** 命令

假设你正在处理一个项目，并希望将另一个 Git 仓库作为子目录引入你的项目中。以下是这个过程的步骤：

### **示例场景**

- 你的主项目名为 **`MyMainProject`**。
- 你想添加的外部仓库的 URL 是 **`https://github.com/example/ExternalLibrary.git`**。
- 你想将这个外部仓库的内容添加到 **`MyMainProject`** 的 **`external_lib`** 子目录中。
- 你想引入外部仓库的 **`master`** 分支。

### **具体步骤**

1. **打开终端**：打开你的命令行工具。
2. **导航到你的项目目录**：
    
    ```bash
    bashCopy code
    cd path/to/MyMainProject
    
    ```
    
3. **添加外部仓库作为子树**：
    
    ```bash
    bashCopy code
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

# git push <remote> <branch>

其中 <remote> 是远程仓库的名称，<branch> 是你要推送的本地分支的名称。但 git push 命令还可以带有一些可选参数，以控制其行为。以下是常用的 git push 参数：

**-u（或 --set-upstream）：**

这个参数用于将当前分支与远程分支关联起来，使得后续的 git push 或 git pull 命令可以不需要指定分支名称。

例如：git push -u origin main 将本地的 main 分支推送到远程仓库，并将远程的 main 分支与本地的

main 分支关联起来。
**--force（或 -f）**：

这个参数用于强制推送，即覆盖远程仓库上的提交历史。谨慎使用，因为它可能会丢失远程仓库上

提交。

例如：git push --force origin main 将强制推送本地的 main 分支，覆盖远程的 main 分支。
**--all：**

这个参数用于推送所有分支到远程仓库。

例如：git push --all origin 将所有本地分支推送到远程仓库。

**--tags：**

这个参数用于推送标签（tags）到远程仓库。

例如：git push --tags origin 将所有本地标签推送到远程仓库。

**-n（或 --dry-run）：**

这个参数用于模拟推送操作，不会实际执行推送，但会显示将要推送的更改。
例如：git push -n origin main 将模拟推送操作，但不会真正推送更改。

## 删除远程分支

你可以使用 git push 命令来向远程仓库推送一个空分支，这会导致远程分支被删除。具体步骤如下：
假设要删除名为 remote-branch 的远程分支。
git fetch --prune       获取最新的远程分支信息
这将从远程仓库获取最新的分支信息，并清理掉已经不存在的远程分支。
git push origin :remote-branch

## 将远程分支连接到本地仓库

检查可用的远程分支： 首先，你可以运行以下命令来查看远程仓库中存在哪些分支：
git ls-remote --heads <remote>
将 <remote> 替换为你的远程仓库的名称（通常是 origin）。这会列出所有远程分支的引用。
创建本地分支并关联远程分支： 一旦你知道了要连接的远程分支的名称，你可以使用以下命令创

一个本地分支并将其关联到远程分支：
**git checkout -b <local-branch-name> <remote>/<remote-branch-name>**

**git push --set-upstream origin main:gh 将main连接到gh上**
拉取远程分支的内容（可选）： 一旦你连接了本地分支和远程分支，你可以选择拉取远程分支的内容到本地分支。这可以确保你的本地分支与远程分支保持同步：
git pull origin main
这会将远程的 main 分支的内容拉取到你的本地 main 分支上。
现在，你已经成功将远程分支连接到了本地仓库，并创建了一个与之关联的本地分支。你可以在本地分支上工作，并使用 git pull 和 git push 来与远程分支进行交互。

## 删除未跟踪的文件

git clean -f

## 切换到 front 分支

git checkout front

## 撤销提交

### 使用 git reset:

软重置（Soft Reset）：git reset --soft HEAD~1 会撤销最近的提交，但保留更改在暂存区。
混合重置（Mixed Reset）：git reset --mixed HEAD~1 或简单地 git reset HEAD~1 会撤销最近的提交，并将更改放回工作区。
硬重置（Hard Reset）：git reset --hard HEAD~1 会彻底撤销最近的提交，包括更改。
使用 git revert:
如果您想要在历史中保留一个“撤销”的记录，可以使用 git revert <commit>。这将创建一个新的提交，它撤销指定提交的更改。

## 删除分支

删除本地分支:
git branch -d <branch-name>：当分支已经完全合并到其上游分支时使用。
git branch -D <branch-name>：当分支尚未合并到其上游分支时使用。请谨慎使用，因为这可能会永久丢失未合并的更改。

## 查看分支的合并状态

1. 使用 git branch -r --merged
这个命令会显示所有已经合并到当前分支的远程分支：
bashCopy code
git checkout <your-branch>
git fetch origin
git branch -r --merged
首先切换到您关心的分支。
然后使用 git fetch origin 来获取最新的远程仓库数据。
使用 git branch -r --merged 查看哪些远程分支已经合并到您当前的分支。
如果您的分支名出现在列表中，这意味着它已经被合并到当前分支。
2. 使用 git log
您也可以检查特定分支的提交是否出现在另一个分支中：
bashCopy code
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

## 常用命令

git log --oneline --graph

git diagose [storage.py](http://storage.py/)

## git reset

**`git reset`** 是 Git 中一个非常强大且多用途的命令，用于撤销更改。它有几种不同的使用方式，取决于你想要达到的目标。**`git reset`** 主要用于三个方面：

1. **重置暂存区（Staging Area）**：将暂存区的更改回退到工作区。
2. **重置工作区（Working Directory）**：放弃工作区的更改。
3. **重置提交历史（Commit History）**：更改提交历史。

### **使用方式**

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
    
    ## ignore something
    
    在 Git 中，要忽略某些文件的提交，通常是通过使用 **`.gitignore`** 文件来实现的，而不是通过一个特定的命令。**`.gitignore`** 文件允许你指定一个模式，Git 会根据这个模式来忽略不需要添加到版本控制中的文件和目录。
    
    ### **创建和配置 `.gitignore` 文件**
    
    1. **创建 `.gitignore` 文件**：
        - 在仓库的根目录下创建一个名为 **`.gitignore`** 的文件。
    2. **配置忽略规则**：
        - 打开 **`.gitignore`** 文件，在文件中添加要忽略的文件或目录的规则。
        - 每一行指定一个模式，比如：
            - **`.log`** 会忽略所有扩展名为 **`.log`** 的文件。
            - **`temp/`** 会忽略名为 **`temp`** 的目录及其所有子文件和子目录。
            - **`!important.log`** 会重新包含（不忽略）名为 **`important.log`** 的文件，即使前面的规则指定忽略 **`.log`** 文件。
    3. **将 `.gitignore` 文件添加到仓库**：
        - 将 **`.gitignore`** 文件添加到仓库并提交。
            
            ```bash
            bashCopy code
            git add .gitignore
            git commit -m "Add .gitignore file"
            ```
            
    
    ### **注意事项**
    
    - **`.gitignore`** 文件应该提交到仓库中，这样所有协作者都可以使用相同的忽略规则。
    - 如果你之前已经跟踪了 **`.gitignore`** 中指定的文件，你需要先从跟踪状态中移除这些文件，这样它们才会被忽略。可以使用命令 **`git rm --cached [文件名]`** 来移除跟踪状态。
    - **`.gitignore`** 只能忽略那些原本未被跟踪（即未被添加到仓库中）的文件。如果文件已经被提交到仓库中，修改 **`.gitignore`** 是不会影响它的。
    
    使用 **`.gitignore`** 文件是管理 Git 仓库中不需要版本控制的文件的标准做法。通过正确配置这个文件，你可以避免不必要的文件被提交到仓库中，从而保持仓库的整洁。