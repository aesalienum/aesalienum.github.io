---
title: 仿真机器人实践 01：初识ROS2框架
author: aesalienum
date: 2023-11-06 21:00:00 +0800
categories: [ROS2, 仿真机器人实践]
tags: [ROS2, C++, 机器人]
pin: true
math: true
mermaid: true
image:
---

## 1. ROS（robot operating system）介绍

- 是一个适用于机器人的开源的元操作系统。

- 提供操作系统应有的服务，包括硬件抽象，底层设备控制，常用函数的实现，进程间消息传递，以及包管理。

- 也提供用于获取、编译、编写、和跨计算机运行代码所需的工具和库函数。

- 具备通讯机制，开发工具，应用功能，生态系统四大功能。

ROS首先是一个通信框架，在该框架内，机器人的不同组件作为独立的节点（Node）进程，通过ROS提供的通信方式进行通信。ROS支持以下几类通信过程：

- 话题`Topic`：订阅发布模型，通过消息`msg`定义数据结构，由发布者发布到框架，并转发给订阅者。

- 服务`Service`：客户端服务器模型，通过`srv`定义双向数据结构，由客户端发起服务请求，构建`srv`数据对象并由框架转交服务器，返回内容会填充至`srv`对象对应位置。

- 动作`Action`：持续性服务，在`Service`的基础上，支持长时间持续性动作。`action`定义了动作的通信数据，包括`goal`, `feedback`和`result`三个部分，客户端首先向服务发送`goal`数据，服务端收到后开始执行动作，并可通过`feedback`返回执行中间过程给客户端；当动作结束或者客户端取消动作时，服务端回复`result`，结束本次通信。

另外，ROS封装了很多开发过程中的实用工具，例如可视化、录包、命令行控制等功能，这些在后续开发中都会用到。

除此之外，ROS作为一个发展了很多年的开源项目，许多开源算法都能开箱即用，使机器人入门变得极为简单。

## 2. ROS环境搭建

### 安装

ROS主要分为ROS1和ROS2，并且有很多发行版，区别和安装方式都能在官网找到。

这里安装ROS2 iron。

```shell
# 首先确保启用了 Ubuntu Universe 存储库。
sudo apt install software-properties-common
sudo add-apt-repository universe

# 添加带有 apt 的 ROS 2 GPG 密钥
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
# 无法访问的话，连接可以改成https://ghproxy.com/https://raw.githubusercontent.com/ros/rosdistro/master/ros.key

# 添加apt仓库地址
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

# 更新并安装
sudo apt update
sudo apt upgrade
sudo apt install ros-iron-desktop # ros桌面版完整包
sudo apt install ros-dev-tools # 开发工具
```

### 运行demo

打开一个终端，运行

```shell
# 使能ROS环境
source /opt/ros/iron/setup.zsh

# 开启一个订阅demo节点
ros2 run demo_nodes_cpp listener

```

打开另一个终端，运行

```shell
# 使能ROS环境
source /opt/ros/iron/setup.zsh

# 开启一个发布demo节点
ros2 run demo_nodes_cpp talker

```

上面两个操作在两个不同的终端中运行了两个ROS节点，一个是topic发布者，一个是topic订阅者，两者通过ROS框架实现跨进程通信。

ROS提供的基础能力通过这个简单的例子就可见一斑，跨进程甚至跨设备通信功能完全由ROS封装，这使得开发者可以在尽可能不考虑任何操作系统、互联网层面的问题的情况下，专注于更抽象的机器人模块框架的搭建和构造。

ROS还提供一定的仿真和可视化能力，使得在没有实体机器人硬件的情况下，也能进行机器人相关开发验证工作，一方面降低了入门要求，另一方面细化了工程验证流程，能方便地做到先仿真再测试。这里用经典的小乌龟demo展示这些能力。

```shell
# 将环境使能命令写到zshrc文件里，这样就不用每次新开终端都要敲了
echo "source /opt/ros/iron/setup.zsh" >> ~/.zshrc
source ~/.zshrc

ros2 run turtlesim turtlesim_node
```

此时，出现了一个可视化页面，中间有一只小乌龟。新建一个终端：

```shell
ros2 run turtlesim turtle_teleop_key
```

在新终端中按键盘上的箭头，或者根据提示的信息键入，就可以控制第一个终端打开的界面中的小乌龟进行运动了。

除此之外，ROS还提供了实时对系统进行监控的各类工具，先不要关闭上两个终端，然后新建一个终端：

