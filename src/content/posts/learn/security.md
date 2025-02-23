---

title: 安全和密码学

published: 2025-02-23

description: 'rt'

image: ''

tags: [notes]

category: 'learn'

draft: false 

---

#  [安全和密码学](https://missing-semester-cn.github.io/2020/security/)
熵等于`log_2(所有可能的个数，即n)`

## 对称密码学

```
openssl aes-256-cbc -salt -in afile -out secfile #加密文件

openssl aes-256-cbc -d -in secfile -out notsafefile #解密
```

## 非对称密码学：

```
keygen() -> (public key, private key)  (这是一个随机方法)

encrypt(plaintext: array<byte>, public key) -> array<byte>  (输出密文)
decrypt(ciphertext: array<byte>, private key) -> array<byte>  (输出明文)

sign(message: array<byte>, private key) -> array<byte>  (生成签名)
verify(message: array<byte>, signature: array<byte>, public key) -> bool  (验证签名是否是由和这个公钥相关的私钥生成的)
```

## SSH

我们在[之前的一堂课](https://missing-semester-cn.github.io/2020/command-line/#remote-machines)讨论了SSH和SSH密钥的使用。那么我们今天从密码学的角度来分析一下它们。

当你运行`ssh-keygen`命令，它会生成一个非对称密钥对：公钥和私钥`(public_key, private_key)`。
生成过程中使用的随机数由系统提供的熵决定。这些熵可以来源于硬件事件(hardware
events)等。 公钥最终会被分发，它可以直接明文存储。
但是为了防止泄露，私钥必须加密存储。`ssh-keygen`命令会提示用户输入一个密码，并将它输入密钥生成函数
产生一个密钥。最终，`ssh-keygen`使用对称加密算法和这个密钥加密私钥。

在实际运用中，当服务器已知用户的公钥（存储在`.ssh/authorized_keys`文件中，一般在用户HOME目录下），尝试连接的客户端可以使用非对称签名来证明用户的身份——这便是[挑战应答方式](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication)。
简单来说，服务器选择一个随机数字发送给客户端。客户端使用用户私钥对这个数字信息签名后返回服务器。
服务器随后使用`.ssh/authorized_keys`文件中存储的用户公钥来验证返回的信息是否由所对应的私钥所签名。这种验证方式可以有效证明试图登录的用户持有所需的私钥。
