---
title: '计网'
draft: false
categories: ["专业课"]
cover: "images/covers/8.jpg"
date: 2026-05-03T11:00:00+08:00
lastmod: 2026-05-03T11:00:00+08:00
---



# 计网

![image-20260201165102084](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260201165102084.png)

Socket其实就是一个门面模式，它把复杂的TCP/IP协议族隐藏在Socket后面，对用户来说只需要调用Socket规定的相关函数，让Socket去组织符合指定的协议数据然后进行通信。

Socket可以理解为TCP/IP网络的API，它定义了许多函数或例程，程序员可以用它们来开发TCP/IP网络上的应用程序。

![image-20260201181725112](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260201181725112.png)

WebSocket是一种在单个TCP连接上进行全双工通信的协议
WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据
在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输
需要安装第三方包：
cmd中：go get -u -v github.com/gorilla/websocket



![image-20260203171023483](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260203171023483.png)

URL:统一资源定位符
http://   -> 是个协议
可以多个域名对应一个IP地址

![image-20260203171518429](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260203171518429.png)

服务：指一层向它的上一层提供的一种操作

![image-20260211121353801](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260211121353801.png)



![image-20260212120032880](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260212120032880.png)

# 网络层部分

#### ICMP协议：

当路由器处理数据包有意外发生时，通过ICMP向发送方报告
例如：
目标主机不可达
TTL（time to time live）(生存周期)超时:在网络环路当中陷入无限转发
路径探测

ping:发送echo(回显)到目的地址，判断目的设备是否活着，或者判断自身是否连接
traceroute:给目标发送一系列数据包，分别将TTTL = 1、2、3、……TTL归零时返回超时信息，由此可以确定沿途路由器的地址

![image-20260212115930787](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260212115930787.png)

#### DHCP协议:动态主机配置协议(如何给新设备分配地址)

新设备 ->进行广播，先到交换机 -> 广播 找到局域网DHCP服务器(如果没有局域网服务器，则通过路由广播到外网的DHCP服务器) -> 返回一个DHCP offer -> 拿到新地址 

#### ARP协议:地址解析协议

背景:链路层需要MAC地址才能发送成帧
作用:通过ARP将基于目的IP地址找到对应的MAC地址

在同一局域网内，通过ARP将基于目的IP地址找到对应的MAC地址
广播寻找IP -> 目标设备发送MAC地址返回

##### 默认网关:

网关:连接不同网络、负责转发数据包的网络设备或者**路由器接口**，他是网络边境的出入口
访问的地址在不同局域网时，通过IP查询到网关，而路由（默认网关）将他的MAC地址返回， 
然后发送到路由器时，路由器对比IP和MAC地址，发现IP不是它的而MAC是它的时，路由器会再次使用ARP协议，基于C6的IP获取MAC

#### NAT:网络地址转换

工位IPV4地址有限，而内部网络设备却日益增多，导致IPV4地址枯竭
NAT在数据包经过路由器时，修改其源（或目的）的IP地址/端口号，并维护一个映射表

#### 端口号:

端口号：唯一标识一个正在运行进程的端口号
进程：一个正在运行的运用程序
多线程：一个进程当中不同的执行路径

源端口号：发送方进程使用的端口号  目的端口号:接收方使用的端口号

#### IPV6:

IPV6地址长度为16个字节，即128位bit，通常以八段十六进制表示 ，每段16个bit

IPV6首部长度固定为20个字节
其不允许中间路由器进行分片组装，只允许在源和墓地上执行
去掉了校检和字段，通过数据链路层和传输层进行校检

#### IPV4的片偏移量：前面的数据量/8

<img src="C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260212190932097.png" alt="image-20260212190932097" style="zoom: 33%;" />

![image-20260219232639329](C:\Users\56955\AppData\Roaming\Typora\typora-user-images\image-20260219232639329.png)
对于网络层 只关注路径选择
对于传输层 只负责端口到端口的数据传输