---
layout: post
title:  "Fedora 和 Debian 开发人员着眼于封装 ROCm 以获得更轻松的 Radeon GPU 计算体验"
date:   2021-12-17 15:10:01 +0800
categories: tutorial
---

>原文：[Fedora & Debian Developers Look At Packaging ROCm For Easier Radeon GPU Computing Experience](https://www.phoronix.com/scan.php?page=news_item&px=Fedora-Debian-ROCm-Work)

当谈到 Radeon ROCm GPU 软件支持时，AMD 仅在 SUSE Linux Enterprise Server、RHEL/CentOS 和 Ubuntu LTS 版本上正式支持它。但是 Arch Linux 已经让他们的第三方软件包的部署变得相当容易，现在 Fedora 和 Debian 的开发人员也在关注 Radeon Open eCosystem 软件的可能的打包方案，以便更轻松地在这些发行版上部署 ROCm。

如果你正在使用 Ubuntu、RHEL/CentOS 或 SUSE 作为受支持的企业 Linux 发行版，AMD 有[安装指南](https://rocmdocs.amd.com/en/latest/Installation_Guide/Installation_new.html#system-requirements)和可用于设置其开源 GPU 计算软件的软件包。但是对于那些使用其他发行版的人，你的部署方法可能会有所不同，因为没有通用安装程序，并且在滚动更新你自己的 ROCm 二进制文件时构建步骤相当复杂。

最近几周的好消息是 Debian 在将 ROCm 打包作为其发行版的一部分方面[取得了进展](https://lists.debian.org/debian-ai/2021/11/msg00048.html)。Debian 软件包反过来可以流向许多基于 Debian 的发行版，如 Ubuntu、Linux Mint、Pop!_OS 等。Debian 维护者一直在与一些 AMD/ROCm 人员合作开发这个 ROCm 封装。有关 Debian + ROCm 工作的更多详细信息，请访问此[论坛主题贴](https://www.phoronix.com/forums/forum/linux-graphics-x-org-drivers/open-source-amd-linux/1296835-debian-is-packaging-rocm-help-them-out)。

同时，一位恰好也是 AMD 员工的 Fedora 打包人员有兴趣亲自[为 Fedora 开发 ROCm 包](https://lists.fedoraproject.org/archives/list/devel@lists.fedoraproject.org/thread/DEZTNQWX627WIOT2P4OPL2ZPA27UJXXL/#DEZTNQWX627WIOT2P4OPL2ZPA27UJXXL)。目前他正在评估利害，并了解其他 Fedora 开发人员希望参与这项工作。

![image1217](/assets/2021/12/17/image1217.jpg) 
>构建 ROCm 软件栈并不是一项微不足道的任务，但 AMD 已经在改进打包方面取得了进展，其他发行版维护者也对此产生了更多兴趣。

几年前，[也有关于为 Fedora 打包 ROCm 的讨论](https://www.phoronix.com/scan.php?page=news_item&px=ROCm-Possible-For-Fedora)，除了 ROCm 软件包的一个子集之外，从未完全实现。 希望这一次事情能够成功，因为看到 ROCm 很容易被前沿的 Fedora 用户使用会很棒。

让我们期待 ROCm 的这种扩展发行版打包在 2022 年推出，以便传统企业 Linux 发行版之外的用户更容易地试验 Radeon 开源 GPU 计算软件。