```shell
ros2 topic list
# /parameter_events
# /rosout
# /turtle1/cmd_vel
# /turtle1/color_sensor
# /turtle1/pose

ros2 topic echo /turtle1/cmd_vel
```

这两条指令首先列出了ROS系统中当前正在活跃的所有topic通信链路，然后监听并打印其中的`/turtle1/cmd_vel`话题，这条指令会占用终端输出，并且目前什么也不会打印，但是当你回到`turtle_teleop_key`的终端，并键入几次方向键后，就能在这里看到你所的键入对ROS系统的影响了——键入的内容转换成了一定时间的线速度和角速度，然后发送到了这个topic上。

## 3. ROS基本知识梳理

如果有需要，建议先过一遍官网上的[入门CLI工具教程](https://docs.ros.org/en/iron/Tutorials/Beginner-CLI-Tools.html)，留个印象，后续博客中会经常用到这些工具，这里简单做一个备忘索引。

### 命令行工具

- 节点

ROS运行的基本单位。节点是ROS中的抽象单位，一般由进程承载

```shell
ros2 run <package_name> <executable_name> #从包启动可执行文件，生成了一个节点

ros2 node list # 列出运行中的节点

ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle
# 重映射，允许修改默认的节点属性

ros2 node info <node_name> # 查看节点信息
```

- 话题

话题topic是一种异步数据通信方式，通信的数据（接口）由msg数据类型定义。

话题的通信过程由发布者发起，生成接口数据，并转发到所有订阅者缓冲中。订阅者被动等待接口数据，由注册的回调函数处理。

```shell
ros2 topic list -t # 列出运行中的所有话题，-t 同时列出话题数据类型
ros2 topic echo <topic_name> # 阻塞监听话题，打印监听数据
ros2 topic info <topic_name> # 显示话题信息（数据类型，订阅、发布数量）
ros2 interface show <msg_type> # 显示消息接口详情
ros2 topic pub [--rate num / --once]<topic_name> <msg_type> '<args>' # 模拟发布话题消息
ros2 topic hz <topic_name> # 统计话题发布频率
```

- 服务

服务service是一种同步数据通信方式，通信的数据（接口）由srv数据类型定义。

服务的通信过程由客户端发起，将接口数据交给服务端，服务端等待客户请求，然后处理并返回，将结果同步交还客户端。

```shell
ros2 service list -t # 列出运行中的所有服务，-t 同时列出服务数据类型
ros2 service type <service_name> # 获取服务数据类型
ros2 service find <type_name> # 根据数据类型查找服务
ros2 interface show <type_name> # 显示类型接口详情
ros2 service call <service_name> <service_type> <arguments> # 模拟请求服务
```

- 参数

参数param是节点上的配置值，可以视为节点的一部分。

节点可以将参数存储为整数、浮点数、布尔值、字符串和列表。

```shell
ros2 param list # 列出运行中的所有节点的所有参数
ros2 param get <node_name> <parameter_name> # 获取参数类型和值
ros2 param set <node_name> <parameter_name> <value> # 设置参数值
ros2 param dump <node_name> # 节点参数转储（默认打印到stdout）
ros2 param load <node_name> <parameter_file> # 节点参数加载（yaml格式）
ros2 run <package_name> <executable_name> --ros-args --params-file <file_name> # 使用指定的参数文件创建节点
```

- 动作

动作是一种持续型通信，适用于长时间运行的任务。 它们由三部分组成：目标、反馈和结果。

动作实际上是由服务和话题共同组成的一种通信机制，在动作中，客户端发起动作目标请求，并得到服务端的回应，随后，客户端发起动作结果请求，此时，在服务端执行的过程中，会不断向客户端发送反馈（alive、进度等），最后，服务端为动作结果请求返回结果。

```shell
ros2 action list -t # 列出运行中的所有动作，-t 同时列出动作数据类型
ros2 action info /turtle1/rotate_absolute # 查看动作详情
ros2 interface show turtlesim/action/RotateAbsolute # 显示动作类型详情
ros2 action send_goal <action_name> <action_type> <values> [--feedback] # 模拟发送动作目标请求，--feedback会同时打印所有反馈
```

- 批启动

launch

- 记录和回放

```shell
ros2 bag record -o <file_name> <topic_name1> <topic_name2> ... # 记录（打包）话题数据
ros2 bag info <file_name> # 查看打包数据文件信息
ros2 bag play <file_name> # 回放打包数据
```

