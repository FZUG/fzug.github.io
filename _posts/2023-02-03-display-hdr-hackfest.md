---
layout: post
title: 显示/HDR 黑客节的公告
date: 2023-02-03 22:30:20 +0800
---

## 译文信息

- 源文：[Announcing the Display/HDR hackfest](https://fedoramagazine.org/announcing-the-display-hdr-hackfest/)
- 作者：[Carlos Soriano](https://fedoramagazine.org/author/csoriano/)
- 许可证：[CC-BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/)
- 译者：[Hanjingxue Boling](https://github.com/Hanjingxue-Boling)
- 日期：2023-02-03

----

>![cover](/assets/2023/01/HDR-Display_Hacker_fest-1024x433.jpg)
>*由 Jakub Steiner 提供，免费许可，无版权。*

大家好，

我是[红帽](https://en.wikipedia.org/wiki/Red_Hat) GPU 团队的工程经理 Carlos Soriano。我与红帽的主要 HDR 开发人员 Sebastian Wick 和红帽的 GPU 团队产品负责人 Niels de Graef 一起宣布我们将于 4 月 24 日至 26 日在[捷克共和国的布尔诺](https://en.wikipedia.org/wiki/Brno)组织**显示/HDR 黑客节**（Display/HDR [hackfest](https://en.wikipedia.org/wiki/Hackathon)）！活动的重点将放在规划和开发各种显示技术所需的技术基础设施上，特别是那些需要 GNOME Shell 与 GPU 堆栈协同工作的技术。这方面的主要示例之一是 HDR 支持，我们知道你们都在等待着它的到来！

## 详细信息

本次黑客节的目的是汇集来自显示、GPU 技术栈的贡献者。与会者将包括来自 [Freedesktop](https://freedesktop.org)、[GNOME](https://www.gnome.org/)、[KDE](https://kde.org/)、[Mesa](https://mesa3d.org)、[Wayland](https://wayland.freedesktop.org/) 和 [Linux 内核](https://www.kernel.org)等项目的成员。这将会是一个很好的，针对使相关技术在不同的供应商和项目中良好运作所需的整体方案进行会面、商讨与合作的机会。

黑客节的建议长度为两整天，第三天上午结束，下午在当地进行一些活动。

你现在可能会问为什么这些技术（例如 HDR）对我们很重要？将它们集成到 Fedora 中的计划是什么？好吧，让我们以 HDR 为例，首先解释一下 HDR 是什么。

## 什么是 HDR？— Sebastian Wick

当大多数人谈论[高动态范围](https://en.wikipedia.org/wiki/High_dynamic_range)（HDR）时，他们通常不仅指 HDR，还指[广色域](https://en.wikipedia.org/wiki/Gamut#Wide_color_gamut)（WCG）。这两个术语都描述了显示技术的改进。

显示器的动态范围描述了它可以同时产生的最低亮度与最高亮度的比率。因此，高动态范围意味着最高亮度的增加（通俗地称为更亮的白色）或最低亮度的降低（更暗的黑色），或两者兼而有之。

显示器的色域描述了它能够再现的颜色。如果色域较“宽”，则意味着与小色域相比，显示器能够再现更多的色度。通俗点说，就是可以展现更多的色彩。

人类能够感知具有一定动态范围和色域的图像。显示器越接近人类的感知水平，生成的图像就越让人身临其境。因此，从更广泛的意义上讲，HDR 就是能够显示更多颜色，我们可以在显示器上使用 HDR 模式来释放它们的全部潜力。

从技术角度来看，启用这些模式并呈现内容并不难，显示器只需显示单一来源即可。虽然这可能适用于某些用例，但通用桌面需要将各种[标准动态范围](https://en.wikipedia.org/wiki/Standard-dynamic-range_video) (SDR) 信号、色彩管理 SDR 信号和各种 HDR 信号同时组合到各种 SDR 显示器、色彩管理显示器和 HDR 显示器。如果你想看看正确完成后的效果，你可以看看 [Apple 的 EDR 概念](https://prolost.com/blog/edr)。

目前还没有针对这种合成的行业标准，而且大多数 HDR 模式（不幸的是所有常见模式），也不是为这种用例设计的。相反，他们专注于呈现单个 HDR 源。

随着合成复杂性的增加，卸载合成和实现零拷贝直接扫描场景变得更加困难。但这也需要控制功耗，从而延长电池寿命。

## 哇，这听起来很复杂

是的，这一切听起来比人们想象的要复杂得多，但我们相信我们能做到。现在，为什么 HDR 对我们很重要；一旦准备就绪，我们将其集成到 Fedora 中的计划是什么？Niels de Graef 在红帽已经作为主要的 HDR 功能负责人工作了几个月，可以帮助我们理解这一点。

## 为什么 HDR 对我们很重要，我们将它集成到 Fedora 中的计划是什么？— Niels de Graef

通过增加对 HDR 的支持，我们希望为几个关键群体赋能。

一方面，我们希望支持将 HDR 视为一项非常有趣的功能的内容创作者。HDR 允许内容创作者以他们希望看到的方式向人们展示他们的作品，消除由于显示器仅支持相对较小的色彩空间而导致的色彩“褪色”的影响。例如：作为一名艺术家，您可能想要准确指定沙漠场景中的太阳应该看起来有多亮，同时确保场景的其余部分不会在细节上失真。

随着内容创作者的发展，一个重要的利益相关者是[视效](https://en.wikipedia.org/wiki/Visual_effects)行业，它由像迪士尼这样的大公司组成。红帽与视效业界密切合作，业界也[推荐](https://drive.google.com/file/d/12k-YZVHuxJs0LVKH_l6l9nf_qcYLfaLJ/view)使用 RHEL 作为他们的发行版选择。我们想确保我们得到行业的反馈，以便我们可以将其纳入这一层，并确保我们从一开始就做对了。

另一方面，我们希望为 Linux 用户赋能。支持 HDR 的硬件正变得越来越普遍，并且最近变得越来越便宜。HDR 得到越来越多的支持，越来越多的内容正在使用它。与 Windows 和 Mac 用户相比，只要我们没有 HDR 支持，Linux 用户的体验就会下降。

最后，支持 HDR 符合 [Fedora 项目的原则](https://docs.fedoraproject.org/en-US/project/)，我们希望做正确的事情，确保每个人都可以自由享受最新的创新和功能。在我们迁移到 Wayland 之后，Wayland 作为一个现代图形堆栈，允许我们构建这样的新功能。

## 结语

我们希望你能喜欢我们为使 Linux 生态系统和用户能够使用最新技术所做的工作。我们对我们要实现的目标感到非常兴奋。感谢所有为这项工作做出贡献的人，以及黑客节的组织者。

我们希望四月份在布尔诺见到大家！

— *Carlos Soriano,*  
*2023-02-03*