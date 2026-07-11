---
title: Linux分区移动记录
description: Windows-Linux双系统中将Windows的空闲空间给Linux
slug: procap
date: 2026-07-11 9:00:00+0012
categories:
    - daily
tags:
    - 杂记
    - 操作系统
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## 引言

![磁盘初始状态](./partition-0.png "磁盘初始状态")

之前在Windows笔记本上装了一个Linux系统，原本想的是这个Linux系统用于玩玩agent，因此没有给Linux留很多的空间。但后来发现Linux系统用起来也挺顺手，而且基本可以把系统管理的事情交给agent来做，因此打算长期使用Linux系统。此时发现Linux中的空间不太够用，于是打算把Windows系统盘里面更多的空闲空间分配给Linux用。但有一个问题，整体磁盘的分区状态如上图所示，我想要把Windows分区中间的这一块分出一部分空闲空间给右边的Linux分区使用，但这样会需要进行如下图所示的分区移动：

![移动问题](./partition-0-1.png "移动问题")

在已有的Linux系统中，利用GParted进行Windows分区的移动是很容易的，但无法直接进行Linux分区的移动，尤其是这里需要移动`/boot`分区。网上查了一圈，好像没有人有写过类似的记录，询问了AI之后说这种情况需要借助LinuxLive USB来做。这篇博客就记录了我利用启动盘对磁盘分区进行移动的过程。

## 前置工具准备

- 一块LinuxLive USB

实际上这个只需要用最开始安装Linux用的启动盘来做就可以了，里面一般都会装有这样的环境。我们主要是要利用这个外部的临时系统来对电脑磁盘进行操作。

## 进入启动盘

和安装系统时一样，插入启动盘，然后在开机的时候进入启动引导界面（我这里的华为笔记本是按F12），然后选择用USB启动。