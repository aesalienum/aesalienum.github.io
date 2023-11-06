---
title: 基于 zerotier 的IPV4虚拟局域网搭建
author: aesalienum
date: 2023-10-05 21:00:00 +0800
categories: [学习环境, 网络]
tags: [zerotier, 虚拟局域网]
pin: true
math: true
mermaid: true
image:
---

## 虚拟局域网

目前，整个互联网的格局被限制在了IPv4框架中，每台接入互联网的设备都有自己的IP地址。

其中，公网IP是一等公民，具备公网IP的设备可以其他具有公网IP的设备进行通信。公网IP的数量有限，因此大部分设备都通过NAT协议，处于一个个独立的局域网中，只具备一个局域网IP，并通过少有的几个公网IP设备，和互联网通信。

局域网IP是二等公民，他们的通信有两种：一是局域网内的通信，这个过程不需要互联网参与，在局域网中可以自由的进行；二是和互联网进行通信，这个过程需要由具备公网IP的路由设备代为进行。路由设备可以“好心地”帮你传达你的意愿，并将回信分发给你，但是当外界想要主动找到你，就是一件不太可能的事情了。

有时候，我们有很多台设备，想要相互通信。最简单的方法是建立一个局域网，用同一根网线（或者wifi）将所有设备连接在一起。但是当这些设备包含移动设备时，物理上的连接很难实现，除非你能把运营商的活干了，自己搭建一条覆盖极广的链路。

有很多思路可以解决这个问题。

### FRP

如果只是想暴露一个服务，那么可以使用frp，将内网端口映射到公网上。这要求你拥有一个公网IP设备，购买云服务器通常是一个方便、经济的选择。

frp的原理很简单，内网处部署客户端，公网处部署服务端，客户端首先发起一个到服务端的请求，建立一个网络连接，这个连接允许两者互相传递信息。因此，对于互联网中的其他设备，只需要请求这个公网上的某个端口，frp就会捕获这些信息，然后转发到隐藏在frp背后的设备和端口上。同样的，如果内网设备需要再向外回复些什么，也通过上述连接，转发给公网设备，并由其代发给外部。

如果你对这个过程感到疑惑，不明白为什么要复杂的转来转去，那可能是因为你忽略了公网设备和内网设备的区别。简单来说，只有公网设备才能提供服务。客户端要求别人给自己提供服务，它需要明确知道自己要向谁请求，然后就会向对应的IP发送请求数据，然后等待其返回服务结果；服务端则比较被动，它需要一直运行，然后处理随时到来的、要求自己提供服务的连接，并给与回复。在这个过程中，从客户端到服务端的主动连接过程非常重要，服务端的可达与否决定了这个过程能否顺利完成。

### VPN

FRP暴露了一个服务给外界，如果没有设置密码或者密钥，那么所有人都能访问到，所以虽然它能实现随时随地对一台设备的访问（当你暴露的服务是ssh服务时），但是并不安全，和虚拟局域网的初衷也相差甚远。与之相比，VPN（Virtual Private Network）选择了相反的方法————它可以将一个远程主机添加进一个局域网中。

简单讲，VPN客户端运行在你的移动主机上，它将你的所有网络连接数据转发到VPN服务端上，这个服务端通常需要和你的个人局域网相连，并且具备一个公网IP地址。然后，VPN服务端用它的局域网IP，或者模拟一个局域网IP和你的个人局域网中的其他设备通信，最后将回复数据返回给你的移动主机。

在这种结构中，你的个人局域网的所有内部主机都未在公网暴露，连接你的个人局域网需要安装VPN客户端，客户端和服务端都可以附带一些验证条件，安全性能要高很多。但是需要一台本地的公网IP设备，这无法直接用VPS实现。

### ZeroTier

ZeroTier是一种SD-WAN（Software-Defined Wide Area Network，软件定义广域网）技术。这类技术可能还有很多，但是ZeroTier是其中使用方便、部署简单、并且个人可以免费实现的一种方案。

大致来说，ZeroTier官方提供了一套完整的个人解决方案，包括跨平台的客户端，和一套公开的虚拟局域网注册系统。作为普通用户，我们只需要在官网上注册账号，生成自己的局域网ID，然后在个人的设备上安装客户端，加入自己的局域网即可。

## 搭建过程

### 1. 购买一个服务器，并作简单配置

服务器的作用是自建moon，因为官方提供的路由路径性能较差，所以可以通过搭建moon节点自己缩短路由路径，不是必选项。

在本地为服务器设置一个别名
```
Host 自定义名称
    HostName IP
    User 用户名
    Port 22
```
{: file='~/.ssh/config'}


```bash
ssh-copy-id -i .ssh/id_rsa.pub ${host_name}

或者手动把公钥复制到服务器的~/.ssh/authorized_keys下，每个公钥占一行
```

### 2. 官网注册一个虚拟局域网


### 3. 本地和服务器都安装zerotier客户端

windows直接在官网下载安装即可。

以linux为例：

```shell
curl https://install.zerotier.com/ | sed 's,download.zerotier.com/,mirrors.sustech.edu.cn/zerotier/,g' | sudo bash

sudo systemctl start zerotier-one.service
sudo systemctl enable zerotier-one.service

sudo zerotier-one cli join 你的网络ID

systemctl restart zerotier-one.service
```

如果出现权限问题，将自己用户加入进zerotier-one组即可
```shell
sudo usermod -a -G zerotier-one $USER
```

配置加入局域网后，还需要在官网的管理界面勾选允许，才算正真加入了虚拟局域网。

现在就可以尝试使用虚拟局域网的IP互相ping了，但是一般可能都有500ms以上的延迟。
原因在于默认的路由服务器比较远，两台设备实际上不在同一局域网的话，每次的路由链路非常长。可通过配置一个moon节点，短路官方的路由路径，提升通信质量。

### 4. 在具有公网IP的服务器上搭建moon节点

moon节点作为路由的中间节点，需要保证其他机器可达，因此必须具备公网IP。

```shell
cd /var/lib/zerotier-one

zerotier-idtool initmoon identity.public > moon.json

vim moon.json
# "stableEndpouints": ["ip/9993"]

zerotier-idtool genmoon moon.json

sudo mkdir moons.d
sudo mv ${machine-id}.moon moons.d/

systemctl restart zerotier-one.service
```

### 5. 在其他机器上启用moon

```shell
zerotier-cli listpeers # 检查节点，是否包括上述配置过的节点，通过机器id来识别

zerotier-cli orbit machine-id machine-id # 启用

# zerotier-cli deorbit machine-id machine-id # 禁用

zerotier-cli listmoons # 检查上述操作是否有效
```


## 其他

公网IP是IPv4阶段妥协的产物，目前IPv6的支持都比较好，但是为了安全考虑，大部分设备都是使用的动态IPv6地址，另外，入户路由等设备也需要更新，保证全链路支持IPv6。如果对这两个问题能够妥善解决，也可以通过IPv6搭建ZeroTier虚拟局域网，那就不需要额外购买服务器了。

笔者目前无法解决入户路由的问题，后续有机会再尝试。