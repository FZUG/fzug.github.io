---
layout: post
title: Fedora Linux 39 Beta 版本现已发布
date: 2023-09-20 12:30:20 +0800
---

- 作品信息：
    - 原文：[Announcing Fedora Linux 39 Beta](https://fedoramagazine.org/announcing-fedora-39-beta/)
    - 作者：[Matthew Miller](https://fedoramagazine.org/author/mattdm/)
    - 译者：暮光的白杨
    - 许可证：[CC-BY-SA 4.0](http://creativecommons.org/licenses/by-sa/4.0/)
    - 译文原文：[Fedora Linux 39 Beta 版本现已发布](https://whiteboard-ui8.pages.dev/translation/fedora39-beta-released/)

---

![](/assets/2023/09/fedora-linux-39-beta-1024x433.jpg)

Fedora 项目很高兴地宣布 Fedora Linux 39 Beta 立即可用，这是我们计划于 10 月底发布 Fedora Linux 39 的下一步。

请从我们的项目网站获取我们任何 Fedora Edition[^1] 的预发行版：

[^1]: Fedora Edition 是指由 Fedora 项目官方维护的多个 Fedora Linux 旗舰版本。

- [Fedora Workstation 39 Beta]
- [Fedora Server 39 Beta]
- [Fedora IoT 39 Beta]
- [Fedora Cloud 39 Beta]
- [Fedora CoreOS “next” stream]

[Fedora Workstation 39 Beta]: https://fedoraproject.org/workstation/download/
[Fedora Server 39 Beta]: https://fedoraproject.org/server/download/
[Fedora IoT 39 Beta]: https://fedoraproject.org/iot/download/
[Fedora Cloud 39 Beta]: https://fedoraproject.org/cloud/download/
[Fedora CoreOS “next” stream]: https://fedoraproject.org/coreos/download/?stream=next

或者你可以尝试 [Fedora Linux Spins] 中的众多不同的桌面变体（例如 KDE Plasma、Xfce 或 Cinnamon）。

[Fedora Linux Spins]: https://fedoraproject.org/spins/

你还可以使用 [DNF system-upgrade] 将现有系统更新为测试版。

[DNF system-upgrade]: https://docs.fedoraproject.org/en-US/quick-docs/upgrading-fedora-offline/

## Beta 版本亮点

在某些方面，这个版本可能主要因为没有什么内容而引人注目。我们计划将 DNF 软件包管理器更新到一个更快的新版本。我们还希望展示期待已久的 Anaconda（我们的安装程序）用户界面更新。然而，我们认为这些事情还未准备完毕。

不过，不要因此而沮丧——这是一个健康的工作过程。多年前，我们在接受一项变更提案后，并不总能找到改变方向的好办法。我们经常发现自己处于这样一种情况：唯一合理的前进方式就是不断前进，即使我们对提供给普通用户的变更并不满意。尽管有些令人失望，但我们现在认识到这些重大变更需要更多投入时间与精力，暂缓发布它们是件好事。

我有一个孩子，每次上课都想拿 100 分（或更高！）。我一直对她说：“事实上，当你有 80% 的时间是对的时候，你才能学得最好。否则，你就没有得到足够的挑战[^2]”。为了履行 Fedora 的创新承诺，我们也需要承担风险。如果一切都按计划进行，那就意味着我们不够努力。与此同时，我们现在的流程允许我们承担这些风险，同时还能确保我们交付的 Fedora Linux 操作系统具有 A+ 的质量。

[^2]: 此处引用的应该是[二八定律]。

[二八定律]: https://wiki.mbalib.com/wiki/80/20%E6%B3%95%E5%88%99

我们仍计划在不久的将来为你带来这些功能，如果你对此感兴趣，请密切关注即将发布的测试公告。

与此同时，请享受 Fedora Linux 发布的许多更新，准备好在这个新测试版中进行测试。

## 值得注意的更新

Fedora Workstation 39 Beta 为我们带来了（本身也是测试版的）GNOME 45。对于每个需要免费开源桌面套件的人来说，LibreOffice 7.6 是不错的选择。

AWS 的 Fedora Cloud 镜像现在默认使用更便宜的 gp3 存储卷。

我们还更新了 GNU 工具链（gcc 13.2、binutils 2.40、glibc 2.38 和 gdb 13.2）。开发人员会感谢我们包含了最新的工具，并且这些更新还包括安全性和性能方面的改进，这将使所有使用 Fedora Linux 的人受益。

## 需要测试

由于这是测试版，我们预计你可能会遇到错误或缺少功能。要报告测试期间遇到的问题，请通过[测试邮件列表]或 Fedora Chat 上的 [#quality 频道]联系 Fedora 质量团队。随着测试的进行，我们将在 [Ask Fedora] 的 [“Common Issues” 类别]中跟踪常见问题。

有关有效报告错误的提示，请阅读[如何提交错误]。

[测试邮件列表]: https://lists.fedoraproject.org/archives/list/test%40lists.fedoraproject.org/
[#quality 频道]: https://chat.fedoraproject.org/#/room/#quality:fedoraproject.org
[“Common Issues” 类别]: https://discussion.fedoraproject.org/tags/c/ask/common-issues/82/f39
[Ask Fedora]: https://discussion.fedoraproject.org/c/ask/6/none
[如何提交错误]: https://docs.fedoraproject.org/en-US/quick-docs/howto-file-a-bug/

## 什么是测试版？

测试版是代码完整的版本，与最终版本非常相似。如果你花时间下载并试用测试版，你就可以检查并确保那些对你很重要的功能正常运行。你发现并报告的每一个错误不仅能帮助你，还能改善全球数百万 Fedora Linux 用户的体验！我们一起努力，让 Fedora 坚如磐石。我们的文化是尽可能地协调新功能并向上游推送修复。你的反馈不仅改善了 Fedora Linux，也改善了整个 Linux 生态系统和自由软件。

## 更多信息

有关 Fedora Linux 39 Beta 版新内容的详细信息，请查阅 [Fedora Linux 39 变更集]。它包含了更多有关新软件包的技术信息和随该版本一起发布的改进。

[Fedora Linux 39 变更集]: https://fedoraproject.org/wiki/Releases/39/ChangeSet