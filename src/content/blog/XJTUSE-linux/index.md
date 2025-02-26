---
title: XJTUSE-Linux实验笔记
description: 西安交大软件专业Linux实验笔记
publishDate: 2025-02-26
#updatedDate: 2025-02-
tags:
  - XJTUSE
  - Linux
---

## 实验与笔记介绍

这是我在上Linux实验时所记录下来的一些有意思的东西，仅供参考。

## Linux简单介绍

Linux是符合**POSIX**(Por)标准的类Unix的操作系统，其诞生发展依赖于：UNIX、MINIX(Mini UNIX)、GNU、POSIX、Internet。

MINIX在各种介绍Linux历史的课程书籍上几乎是必然出现的，它是荷兰阿姆斯特丹大学著名的计算机教授Andrew S. Tanenbaum于1987 年开发完成的，大部分由C语言编写：分为内核、内存管理和文件管理。

Linux Torvalds使Linux内核源码可以在GPL（GNU General Public License）上使用，这个许可证我记得是为了对抗对Unix衍生的一些产品的闭源行为而产生的。他允许所有人读取和编辑源码且要提供所有编辑过的源码。

Linux这个名称也很有意思，实际就是Linus's Minix。

Linux系统内核的主要模块（或组件）分以下几个部分：存储管理、CPU和进程管理、文件系统、设备管理和驱动、网络通信，以及系统的初始化（引导）、系统调用等。

### Linux版本号

这个小角落的知识以前我从来都没了解过（）

* **内核版本号**
  * Linux内核开发小组决定
  * 主版本号、次版本号、次次版本号，如2.6.5
  * n次版本号为奇数时为测试版本
* **发行版本号**：由发行Linux套件的公司/组织决定

### 虚拟机

一般使用的虚拟机软件有VMware Workstation和另一个（忘记叫什么了），不过我不是很喜欢使用虚拟机，有点卡（）。我选择使用WSL2+二手的Thinkpad上装的Linux真机。

VMWare模拟出来的硬件有：主板、内存、硬盘(IDE和SCSI)、DVD/CD-ROM、软驱、网卡、声卡、串口、并口和USB口。VMWare没有模拟出显卡。但VMWare为每一种Guest OS提供一个叫做vmware-tools的软件包，来增强Guest OS的显示和鼠标功能。

VMWare模拟出来的硬件是固定型号的，与Host OS的实际硬件无关。

## Linux的使用

Unix系列的OS中，shell程序在用户和操作系统内核之间充当桥，将用户的要求转换为系统调用，并传递回系统消息。

在Ubuntu中，按下`Ctrl + Alt + T`可以快速打开一个新终端。

**创建和查看文件**

* touch cat就不说了，基本的命令
* more和less：用于分页查看文本文件内容；more只能向前滚动浏览文件内容，无法直接向后滚动查看已经浏览过的内容。不仅可以向前，还能向后滚动浏览文件。more**一次性**将整个文件加载到内存，而less采用**按需加载**机制，仅加载当前显示的部分内容。
* head和tail默认显示文件开头或结尾的10行，加`-n`来指定行数



`history`命令显示使用过的命令，其显示的内容实际上是写在用户主目录中的文件`.bash_history`中。



















