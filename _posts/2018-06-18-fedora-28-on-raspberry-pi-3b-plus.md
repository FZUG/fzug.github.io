---
layout: post
title: 在树莓派 3B+ 上使用 Fedora 28
date: 2018-06-18 17:40:11 +0800
---

> 本文翻译自 Sinny Kumari 的文章 [Fedora 28 on Raspberry Pi 3 B+](https://fedoramagazine.org/fedora-28-on-raspberry-pi-3b-plus/)。

2018 年 3 月中旬，树莓派大家庭增加了一名新的成员——[树莓派 3 代 B+ 型](https://www.raspberrypi.org/magpi/raspberry-pi-specs-benchmarks/)（RPi 3 B+）。RPi 3 B+ 相较于之前的树莓派 3 代 B 型有很多新的特性和改进。包括处理器主频提高到了 1.4GHz，搭载了千兆以太网卡，提供双频 2.4GHz 和 5GHz Wi-Fi，以及蓝牙 4.2 的支持。 RPi 3 B+ 推出后不久 [Fedora 28](https://fedoramagazine.org/announcing-fedora-28/) 正式发布。值得兴奋的是它可以支持 RPi 3 B+，同时还包括树莓派 2 代和 3 代 B 型。ARMv7（32 位）和 aarch64（64位）的镜像[都可以下载](https://fedoraproject.org/wiki/Architectures/ARM/Raspberry_Pi#Downloading_the_Fedora_ARM_image)。本文会教你如何在 RPi 3 B+ 上启用 Wi-Fi。

## 将镜像写入 SD 卡

首先，从[这里](https://fedoraproject.org/wiki/Architectures/ARM/Raspberry_Pi#Downloading_the_Fedora_ARM_image)下载 Fedora 28 的系统镜像。在本例中，需要下载 [Fedora 28 aarch64 Workstation 镜像](https://dl.fedoraproject.org/pub/fedora-secondary/releases/28/Workstation/aarch64/images/Fedora-Workstation-28-1.1.aarch64.raw.xz)并将其写入 SD 卡：

```
$ wget https://dl.fedoraproject.org/pub/fedora-secondary/releases/28/Workstation/aarch64/images/Fedora-Workstation-28-1.1.aarch64.raw.xz
```

现在运行 `dmesg` 来看一下 SD 卡被挂载到了系统的哪个位置：

```
$ dmesg
...
30046.093242] sd 4:0:0:0: [sdb] 62333952 512-byte logical blocks: (31.9 GB/29.7 GiB)
...
```

本例中 SD 卡被挂载到了 `/dev/sdb`。这个位置可能会因具体系统不同而有所区别。现在将镜像写入 SD 卡，执行 `dd` 命令：

```
$ xzcat Fedora-Workstation-28-1.1.aarch64.raw.xz | sudo dd status=progress bs=4M of=/dev/sdb
10737418240 bytes (11 GB, 10 GiB) copied, 1200.19 s, 8.9 MB/
```

这时候我们已经成功将镜像写入 SD 卡了。

接下来，为了能让内建 Wi-Fi 可用，我们必须下载某些缺失的固件文件并将其放到 `/usr/lib/firmware/brcm/` 目录中。

首先，挂载 root 文件系统并添加这些文件，以便使它们可以在引导过程中发挥作用。使用 `lsblk` 命令找出 root 文件系统在哪里：

```
$ lsblk
...
sdb    8:16 1 29.7G 0 disk 
├─sdb1 8:17 1 200M  0 part 
├─sdb2 8:18 1 1G    0 part 
├─sdb3 8:19 1 1G    0 part 
├─sdb4 8:20 1 1K    0 part 
└─sdb5 8:21 1 7.8G  0 part
```

在本例中，root 文件系统在 `sdb5`，因此挂载它，然后添加缺失的固件文件：

```
$ sudo mkdir /mnt/foo &amp;&amp; sudo mount /dev/sdb5 /mnt/foo/
$ sudo curl https://fedora.roving-it.com/brcmfmac43455-sdio.txt -o /mnt/foo/usr/lib/firmware/brcm/brcmfmac43455-sdio.txt
$ sudo curl https://fedora.roving-it.com/brcmfmac43455-sdio.clm_blob -o /mnt/foo/usr/lib/firmware/brcm/brcmfmac43455-sdio.clm_blob
$ sudo umount /mnt/foo
```

其实也可以在首次启动之后再添加这些固件文件。不过那种情况就需要树莓派 3 代 B+ 型能通过有线网络连接了。然后可以下载这些固件文件，并且重启树莓派来加载这些固件。

## 启动并初始化配置

将准备好的 SD 卡插入到 树莓派 3 代 B+ 型中，接好显示器。接上 5 伏 2.5 安电源，RPi 3 B+ 会立即启动。显示器上会显示启动过程的输出信息。

在首次启动过程中，系统会进入初始化界面设定页面，我们可以在此配置用户详细信息。同时它还会列出可用网络并连接上 Wi-Fi。在这些步骤之后，我们就能看到常见的 GNOME 桌面了。

## 使用命令行连接 Wi-Fi

如果你启动的是 Server 镜像的话，就没有图形界面了，那这一部分就需要注意一下了。

```
$ nmcli device wifi list
IN-USE SSID         MODE   CHAN  RATE SIGNAL BARS SECURITY
       SSID_2.5_GHz  Infra  10   195   Mbit/s 100 ▂▄▆█ WPA2
       SSID_5_GHz    Infra  149  405   Mbit/s 72  ▂▄▆_ WPA2
$ nmcli device wifi connect SSID_5_GHz password $PASSWORD
$ nmcli device wifi list
IN-USE SSID MODE CHAN RATE SIGNAL BARS SECURITY
       SSID_2.5_GHz Infra 10  195 Mbit/s 100 ▂▄▆█ WPA2
 *     SSID_5_GHz   Infra 149 405 Mbit/s 72  ▂▄▆_ WPA2
```

注意这里树莓派连接的是 5GHz Wi-Fi。

## 已知问题

树莓派 3 代 B+ 型上 Fedora 28 可以良好运行，但是有些问题还是需要留意一下。

- aarch64 Server 镜像中并没有预装 `NetworkManager-wifi` 软件包。所以在尝试连接 Wi-Fi 前你一定要提前装好这个包。其他镜像都已经有了这个包了。
- 某些情况下，树莓派 3 代 B+ 型上连接了键盘和鼠标会导致启动过程卡住。如果遇到这种情况，拔掉键盘鼠标，等启动之后再接上即可。

## 获取帮助

树莓派 3 代 B+ 型是 Fedora 28 所支持的最酷的也是最流行的单板电脑设备之一。你可以尽情地用它来尝试你所喜欢的物联网应用！

在 [Fedora ARM wiki](https://fedoraproject.org/wiki/Architectures/ARM/Raspberry_Pi/zh-cn#.E5.B8.B8.E8.A7.81.E9.97.AE.E9.A2.98.EF.BC.88_FAQ_.EF.BC.89) 页面上有一份常见问题列表，遇到问题时不妨先来这里找找答案。如果问题还是无法解决，你还可以通过以下两个方式来寻求帮忙或建议：

- [Fedora ARM 邮件列表](https://lists.fedoraproject.org/admin/lists/arm%40lists.fedoraproject.org/)
- [Freenode](https://fedoraproject.org/wiki/IRC) 上的 #fedora-arm IRC 频道
