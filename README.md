# Linux 服务器透明翻墙

## 前言

Kubernetes 是来自 Google 云平台的开源容器集群管理系统, 我们在进行 Kubernetes 学习和实验的过程中, 总会因为 flannel、kubernetes/pause、hub.docker.io 等资源被墙或连接缓慢, 无法顺畅的进行学习实验. 

解决这个问题有`搭建本地资源仓库`和`服务器透明翻墙`两种方法, 为了避免以后有其他翻墙有关的问题出现, 我们选择服务器透明翻墙的方式.

## 目标和资源依赖

### 目标

使内网的服务器集群都能实现透明翻墙. 透明翻墙是指, 服务器无需配置翻墙客户端, 或者通过环境变量来设置代理服务器, 而是自动的翻墙访问国外 IP.

### 资源依赖

- 境外 VPS 一个 (能高速稳定的访问 Google, quay.io, AWS 等被墙网站)
- 可以刷集成 Shadowsocks 客户端的固件的路由器一台
- 一台服务器拥有有双网卡

## 章节

__本文接下来会详解整个构建透明翻墙环境方法, 限于篇幅分为三个章节:__

- __[透明翻墙路由器配置](./chapter-1/transparent-router-cross-over-the-wall.md)__
- __[内网集群网络构建详解](./chapter-2/construction-cluster-network.md)__
- __[配置服务器路由转发策略](./chapter-3/routing-policy-configuration-server.md)__


