---

title: cmd网络代理

published: 2024-11-02

description: 'rt'

image: ''

tags: [tutorial]

category: 'tools'

draft: false 

---

clash默认监听端口为7890

在cmd中输入
```
set http_proxy=http://127.0.0.1:7890
set https_proxy=http://127.0.0.1:7890

如有密码
set http_proxy_user=
set http_proxy_pass=
```

如果希望永久更改，需要在系统环境变量中修改。