---

title: 服务器使用rclone上传文件到google drive

published: 2024-11-02

description: 'rt'

image: ''

tags: [tutorial]

category: 'tools'

draft: false 

---
参考https://www.howtogeek.com/451262/how-to-use-rclone-to-back-up-to-google-drive-on-linux/
## 服务器安装并运行rclone

下载路径：https://rclone.org/downloads/

运行
```
rclone config
```
按照https://rclone.org/drive/配置
在这一步选择N
> Use web browser to automatically authenticate rclone with remote?
>    * Say Y if the machine running rclone has a web browser you can use
>    * Say N if running rclone on a (remote) machine without web browser access
>    If not sure try Y. If Y failed, try N.
在windows下载rclone，解压后在当前文件夹运行cmd，运行服务器上要求的指令，按照要求获得并输入token。（如果一直timeout，请配置[cmd网络代理](https://tubehao.github.io/blog/posts/cmd网络代理/)）
```
rclone ls remote:datasets
rclone copy local_path remote:datasets
```

