---
layout: post
title: Fedora Workstation 38 的新亮点
date: 2023-04-19 12:30:20 +0800
---

## 译文信息

- 原文：[What’s new in Fedora Workstation 38](https://fedoramagazine.org/whats-new-fedora-38-workstation/)
- 作者：[Merlin Cooper](https://fedoramagazine.org/author/mxanthropocene/)
- 许可证：[CC-BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/)
- 译者：[暮光的白杨](https://gitlab.com/Poplar.at.twilight)
- 日期：2023-04-19

----

![cover](/assets/2023/04/fedora/larger_text_dark-1024x433.jpg)

Fedora Workstation 38 是领先的 Linux 桌面操作系统的最新版本，由包括你在内的全球社区共同开发！本文描述了这个新版本的 Fedora Workstation 中一些面向用户的更改。请在今天从 [GNOME 软件]升级系统，或在终端模拟器中使用 [dnf system-upgrade][dnf-up]！

[dnf-up]: https://docs.fedoraproject.org/en-US/quick-docs/dnf-system-upgrade/

## GNOME 44

Fedora Workstation 38 装载了最新版本的 GNOME 桌面环境。GNOME 44 在整个过程中都进行了细微的调整和改进，最显着的变更发生在“快速设置”菜单和“设置”应用程序中。有关更多详细信息，请参阅 [GNOME 44 发行说明]。

[GNOME 44 发行说明]: https://release.gnome.org/44/

### 文件选择器

大多数 GNOME 应用程序都是基于 GTK 4.10 构建的。这引入了一个改进的，带有图标视图和图像预览的文件选择器。

<center>
![01](/assets/2023/04/fedora/File-chooser.png){ width=70% }  
*GTK 4.10 中的新功能：带有图像预览的图标视图*
</center>

### 快速设置调整

GNOME 44 对于“快速设置”菜单进行了多项改进。新版本包括一个引入了快速连接和断开已知蓝牙设备的新功能的蓝牙菜单。多亏了新的字幕，每个快速设置按钮都提供了额外的信息。

<center>
![02](/assets/2023/04/fedora/quick-settings_crpd.jpg){ width=50% }  
*蓝牙菜单现在可用于连接已知设备*
</center>

同样在快速设置菜单中，一个新的后台应用程序功能列出了在没有可见窗口的情况下运行的 Flatpak 应用程序。

<center>
![03](/assets/2023/04/fedora/f38-background-apps_crpd-720x1024.jpg){ width=50% }  
*后台应用程序让你看到在没有可见窗口的情况下运行的沙盒应用程序并关闭它们*
</center>

### 核心应用

GNOME 的核心应用程序在新版本中得到了重大改进。

[设置]已经进行了一轮更新，重点是改善每个设置面板的体验。以下是一些显着的变化：

[设置]: https://apps.gnome.org/zh-CN/app/org.gnome.Settings/

- 鼠标、触摸板以及辅助功能的重大的重新设计显着提高了可用性。
- 更新后的设备安全现在使用更清晰的语言。
- 重新设计的声音现在包括用于音量混合器和警报声音的新窗口。
- 您现在可以通过二维码将您的 Wi-Fi 凭据共享到另一台设备。

<center>
![04](/assets/2023/04/fedora/settings.png){ width=70% }  
*设置中改进的鼠标和触摸板面板*
</center>

在[文件]中，现在有一个选项可以在列表视图中展开文件夹。

[文件]: https://apps.gnome.org/zh-CN/app/org.gnome.Nautilus/

<center>
![04](/assets/2023/04/fedora/files.png){ width=70% }  
*可以在文件的设置中打开树视图*
</center>

[GNOME 软件]现在会自动检查未使用的 Flatpak 运行时并将其删除，从而节省磁盘空间。你还可以选择搜索结果中仅允许出现开源应用程序。

在[联系人]中，您现在可以通过二维码分享联系人，从桌面分享到手机超级简单！

[联系人]: https://apps.gnome.org/zh-CN/app/org.gnome.Contacts/
[GNOME 软件]: https://apps.gnome.org/zh-CN/app/org.gnome.Software/

## 第三方存储库

Fedora 的第三方存储库功能可以轻松启用一系列额外的软件存储库。旧版本的使用了过滤后的 Flathub，只包含少量应用程序。Fedora 38 将不再过滤 Flathub 内容。这意味着第三方存储库现在提供了对 Flathub 的完全访问权限。

你仍然必须手动启用第三方存储库，并且可以从 GNOME 软件设置中禁用各个存储库。如果你不想让专有应用程序出现在你的搜索结果中，你也可以在 GNOME 软件的首选项菜单中启用以下选项：

<center>
![04](/assets/2023/04/fedora/software-config.png){ width=70% }  
*一切尽在掌握*
</center>

## Fedora Linux 38 的底层变化

Fedora Linux 38 具有许多底层更改。以下是一些值得注意的变更：

- 最新的 Linux kernel 6.2 版本带来了扩展的硬件支持、错误修复和性能改进。
- 系统服务可能阻止关机的时间长度已减少。这意味着，如果某项服务延迟了您的机器关闭电源，那么它的破坏性将比过去小得多。
- RPM 现在使用 Rust 编写的 Sequoia OpenGPG 解析器来提高安全性。
- Noto 字体现在是高棉语和泰语的默认字体。Noto CJK 字体的可变版本现在用于中文、日文和韩文。这减少了磁盘使用。
- Fedora 38 的分析将更容易，这要归功于其[默认构建配置的更改][change]。我们期望这将促进未来版本的性能改进。

[change]: https://fedoraproject.org/wiki/Changes/fno-omit-frame-pointer

## 还要看看……

搭载 Budgie 桌面环境和基于 Wayland 的平铺窗口管理器 Sway 的官方 [spin] 版本现已可用！

[spin]: https://spins.fedoraproject.org/