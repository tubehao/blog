---

title: Linux从零到一

published: 2024-10-31

description: '见识浅薄，请多指教'

image: ''

tags: ["tutorial"]

category: 'tools'

draft: false 

---

## 写在前面
linux系统操作对于cs乃至电类很大一部分专业的学生不可或缺，但是由于国内cs课程完全没有linux相关教学，笔者直到大三所接受过的课内linux教育也仅有oslab中顺口提到的一个视频，在新手入门linux的过程中往往会不知所措。因此写下这篇从零到一，之所以是从零到以一，只是希望你能够在读后对linux系统及其工具可以有初步地理解，可以从此在已有的一后面添加无数个零。本人对linux也绝非精通，只是会用，如有建议请开issue。


## 配置你的linux虚拟机
鉴于阅读这篇文章的同学多为小白，不会将linux作为主力机，配置虚拟机必不可少。现有的linux主要有三种方案：
+ wsl：较为推荐，避免虚拟机占用更多资源，linux系统可以直接使用win的文件
+ vmware：网上资源较多，优点是操作简单，可以直接拖拽文件到虚拟机，剪贴板可以共用，缺点是vmware tool有时候会出奇怪的bug
+ hyper-v：与vmware类似，不能拖拽文件交互性，不能共用剪贴板算是比较明显的缺点，但是优点是有更高的自由度（当然也可能是我对vmware的探索不够深），并且可以后台运行（任务栏少一个进程强迫症狂喜）

基于这三种方案的配置均有大量教程，这里不再赘述。

## 终端
linux的主要交互方式是终端，通过终端你可以用一行命令实现相当多且高的功能，这也是我喜欢linux的原因之一：让我更像电影里的黑客（bushi）。首先你应当掌握linux中的一些基本命令。参考missing-semester。

## ssh
如果你是用vmware或hyper-v配置了自己的虚拟机，那么我想你一定会为alt+tab快捷键的失效而感到苦恼。那么既然如前文所说，linux最重要的交互窗口其实是终端，我们可以通过vscode ssh到你的虚拟机上面，这样就可以实现浏览器gpt和你的代码页面的迅速切换（bushi）。那么这么人性化的操作应该如何进行呢？
1. 在虚拟机上启用ssh服务并关闭防火墙（否则可能导致无法连接）
2. [固定虚拟机ip（可选）](https://tubehao.github.io/blog/posts/hyper-v%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%9B%BA%E5%AE%9Aip%E5%B9%B6%E8%BF%9E%E6%8E%A5%E5%A4%96%E9%83%A8%E7%BD%91%E7%BB%9C%E6%96%B9%E5%BC%8F/)
3. 在windows "C:\Users\yourUserName\.ssh\config"中按照下列方式添加你的host
   ```
   Host anything
    HostName 虚拟机ip
    User 虚拟机用户名
   ```
4. [配置公钥私钥免密码登录（可选）](https://www.linuxdashen.com/ssh-key)
5. 在vscode中左侧的远程资源管理器连接
   
## git && github
git是相当强大的版本控制工具，github也名声在外。为了更好地使用这两种工具，首先你需要在自己的linux系统安装git，在本地的ssh生成公钥私钥对，将公钥添加到github的settings--SSH and GPG keys。这样你就可以轻松地使用git clone，git push等基本命令了。继续请参考：[git&github](https://tubehao.github.io/blog/posts/learning-gitgithub/)

## tmux
tmux可以让你更好地管理自己的终端页面，也可以轻松地让任务在后台运行。继续请参考：[tmuxcheatsheet](https://tubehao.github.io/blog/posts/tmux/)。

## conda
conda帮助你更好地管理你的环境（比如python版本）[参考](https://blog.csdn.net/wyf2017/article/details/118676765)

## 未完待续——想起来啥就加啥