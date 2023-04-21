---
layout: post
title: Fedora Linux 38 现已正式发布
date: 2023-04-19 12:30:20 +0800
---

## 译文信息

- 原文：[Announcing Fedora Linux 38](https://fedoramagazine.org/announcing-fedora-38/)
- 作者：[Matthew Miller](https://fedoramagazine.org/author/mattdm/)
- 许可证：[CC-BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/)
- 译者：[暮光的白杨](https://gitlab.com/Poplar.at.twilight)
- 日期：2023-04-19

-----

![cover](/assets/2023/04/fedora/f38-1024x433.jpg)

今天，我很高兴与大家分享数千位 Fedora 项目贡献者辛勤工作的成果：Fedora Linux 38 版本发布了！通过此版本，我们开始了一个新的按时发布的纪录。事实上，我们提前一周准备好了！

和之前一样，在从旧版本升级之前，你应该确保你的系统已全部更新至最新的状态。等不及要开始了吗？请边阅读边[下载][new-site]吧！

[new-site]: https://fedoraproject.org/

## 新站点

你是否点击了上面的下载链接呢？你可能已经注意到该[网站][new-site]看起来有所不同。这是我们的网站和应用程序团队、设计和基础设施团队以及整个社区合作一年多的结果。现在，你将看到我们五个版本中每个版本[^edition]的页面，但这只是一个开始。[Spin] 和 [Lab] 网站将在未来更新。最终，这将为把我们更多的网站整合在一起奠定基础。我对视觉上的更新感到非常兴奋，因为这将使我们的网站对 Fedora 内部的团队提供更多的自助服务——同时我也为团结起来创造这个奇迹的社区团队感到非常自豪。

[^edition]: 版本（Edition）是 Fedora 官方正式维护的旗舰版本。

[spin]: https://spins.fedoraproject.org/
[lab]: https://labs.fedoraproject.org/

## 新 spin

Fedora Linux 38 引入了几个新的 Spin——展示不同桌面环境的变体。流行的 [Budgie] 桌面环境首次在 Fedora 37 获得打包，现在它有了自己的 Spin。[Fedora Budgie Spin] 旨在为 Fedora Linux 用户提供首屈一指的 Budgie 桌面体验，这是一个面向开发者和用户的领先平台。

对于平铺窗口管理器的粉丝们，我们现在提供基于 [rpm-ostree]，装载了窗口管理器 [Sway] 的 Spin 版本，代号为 “[Sericea]”。Sway 使用现代的 Wayland 协议，旨在成为 [i3 窗口管理器]的直接替代品。

[sway]: https://swaywm.org/
[Sericea]: https://fedoraproject.org/sericea/
[rpm-ostree]: https://rpm-ostree.readthedocs.io/en/latest/
[i3 窗口管理器]: https://i3wm.org/
[Budgie]: https://blog.buddiesofbudgie.org/
[Fedora Budgie Spin]: https://fedoraproject.org/spins/budgie/

如果您想在移动设备上使用 Fedora Linux，可以关注一下 Fedora 38 引入的 [Phosh 镜像][phosh-spin]。[Phosh] 是一个基于 Gnome 的移动设备的 Wayland shell。这是我们的 [Mobility SIG] 的早期成果。如果您的设备尚不受支持，我们欢迎您的贡献！

[phosh]: https://fedoraproject.org/spins/phosh/
[phosh-spin]: https://fedoraproject.org/spins/phosh/
[Mobility SIG]: https://fedoraproject.org/wiki/Mobility

## 桌面改进

Fedora Workstation 专注于桌面体验。与往常一样，Fedora Workstation 采用了 GNOME 最新的版本。[GNOME 44] 包含许多重大改进，包括新的锁定屏幕、快捷菜单上的“后台应用程序”部分，以及对辅助功能设置的改进。此外，启用第三方软件仓库后，你可以在 Flathub 上以未经过滤的方式查看应用程序。

[GNOME 44]: https://release.gnome.org/44/

在此版本中，我们缩短了服务关闭时的默认超时时间。这有助于你的系统更快地关闭电源——这在你需要拿起笔记本电脑离开时非常重要。

当然，我们生产的不仅仅是 Edition。[Fedora Spins][spin] 和 [Labs][lab] 面向各种受众和用例，包括为计算神经科学提供工具的 [Fedora Comp Neuro]，以及提供轻量级桌面环境的 [Fedora LXQt] 等桌面环境。而且，不要忘记我们的替代架构：[ARM AArch64、Power 和 S390x][alt]。

[Fedora Comp Neuro]: https://labs.fedoraproject.org/en/comp-neuro/
[Fedora LXQt]: https://spins.fedoraproject.org/en/lxqt/
[alt]: https://alt.fedoraproject.org/alt/

## 系统管理改进

Microdnf——默认包管理器的轻量级版本——被 dnf5 取代。[dnf5] 带来了性能改进、更小的内存占用，以及可以提供 [PackageKit] 替代方案的新守护进程。在 dnf5 成为 Fedora Linux 未来版本的默认设置之前，你现在可以开始测试它。

[dnf5]: https://dnf5.readthedocs.io/en/latest/
[PackageKit]: https://www.freedesktop.org/software/PackageKit/

对于大型机管理员，我们将 IBM Z 硬件的最低架构级别提高到 z13。这使你能够受益于该平台的新功能并获得更好的 CPU 性能。

我们始终努力为用户快速带来新的安全功能。我们现在使用更严格的编译器标志构建软件包，以防止缓冲区溢出。rpm 包管理器现在使用[基于 Sequoia 的 OpenPGP 解析器][openpgp]而不是它自己的实现。

[openpgp]: https://sequoia-pgp.org/

## 其他更新

如果您正在分析应用程序，您会喜欢现在内置在官方包中的帧指针（framer pointer）[^pointer]。这使得 Fedora Linux 成为寻求提高 Linux 应用程序性能的开发人员的绝佳平台。

[^pointer]: 另见：Fedora Wiki 上的[原始提案](https://fedoraproject.org/wiki/Changes/fno-omit-frame-pointer)和 imbearchild 的《[Fedora 38 的帧指针](https://imbearchild.cyou/archives/2023/02/fedora-38-frame-pointer/)》

基于我们“第一”的[原则][rule]，我们更新了关键的编程语言和系统库包，包括 gcc 13、Golang 1.20、LLVM 16、Ruby 3.2、TeXLive2022 和 PHP 8.2 等等。

[rule]: https://docs.fedoraproject.org/en-US/project/

我们很高兴你能体验新版本！请转到 <https://fedoraproject.org/> 并立即下载。如果你已经在运行 Fedora Linux，请按照[简单的升级说明][upgrading]进行操作。有关 Fedora Linux 38 中新功能的更多信息，请参阅[发行注记]。

[upgrading]: https://docs.fedoraproject.org/en-US/quick-docs/upgrading/
[发行注记]: https://docs.fedoraproject.org/en-US/fedora/f38/release-notes/

## 万一出现问题……

如果你遇到了问题，请访问我们的 [Ask Fedora] 用户支持论坛。这里有一个包括[常见问题]的类别（中文语言类别另见[此处]）。

[Ask Fedora]: https://ask.fedoraproject.org/
[常见问题]: https://discussion.fedoraproject.org/tags/c/ask/common-issues/82/none/f38/l/latest
[此处]: https://discussion.fedoraproject.org/tags/c/ask/non-english/85/chinese-simplified

## 感谢所有人

感谢在此发布周期中为 Fedora 项目做出贡献的许多人。我们为你加入 Fedora 社区感到欣喜。我[^me]希望在今年 8 月于[科克]回归的 [Flock to Fedora] 活动上见到你。

[^me]: 即 Matthew Miller

[Flock to Fedora]: https://flocktofedora.org/
[科克]: https://en.wikipedia.org/wiki/Cork_(city)