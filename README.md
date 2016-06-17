# Linux 服务器透明翻墙

## 目标和资源依赖

### 目标

在少量服务器上实现透明翻墙. 少量的含义是，本文介绍的方法需要一台家用路由器，每台服务器都要通过网线连接到路由器的一个 LAN 口, 而 LAN 口数量一般是 4 个，所以只适用于不多于 4 台的服务器. 透明翻墙是指, 服务器无需配置翻墙客户端, 或者通过环境变量来设置代理服务器, 而是自动的翻墙访问国外 IP.

### 资源依赖

- 境外 VPS 一个 (能高速稳定的访问 Google, quay.io, AWS 等被墙网站)
- 可以刷集成 Shadowsocks 客户端的固件的路由器一台
- 所有服务器拥有有双网卡

## 搭建 Shadowsocks 服务器

首先购买境外 VPS, 可以选择 [bandwagonhost](https://bandwagonhost.com), [linode](https://www.linode.co://www.linode.com), [dititalocean](https://www.digitalocean.com) 等。购买后得到一台虚拟主机和一个 IP，虚拟主机上可以安装主流的 Linux 操作系统。
登录 VPS 后, 执行以下命令安装 Shadowsocks 服务器程序:

```bash
# 这里我们选择 Go 版本的 shadowsocks , 对多客户端并发支持比较好
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-go.sh
chmod +x shadowsocks-go.sh
./shadowsocks-go.sh 2>&1 | tee shadowsocks-go.log

# 根据提示填入必要参数后会安装成功
Congratulations, shadowsocks-go install completed!
Your Server IP:your_server_ip
Your Server Port:your_server_port
Your Password:your_password
Your Local Port:1080
Your Encryption Method:aes-256-cfb

Welcome to visit:https://teddysun.com/392.html
Enjoy it!
```

## 路由器刷集成 Shadowsocks 客户端的固件

我们使用的是 NetGear R7000 型号的路由器, 刷的是 merlin 改版固件, 具体过程如下 (参考的是[本文](http://post.smzdm.com/p/51938/)), 步骤如下: (TODO: 将步骤写在这里, 以免引用的网络论坛的贴子失效)

根据 VPS 上生成的 server_ip 、port、password、加密方式填写, 模式选择大陆白名单模式 (所有国外流量自动走代理, 不用维护代理列表).

![router](./images/2016-06-02_11-48-14.png)

## 配置服务器

### 网络结构

我们在 3 台安装了 CoreOS 系统的服务器上实现了透明翻墙，网络结构如下图所示：

![network_config](./images/2016-05-31_20-49-31.png)

### 配置系统静态路由

由于三台服务器出口接入翻墙路由器使用的 NAT 网络模式经过一次地址转换, 同一网络无法访问, 所以使用双网卡策略. 通过添加静态路由策略, 使服务器访问外网走翻墙路由器, 内网服务器网关.

### 配置系统自动加载静态路由

在每台服务器的 `/etc/systemd/network` 目录下分别创建 `10-static.network` 和 `20-dhcp.network` 两个文件

```bash
# 创建 10-static.network
vim 10-static.network
---
[Match]
Name=eno1

[Network]
Address=10.10.10.192/24

[Route]
Gateway=10.10.10.254
Destination=10.200.0.0/16

[Route]
Gateway=10.10.10.254
Destination=192.169.100.0/24

# 创建 20-dhcp.network
vim 20-dhcp.network
---
[Match]
Name=en*

[Network]
DHCP=yes
```
至此, 翻墙完成. Happy Hacking!
