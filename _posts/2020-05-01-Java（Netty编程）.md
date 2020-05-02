---
layout:     post
title:      Java（Netty编程）
subtitle:   李嘉图的博客
date:       2020-05-02
author:     李嘉图
header-img: img/java2.jpg
catalog: false
tags:
- Java
---
-   Netty, http://netty.io
-   最早由韩国Trustin Lee 设计开发的
-   后来由JBoss接手开发，现在是独立的Netty Project
-   一个非阻塞的客户端-服务端网络通讯框架
-   基于异步事件驱动模型
-   简化Java的TCP和UDP编程
-   支持HTTP/2， SSL等多种协议
-   支持多种数据格式，如JSON等

---
**关键技术**
- 通道 Channel
- ServerSocketChannel/NioServerSocketChannel/…
- SocketChannel/NioSocketChannel
- 事件 EventLoop
- 为每个通道定义一个EventLoop，处理所有的I/O事件
- EventLoop注册事件
- EventLoop将事件派发给ChannelHandler
- EventLoop安排进一步操作
- **事件**
- 事件按照数据流向进行分类
- 入站事件：连接激活/数据读取/……
- 出站事件：打开到远程连接/写数据/……
- 事件处理 ChannelHandler
- Channel通道发生数据或状态改变
- EventLoop会将事件分类，并调用ChannelHandler的回调函数
- 程序员需要实现ChannelHandler内的回调函数
- ChannelInboundHandler/ChannelOutboundHandler
- ChannelHandler工作模式：**责任链**
- **责任链模式**
- 将请求的接收者连成一条链
- 在链上传递请求，直到有一个接收者处理该请求
- 避免请求者和接收者的耦合
- ChannelHandler可以有多个，依次进行调用
- ChannelPipeline作为容器，承载多个ChannelHandler
- **ByteBuf** 
- 强大的字节容器，提供丰富API进行操作
