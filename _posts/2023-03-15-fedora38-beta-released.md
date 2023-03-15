---
layout: post
title: Fedora 38 Beta 版已发布
date: 2023-03-15 09:30:20 +0800
---

## 译文信息

- 源文：[Announcing Fedora Linux 38 Beta](https://fedoramagazine.org/announcing-fedora-38-beta/)
- 作者：[Matthew Miller](https://fedoramagazine.org/author/mattdm/)
- 许可证：[CC-BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/)
- 译者：[暮光的白杨](https://gitlab.com/Poplar.at.twilight)
- 日期：2023-03-15

----

![cover](/assets/2023/03/f38-beta-1024x433.jpg)

Fedora 项目很高兴地宣布 Fedora Linux 38 Beta 现已发布，这是我们计划在 4 月底发布的 Fedora Linux 38 的下一步。

请从我们的 Get Fedora 站点下载这个预发行版：

- [获取 Fedora 38 Workstation Beta](https://getfedora.org/workstation/download/)
- [获取 Fedora 38 Server Beta](https://getfedora.org/server/download/)
- [获取 Fedora 38 IoT Beta](https://getfedora.org/iot/download/)
- [获取 Fedora 38 Cloud Beta](https://alt.fedoraproject.org/en/cloud/)
- [获取 Fedora 38 CoreOS Beta](https://getfedora.org/en/coreos/download)
- [中国大陆镜像站列表](./../guide/misc/mirror-site.md)

或者，查看我们流行的变体分支，其中包括 KDE Plasma、Xfce 和其他桌面环境，以及用于 ARM 设备（如 Raspberry Pi）的镜像：

- [获取 Fedora Spins 38 Beta](https://spins.fedoraproject.org/prerelease)
- [获取 Fedora Labs 38 Beta](https://labs.fedoraproject.org/prerelease)
- [获取 Fedora Linux 38 Beta for ARM](https://arm.fedoraproject.org/prerelease)

## 测试版亮点

### Fedora Workstation

Fedora 38 Workstation Beta 搭载了 GNOME 44。它目前处于测试阶段，最终版本预计在 3 月底发布。GNOME 44 包含许多重大改进，包括新的锁定屏幕、快捷菜单上的“后台应用程序”部分以及对辅助功能设置的改进。此外，启用第三方存储库现在可以在 Flathub 上以未经过滤的方式查看应用程序。

### 其他更新

我们始终努力为用户快速带来新的安全功能。软件包现在使用更严格的编译器标志构建，以防止缓冲区溢出。rpm 包管理器使用基于 Sequoia 的 OpenPGP 解析器而不是它自己的实现。

如果你正在分析应用程序，你会喜欢现在内置在官方包中的帧指针。这使得 Fedora Linux 成为寻求提高 Linux 应用程序性能的开发人员的绝佳平台。

当然，还有编程语言和库的常规更新：Ruby 3.2、gcc 13、LLVM 16、Golang 1.20、PHP 8.2 等等！

## 需要测试

由于这是 Beta 版本，我们预计你可能会遇到错误或缺少功能。要报告测试期间遇到的问题，请通过[测试邮件列表](https://lists.fedoraproject.org/archives/list/test%40lists.fedoraproject.org/)或 Fedora Chat 的 #quality 频道联系 Fedora QA 团队。随着测试的进行，常见问题会在 Ask Fedora 的[“常见问题”类别](https://discussion.fedoraproject.org/tags/c/ask/common-issues/82/f38)中进行跟踪。

有关有效报告错误的提示，请阅读[如何提交错误报告](https://docs.fedoraproject.org/en-US/quick-docs/howto-file-a-bug/)。

## 什么是测试版？

Beta 版本是代码完整的，与最终版本非常相似。如果你花时间下载并试用 Beta 版，你可以检查并确保对你来说重要的事务正常运行。你发现和报告的每个错误不仅能帮助你，还能改善全球数百万 Fedora Linux 用户的体验！我们可以一起让 Fedora 坚如磐石。我们有一种协调新功能并尽可能向上游推送修复的文化。你的反馈不仅改进了 Fedora Linux，而且改进了整个 Linux 生态系统和自由软件。

## 更多信息

有关 Fedora Linux 38 Beta 版新功能的更多详细信息，你可以查阅 [Fedora Linux 38 Change set](https://fedoraproject.org/wiki/Releases/38/ChangeSet)。它包含有关此版本随附的新软件包和改进的更多技术信息。