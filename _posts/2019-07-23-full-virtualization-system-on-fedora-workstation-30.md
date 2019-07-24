---

layout: post
title: 如何使用 virt-manager 来操控虚拟机
date: 2019-07-23 23:36:53 +0800
---

> 本文翻译自 Marco Sarti 的文章 [How to run virtual machines with virt-manager](https://fedoramagazine.org/full-virtualization-system-on-fedora-workstation-30/)。

早先时候，如果想要在笔记本上安装多个系统的话，大家可能会选择做成多系统模式。而其缺点就是没办法同时运行多个系统或者在多个系统之间进行互通。多年之后，这些都不再是问题了，即使在普通的个人电脑上，也可以通过虚拟化的方式在一个系统之中运行另外其他的系统。

近年来的个人电脑或笔记本，包括一些中端的产品，其硬件特性已经足以使虚拟机的性能接近物理主机。

虚拟化便随之「飞入寻常百姓家」，我们可以用来测试操作系统，学习新技术，创建自己的家庭云存储，创建自己的测试环境等等。本文就为各位演示一下如何在 Fedora 上用 Virt Manager 创建虚拟机。

## QEMU/KVM 与 Libvirt 简介

和其他 Linux 系统一样，对虚拟化扩展的支持是 Fedora 与生俱来的能力。因为提供这一能力的 KVM（基于内核的虚拟机）模块已经是内核的一部分了。

而 QEMU 是一个完整的系统模拟器，它可与 KVM 协同工作，允许我们使用硬件和外设来创建虚拟机。

最后，通过 [libvirt](https://libvirt.org/) 这一层 API，我们就可以管理基础架构了，例如创建和运行虚拟机等。

以上这三种技术全部都是开源的，我们接下来就在 Fedora Workstation 上安装它们。

## 安装

### 第一步：安装软件包

整个安装过程相当简单。Fedora 软件包仓库里提供了「虚拟化」软件包组，里面就包含了我们所需的全部软件包。

```
sudo dnf install @virtualization
```

### 第二步：编辑 libvirtd 配置

默认情况下，只有 root 用户才能进行系统管理，如果要为普通用户授权的话，则需要按以下步骤操作。

编辑 /etc/libvirt/libvirtd.conf 这个文件。

```
sudo vi /etc/libvirt/libvirtd.conf
```

将域 socket 的所有组设置为 libvirt：

```
unix_sock_group = "libvirt"
```

修改 R/W socket 的 UNIX socket 权限：

```
unix_sock_rw_perms = "0770"
```

### 第三步：运行 libvirtd 服务并设置为开机自启

```
sudo systemctl start libvirtd

sudo systemctl enable libvirtd
```

### 第四步：将用户添加到组

如果想使用普通用户身份来管理 libvirt 的话，需要将该用户添加到 libvirt 组，不然的话每次启动虚拟管理器时，都得输入 sudo 密码。

```
sudo usermod -a -G libvirt $(whoami)
```

这一行命令就可以将当前用户添加到组。注意，这里需要注销后再登录才能生效。

## 开始上手 virt-manager

libvirt 系统既可以通过命令行工具（virsh）也可以用图形界面工具 virt-manager 来管理。如果想要自动化地配置虚拟机，命令行就非常实用，例如使用 [Ansible](https://fedoramagazine.org/get-the-latest-ansible-2-8-in-fedora/) 的情况，不过在本文中，我们就主要用对于用户更友好的图形界面吧。

virt-manager 的界面非常简洁。主界面会显示连接列表，其中包括本地系统连接。

连接设置中包括了虚拟网络和存储定义。我们可以定义多个虚拟网络，这些网络可用于在虚拟机系统之间以及虚拟机和宿主机之间进行通信。

## 开始创建第一台虚拟机

创建一台新虚拟机，点击主界面左上角的按钮：

![](/assets/2019/07/23/Screenshot-from-2019-07-14-09-41-45.png)

向导的第一步需要我们选择安装模式。可以选择通过本地安装介质，通过网络引导或安装，以及通过现有虚拟磁盘导入：

![](/assets/2019/07/23/Screenshot-from-2019-07-14-09-30-53.png)

选择通过本地安装介质，下一步选择本地 ISO 镜像的路径：

![](/assets/2019/07/23/Screenshot-from-2019-07-14-10-42-39.png)

之后的两个步骤是调整新虚拟机的 CPU，内存和磁盘的大小。最后一步会询问我们想要选择哪种网络配置：如果想让虚拟机通过 NAT（网络地址转换）的方式与主机网络隔离开，那就选择默认；如果想让虚拟机可以从外部访问得到，那就选择桥接模式。需要注意的是，如果选择了桥接模式，虚拟机便无法直接与宿主机进行通讯了。

如果你想最后再检查一遍或者再做调整的话，可以勾选上「在安装前自定义配置」这个复选框：

![](/assets/2019/07/23/Screenshot-from-2019-07-14-10-43-21.png)

我们便可以通过这个虚拟机配置表来检查或修复其硬件配置。如添加硬盘、网卡，修改启动顺序等等。调整好了以后，点击「开始安装」：

![](/assets/2019/07/23/Screenshot-from-2019-07-14-10-44-58.png)

之后，软件就会为我们打开控制台，显示系统的安装过程。安装完成后，我们就可以从控制台访问这台虚拟机了：

![](/assets/2019/07/23/Screenshot-from-2019-07-14-10-55-35.png)

刚刚创建的虚拟机会出现在主界面的列表中，并且还会以图表的形式显示 CPU 和内存的占用情况：

![](/assets/2019/07/23/Screenshot-from-2019-07-14-11-09-22.png)

libvirt 和 virt-manager 是一套功能非常强大的工具，可以为我们提供企业级的管理功能，满足我们自定义虚拟机的需求。当然了，要是你想要一个更简单的工具的话，可以了解一下 Fedora Workstation 上自带的 [GNOME Boxes，它也可以满足一些基本的需求](https://fedoramagazine.org/getting-started-with-virtualization-in-gnome-boxes/)。

