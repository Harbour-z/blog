---
title: Java网络编程——BIO、NIO与Netty
author: Zhidong Zhang
description: 记录最近在计网的课设和计网实验中所学到的Java网络编程相关知识
publishDate: 2025-06-26
# updatedDate: 
draft: False
language: 中文
tags:
  - Java Network
  - BIO
  - NIO
  - Netty
---

## BIO、NIO与Netty简介

BIO（Blocking IO，同步阻塞IO）和NIO（Non-blocking IO，同步非阻塞IO）是OS层面的IO模型，描述数据在计算机底层（内核态和用户态）之间传输的方式。它们即是抽象的思想模型，也有具体的实现（Java通过JDK提供的API实现了两个模型）。

> Java中的 `java.net`包下的`ServerSocket`与`Socket`就是BIO模型的实现

**同步和异步** 是对两个线程之间的关系进行描述的，**阻塞与非阻塞** 是描述单个线程的状态的。





## Java NIO

**核心思想**：通过IO多路复用实现单线程管理多个连接，避免线程阻塞在IO操作上。

**核心技术**：依赖操作系统的**IO多路复用器**（如Linux的`epoll`、Windows的`IOCP`），通过一个线程监听多个IO事件（连接、可读、可写等），当事件就绪时再触发处理（如NIO的`Selector`选择器）。

NIO三大组件：Channel 通道，Buffer 缓冲区，Selector 选择器；

> Java 原生的NIO实现：`java.nio`提供支持，编程复杂度高，需手动管理事件注册、缓冲区边界、编解码等。

Channel 是双向数据通道，传统Java IO中，数据基于Stream流传输，NIO中 Channel 取代了Stream，成为进行IO的主要接口。

Selector通过监听多个Channel通道，当某个通道就绪后就读写哪个通道上的缓冲数据。

**原生NIO的缺陷**

* 编程复杂：手动管理Selector事件注册、Buffer缓冲区、编解码
* 缺乏高级特性：断线重连、心跳检测、SSL/TLS需手动实现
* 稳定性：存在空轮询、Selector唤醒问题等底层缺陷，需额外处理

## Netty

基于Java NIO 的高性能、异步事件驱动的网络应用程序框架。这是基于 NIO模型(结合Reactor模式)的框架实现。

Netty底层使用了 Java NIO并进行了性能优化，Java NIO 基于非阻塞IO和IO多路复用。

Java NIO 提供的所有特性，Netty都以更高级、更方便、更可靠的方式提供。

| 组件   |  Java NIO  | Netty                  | Netty 特点                                                   |
| :----- | :--------: | ---------------------- | ------------------------------------------------------------ |
| 通道   |  Channel   | NioServerSocketChannel | 将接收到的I/O事件发送给ChannelPipeline和ChannelHandler来进行自定义处理 |
| 缓冲区 | ByteBuffer | ByteBuf                | 池化、零拷贝、自动扩容、链式调用                             |
| 选择器 |  Selector  | EventLoop              | 有两组管理EventLoop的EventLoopGroup：<br />一组用于监听通道是否连接就绪，即请求组(单线程)<br />一组处理IO事件，即工作组（多线程） |

### EventLoop

EventLoopGroup 本质是Netty的“IO线程池”，负责管理一组EventLoop（IO线程）。每个EventLoop是一个单线程，绑定一个Selector，负责监听和处理IO事件。

每个EventLoop可以对应多个客户端连接，本质通过IO Multiplexing机制

`bossGroup`和`workerGroup`本质上是两个不同职责的线程池，分别处理网络通信中不同阶段的事件，共同构成Netty的主从Reactor线程模型。

前者唯一职责是处理客户端的连接请求，通常配置1个线程，因为监听连接事件的频率远低于IO事件，一个线程足够处理所有连接请求。仅关注“连接是否就绪”，不处理具体的业务数据读写，避免阻塞在耗时的业务逻辑上（核心目标是“高效接收连接”）。

workerGroup（通常称为“从Reactor”或“IO线程组”）的核心职责是**管理已建立的客户端连接**，处理**IO事件**（数据可读、可写等），具体流程：

- **注册通道**：客户端连接被bossGroup接收后，会被注册到workerGroup的某个EventLoop（通过`Channel.register(workerGroup.next())`），由workerGroup的EventLoop监听该连接的IO事件；
- **监听IO事件**：当客户端发送数据（数据就绪）或可写入数据时，workerGroup的EventLoop通过Selector监听到`OP_READ`或`OP_WRITE`事件，触发数据读写；
- **处理业务逻辑**：通过`ChannelPipeline`中的Handler链（如编解码器、业务Handler）处理数据（如解析协议、执行业务逻辑、响应客户端）。



**关键特性**

- **线程数量多**：通常配置为**CPU核心数 \* 2**（Netty默认值），充分利用多核CPU资源（如8核CPU配置16个线程），避免单个线程处理过多连接导致IO阻塞；
- **IO事件驱动**：通过Selector监听多个客户端连接的IO事件，当数据就绪时才触发处理（非阻塞），避免线程阻塞在无效等待上；
- **线程隔离**：每个`SocketChannel`会绑定到一个固定的EventLoop（通过`register(EventLoop)`），确保连接的所有IO操作都在同一个线程中处理，避免线程安全问题（无需加锁，Netty的线程安全保障核心）。

## IO 多路复用

IO Multiplexing是一种同步非阻塞的 IO 模型，核心是通过一个或少量线程管理多个IO连接，避免BIO中的资源浪费问题。本质是通过一个**管理者（选择器）**监听多个IO事件，某个事件就绪时再通知应用处理。





