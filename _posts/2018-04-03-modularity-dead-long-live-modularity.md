---
layout: post
title: Fedora 模块化 (Modularity) 的前世今生
date: 2018-04-03 17:12 +0800
---

> 本文翻译自 Stephen Gallagher 的文章 [Modularity is Dead, Long Live Modularity!](https://communityblog.fedoraproject.org/modularity-dead-long-live-modularity/)。
>
> 原文发布于 2017 年 12 月 19 日，其中涉及的相应技术已有进展，文中亦有译者添加的更新标示。
>
> 在 2018 年 5 月发布的 Fedora 28 Server 版中，模块化仓库已正式推出，相关介绍请移步本站文章「[Fedora 28 Server 版中的模块化 (Modularity) 新特性]({% post_url 2018-05-10-Modularity-in-Fedora-28-Server-Edition %})」。
>
> 本文仅作为 Fedora 模块化的探索历史供读者参考。
>
> —— 2018 年 5 月 10 日，译者注

## 概览

Fedora 模块化 (Modularity) 的初衷是希望打包者可以轻松创建软件的替代版本，并让用户可以更简单地使用这些软件流 (streams)。经过多年研究，我们终于在 2017 年的夏天推出了「 Boltron 」原型及之后的 Fedora Modular Server beta。从收到的反馈来看，这些测试版本并没有达到预期目标，因而我们会将其纳入我们认为 *可能的* 设计修改中。我们计划通过 [Devconf.cz](https://devconf.cz/) 和 [FOSDEM](https://fosdem.org/2018/schedule/track/distributions/) 来演示新进展。

> 注：
>
> 后续确实有设计修改，详见下文。
>
> Fedora 开发人员 Adam Samalik 在 FOSDEM 2018 上讲解 Fedora 模块化的视频链接在 <https://www.youtube.com/watch?v=Z0-djnoOxRs>

## 回顾

也许你已经读过[这篇文章](https://fedoramagazine.org/fedora-27-server-classic-release/)，模块化工作组 (Modularity Working Group) 和 Server 工作组的各位成员认为，以模块化当时的完成度来说，还不足以在 Fedora 27 发布时达到 Fedora 预期的水准——即使 Fedora 理事会 (Fedora Council) 再给项目组放宽时限也不行。特别是，Fedora 27 Modular Server Beta 版的反馈中还提出了一系列设计上的问题。

最常见的用户反馈类似这样：「我该如何在 Modular Server 安装某某某软件包？」其中，「某某某」指代的是各种软件，不管是 `screen` 之类的软件还是各种复杂的第三方应用。在当时的模块化版本中，整个系统只能全是模块，或者全否。想要软件在一个全是模块的系统上可用，其软件包必然也要是模块的一部分——只可惜，我们并没有完成太多。

可用模块的匮乏意味着打包者的参与度不高。究其原因，一部分是因为在实现和设计上有分歧，还有就是创建模块终究还是一件比我们预期的要更耗时、学习曲线更陡峭的工作。此外，整个过程不够明确与社会化 (well-socialized)。整个团队制订了大量文档，但是打包者们需要尽数理解之后才能上手。结果，原计划要配合 F27 Modular Server 推出的模块都要由模块化团队来开发，在希望要推出的海量软件包面前，团队显得心有余而力不足。

当然，其中的羁绊不止于此。我们并没有一个好的方案来实现从传统方式安装的版本到新版的升级操作。对于软件应当如何加载到系统上，我们也没有一个统一的表述。而且，我们最终也还是需要一个「引导 (bootstrap) 」模块（下文中会解释）。所以，我们最终决定放弃在 Fedora 27 中发行 Modular Server，退而求其次，发布了[传统版的 Fedora 27 Server](https://getfedora.org/server/)。

## 继续前行

考虑以上种种，我们决定将模块化方案回炉重造。我们分析了原计划要提供的诸多功能，斟酌取舍。

首先最重要的就是，我们放弃了要创建一个严格维护的稳定 buildroot 的想法。传统的 Fedora 是通过在 buildroot 中构建一个 RPM 包来进行构建的，该 buildroot 中包含了一个发行版的稳定版更新仓库中的最新可用软件包（当需要构建预发行的包时还要加上超前的仓库）。借助模块化，我们希望可以定义一个小而具体的 buildroot 以使其可以在一个版本的发行生命周期中保持稳定且得以维护。我们可以用以构建每一个模块。结果证明，我们想实现这件事几乎不可能。因为这需要我们找到一个可以用于构建整个 buildroot *自身*的一个点，这可是一番多年都不曾在 Feodra 中实现的大业（如果有人尝试过的话）。 Fedora 软件包确实只保证针对其*已完成*构建的 buildroot 上可以构建成功。如果针对其他的 buildroot 也可以构建的话，那也可以算幸运了。

其中的缘由可能有很多——构建过程的依赖链中某一环缺失；编译器或链接器引出的 bug 导致的中断；编译器的新默认标记致使警告变成错误，等等等等。这些都是亟待满足的需求，却从来没有实现过。我们用了一个不甚优雅的「引导」模块来解决，用于提供一系列从非模块的 Fedora 中复制过来的软件包，来进行平台构建。在此基础上，**我们终于可以针对标准 Fedora buildroot 来构建模块了**。

![modularity-hybrid-dist-git-and-buildroot](/assets/2018/04/03/modularity-hybrid-dist-git-and-buildroot-1-1.png)

至此，我们的 buildroot 可以访问针对 Fedora 构建的全部内容了，我们便开始重新考虑另一个最初的目标，即将其做成一个其他模块都能依赖的提供模块的基础平台。最初，这是为了定义基础平台所提供的 API，并标示出哪些部分可以被视为稳定。但是，Fedora 已经有了一个稳定的更新策略作为发行版本的准则。该策略只有少数几个例外（针对内核、KDE 等），大部分情况下，从我们发布 Beta 版开始，平台 API 就会在 Fedora 发行版的生命周期中保持稳定。放宽这一要求后，我们意识到我们可以消除（或至少推迟）要有一个指定的平台模块这一需求。

我们决定取而代之的是将 Fedora 的 Everything 仓库（基本上囊括了一个 Fedora 发行版本中的全部可用包）作为「平台模块」，尽管工具并不会将这些内容报告为一个模块。在实际操作中，这意味着**模块的创建者不必再焦头烂额地去回溯到底是哪一个模块提供所需的依赖了**。他们可以依赖 Everything 仓库中可用的系统版本了。如果其版本不能满足所需，还有其他选项，我们后面再详述。

这为我们带来了几个优势，包括**可以从传统方式部署的版本上直接升级过来**，因为我们会同时保留传统仓库以及一组默认模块。这意味着，当前的 Fedora 27 系统可以直接升级到模块化的 Fedora 28 系统，无需任何特殊的步骤。实际上，这种方法也意味着模块化不会局限于服务器版 (Server Edition)。

这种变化的另一个实际优势是**模块的创建将变得 *非常* 简单**。现在，模块只需要描述与基础仓库的差异部分，而不必管复杂的软件包及其所有依赖关系。例如，Fedora 28 将在标准存储库中提供 Node.js 8.x LTS 版本，同时我们还可以构建一个模块来提供 9.x 实验版本作为可选项。我们还可以轻松地提供较旧的 6.x LTS 版本来支持早期的应用程序。在这些情况下，我们可以发布非常简单的模块描述，其中只需要列出与所需上游版本匹配的 dist-git 分支即可。

这样，**我们原计划的提供工具来自动创建这些模块**这一构想就会变得非常简单了。由于大多数模块现在只需要单个源代码包在组件列表中，因而我们计划，对所有在支持周期内的 Fedora（以及 EPEL）发行版，启用自动构建 dist-git 中单个分支的支持。即使对于更复杂的多软件包模块 (multi-package modules)，自动创建的模块描述也可以提供一个简单明了的起点。

![modularity-hybrid-one-branch-builds-them-all](/assets/2018/04/03/modularity-hybrid-one-branch-builds-them-all.png)

## 最终如何呈现？

![modularity-hybrid-repos-and-terminal](/assets/2018/04/03/modularity-hybrid-repos-and-terminal.png)

从一个最终用户的角度来看，**Fedora 将会搭载两套软件仓库**。其中一个是传统的 Fedora 仓库（fedora、updates 和 updates-testing），另一个是一组提供替代和补充模块的全新仓库。最终的名称尚未定下，我们姑且称之为 modular、modular-updates 和 modular-updates-testing 吧。

> 注：
>
> 2018 年 4 月 3 日，Fedora 28 Beta 版发布。前文中所述的设计构想基本已经实现，modular、modular-updates 和 modular-updates-testing 仓库也已预装在 Fedora 28 Beta Server 上。 nodejs 等模块已经可以安装体验了。更多的可用模块也已经在路上了。
>
> 除了 Server 版，Workstation 等其他 Fedora 版本上也是可以体验模块化仓库的，只不过 GNOME Software 和 Plasma Discover 中还没办法安装模块，因为需要 libdnf 的支持，尚需时日。
>
> 目前对于 Fedora 桌面用户来说，建议最好还是在容器中启用模块仓库来进行测试体验。
>
> 参见 <https://fedoramagazine.org/announcing-fedora-28-beta/>。

借此设计，**对于模块提供的附加软件版本没有需求的人，可以禁用 modular 和 modular-updates 这两个仓库，其系统的功能也会安然无恙**。使用 Fedora 传统程序构建的软件包将通过常规 Fedora 软件仓库安装和管理，而默认版本的软件包则将在后台使用新的进程。

**对于软件其他版本有需求的人，使用新的模块仓库便能满足所需**。用户可以使用 DNF 中的一些新语法来与这些新的软件仓库进行交互，与 Modular Server Beta 中的用法一致。如果用户想要安装特定的模块流 (module stream)，可以执行 `dnf install module[:stream[/profile]]`（例如 `dnf install @nodejs:6` 就会安装包含 nodejs 和 npm 包的默认配置文件）。如果用户只想安装该系统的默认版本，那么依然可以使用 `dnf install 包名` 的方式。

> 注：
>
> 最新的用法示例如下——
>
> 列出全部可用模块 `dnf module list --all`
>
> 安装模块（以 nodejs 8 为例） `dnf module install nodejs:8/default`
>
> 移除模块（以 nodejs 8 为例） `dnf module remove nodejs:8/default`
>
> 更多用法请执行 `man dnf`，然后搜索 `Module Command`。

## 结语

这份改进过的计划为模块化创造了一个可理解、可触及、可交付的未来。不想创建模块的打包者还可以继续按以往的方式打包，**无需修改**其工作流程。希望在单个发行版本中提供软件的替代版本，或者在多个发行版本中轻松提供相同版本软件的打包者，可以使用新的工具来简化这一流程。

随着可用模块数量的增加，Fedora 用户将可以更轻松地用上所需指定版本的软件。快速原型开发人员可以更轻松地用上更新版本的软件包，与此同时运行较旧程序的用户依然可以继续使用他们所需的旧软件流。

> 注：
>
> 2018 年 3 月 16 日，Fedora 模块化项目的主导者 [Langdon White](https://twitter.com/1angdon) 在 Fedora 播客节目的第三期中对模块化做了分享，有兴趣的朋友可以访问 [Fedora Podcast 003 — Fedora Modularity](https://fedoramagazine.org/fedora-podcast-003-fedora-modularity/) 收听。
>
> 2018 年 4 月 10 日，Fedora 模块化团队举办了「测试日活动 (Add-On Modularity Test Day)」，大家记录了在测试 Fedora 模块化的过程中遇到的问题，可以参考 <https://fedoraproject.org/wiki/Test_Day:2018-04-10_Add-On_Modularity_Test_Day>。
