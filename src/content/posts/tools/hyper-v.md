---

title: hyper-v虚拟机固定ip并连接外部网络方式

published: 2024-10-24

description: 'rt'

image: ''

tags: [tutorial]

category: 'tools'

draft: false 

---

参考[https://blog.gwlin.com/posts/6260](https://blog.gwlin.com/posts/6260)

# 固定虚拟机ip

1. 创建虚拟交换机管理器，给虚拟机添加这个虚拟交换机管理器，选择内部类型
2. 在宿主机设置中找到这个网络适配器，设置一个合适的ip
3. 在虚拟机的网络配置中配置对应网卡的设置，以kali为例
    1. sudo nano /etc/network/interfaces
    
    ```jsx
    auto eth0
    iface eth0 inet static
    address 192.168.1.100    # 设置固定IP地址（随意，但是要跟你的宿主机在同一网段）
    netmask 255.255.255.0    # 子网掩码
    gateway 192.168.1.1      # 默认网关（你设置的宿主机的ip）
    dns-nameservers 8.8.8.8  # DNS服务器地址
    
    ```
    
    b. sudo systemctl restart networking
    
4. 现在，你应该可以在宿主机ping通你的虚拟机了

# 访问外部网络

## 桥接

选择你上网的网卡，点开属性，共享，共享给你的虚拟网络适配器，弹窗会提示你宿主机在这个子网段下的ip，这个应该与你上面的默认网关相同

测试连接

## 利用hyper-v

给虚拟机再添加一个网卡，选择default-switch模式，修改虚拟机路由。

通过ip route查看路由表，通过

sudo ip route del default via <other_gateway> dev <other_interface>

删除不需要的路由（也即在固定ip过程中配置的网卡）

添加默认路由：

sudo ip route add default via 172.26.143.255 dev eth1

测试连接

---

注意，windows防火墙会组织ping的请求，请检查你的防火墙设置