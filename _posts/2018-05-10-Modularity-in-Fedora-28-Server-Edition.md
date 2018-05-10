---
layout: post
title: Fedora 28 Server 版中的模块化 (Modularity) 新特性
date: 2018-05-10 09:54:09 +0800
---

> 本文翻译自 Stephen Gallagher 的文章 [Modularity in Fedora 28 Server Edition](https://fedoramagazine.org/modularity-fedora-28-server-edition/)。
>
> 关于 Fedora 模块化的探索历史，可以参考本站文章「[Fedora 模块化 (Modularity) 的前世今生]({% post_url 2018-04-03-modularity-dead-long-live-modularity %})」。
>
> —— 译者注

## 模块化 (Modularity) 是什么？

所有的开源发行版都面临着一个经典难题，即「太快/太慢」的问题。用户安装操作系统，是为了能够使用他们的应用程序。像 Fedora 这样的综合发行版，面对海量的可用软件时，既有优势，也有劣势。虽然用户需要的软件包有可用的，但用户可能无法用上真正所需的版本。于是为了解决这一问题，模块化应运而生。

对于某些用户来说，Fedora 有时 *过于前卫*。其发布周期之快、搭载最新稳定版软件的意愿之强，都可能导致应用程序的兼容性脱节。如果因为 Fedora 将 web 框架升级成了不兼容的版本，而导致用户无法运行 web 应用，那无疑会令人非常恼火。对「太快」这一问题的经典说法是「Fedora 应该有一个长期支持版 (LTS release)」。然而，这个办法只解决了问题的一半，而且还会使这个难题的另一半更加棘手。

话分两头，有的时候，对于用户来说 Fedora 又 *太过滞后* 了。例如，Fedora 的发布可能与其他优秀软件的发布时间不太一致。一旦 Fedora 发布了稳定版，打包者就必须遵守[稳定更新策略](https://fedoraproject.org/wiki/Updates_Policy#Stable_Releases)，不能再向系统中引入不兼容的更改。

Fedora 模块化解决了这一令人左右为难的问题。在传统策略下，Fedora 仍将推出标准发行版。但同时，它还将搭载一组提供流行软件替代版本的模块。那些被「前卫」所累的用户依然可以享受到 Fedora 的新内核和其他通用的平台增强特性。而且，他们仍然可以继续使用其应用程序所需要的较旧的框架或工具链 (toolchains)。

此外，那些希望能更快尝鲜的用户，也可以在软件发布后的第一时间用上最新版本。

## 模块化 *不是* 什么？

模块化不是 [Software Collections](https://www.softwarecollections.org/) 的替代品。虽然这两种技术都在试图解决许多相同的问题，但是两者有着明显的差异。

Software Collections 会在系统上并行安装不同版本的软件包。其缺点是，每个安装都只存在于文件系统中它们自己的命名空间的部分 (namespaced portion)。此外，你需要让每个依赖于它们的应用程序都知道它们所在的位置。

在模块化的情况下，系统中只存在一个版本的软件包，而用户可以选择用其中的哪一个版本。其优点是该版本会装到系统的标准位置。软件包不需要对依赖它的应用程序进行特殊更改。从用户调研所获得的反馈来看，大多数用户实际上并不依赖于并行安装。况且容器化 (containerization) 和虚拟化 (virtualization) 完全可以解决这一问题。

## 那为什么不用容器呢？

这是另一个常见的问题。明明用容器就好了，用户为什么还需要模块？答案是，使用容器时，用户需要在容器中维护（相应版本的）软件。模块可以为这些容器提供已经打包好的内容（软件），这样用户就不用自己维护、更新以及打补丁了。Fedora 正是以这种方式，将传统发行版的价值带到了新的、容器化的世界之中。

以下以 Node.js 和 Review Board 为例，来看一下模块化是如何为用户解决问题的。

## Node.js

许多读者可能比较熟悉 Node.js，这是一个广受欢迎的服务器端 JavaScript 运行时。Node.js 有一个偶数/奇数发版策略。其社区对偶数编号的版本（6.x，8.x，10.x 等）的支持大约是 30 个月。而对于奇数编号的版本——实质上是开发者预览版——支持仅为 9 个月。

基于这样的发版周期，Fedora 在稳定版软件仓库中只提供了最新的偶数版本 Node.js，而完全跳过了奇数编号的版本。因为这些奇数版本的生命周期比 Fedora 还短，而且通常还与 Fedora 的发布时间不一致。这样一来，就给一些 Fedora 用户造成了困扰，因为他们希望能及时用上最新、最强大的功能。

得益于模块化，Fedora 28 搭载了三个版本的 Node.js 来满足开发人员和稳定部署的需求。Fedora 28 的传统软件仓库搭载了 Node.js 8.x，这个版本是当前发行周期中的最新的长期稳定版本。Modular 仓库（在 Fedora 28 Server 版上已默认启用）也则提供了 Node.js 6.x 旧版本和更新的 Node.js 9.x 开发版本。

此外，就在 Fedora 28 发布后的短短几天，Node.js 发布了 10.x 上游版本。在过去，用户要等到 Fedora 29 才能够部署该版本，或者只能使用 Fedora 之外的软件来源。不过，好在有了模块化，Node.js 10.x 在 Fedora 28 Modular Updates-Testing 仓库中已经[可用了](https://bodhi.fedoraproject.org/updates/FEDORA-MODULAR-2018-2b0846cb86)。

## Review Board

Review Board 是一个颇受欢迎的 Django 应用，用于执行代码审查。从 Fedora 13 一直到 Fedora 21，Review Board 都可以运行在 Fedora 上。但在此之后，Fedora 升级到 Django 1.7。由于 Django 数据库支持变得向后不兼容，Review Board 便无法再用了。但是它仍然存在于为 RHEL/CentOS 7 提供支持的 EPEL 中，只是因为这些发行版幸运地冻结了 Django 1.6。然而在 Fedora 上，显然它已经走到了尽头。

不过，随着模块化的出现，Fedora 可以以非默认模块流的形式，再次运行旧的 Django。于是，Review Board 可以作为一个模块重新在 Fedora 上运行。上游支持的两个版本，Fedora 都可以提供：2.5.x 和 3.0.x。

## 综上所述

Fedora 一直以来都提供了丰富的软件供用户使用。而现在 Fedora 模块化让大家有了更深层次的选择，大家可以选择需要的软件版本了。未来几年，Fedora 将会非常令人振奋，因为开发者和用户开始拥有全新的（或旧的）激动人心的方式，来汇集我们的软件了。
