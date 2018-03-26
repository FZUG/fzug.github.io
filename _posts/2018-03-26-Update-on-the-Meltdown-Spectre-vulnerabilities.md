---
layout: post
title:  "对“熔断” (Meltdown) 和“幽灵” (Spectre) 漏洞的一些跟进"
date:   2018-03-26 14:01:00 +0800
---

今年一月份，一系列名为[“幽灵” (Spectre) 和 “熔断” (Meltdown)]({% post_url 2018-01-18-protect-fedora-system-meltdown %}) 的漏洞被公之于众。问题的性质决定了解决方案非常复杂，并且需要修复相关代码。x86 架构上对“熔断” (Meltdown) 的最初修复 KPTI 几乎在公开时立即发布了。对“幽灵” (Spectre) 漏洞的修补则更加复杂。当然其他架构也需要审视受影响的状态，并且在需要的情况下做出修复。到现在为止已经过去了相当的时间，那么 Fedora 的修复状况如何呢？

## “熔断” (Meltdown) 和“幽灵” (Spectre) 漏洞修补的覆盖情况

对“幽灵” (Spectre) 和 “熔断” (Meltdown) 漏洞的修补状态相当好。对于 x86 架构，[KPTI]({% post_url 2018-02-25-kpti-new-kernel-feature-mitigate-meltdown %}) 修补了“熔断”漏洞 ([CVE-2017-5754](https://access.redhat.com/security/cve/cve-2017-5754))，**retpoline** 技术修补了“幽灵”漏洞的变体 2 ([CVE-2017-5715](https://access.redhat.com/security/cve/cve-2017-5715))。修补“幽灵”漏洞变体 1 ([CVE-2017-5753](https://access.redhat.com/security/cve/cve-2017-5753)) 需要的代码和相关问题已经在上游得到了修补。此外，对 ARM 架构的修补已经随着 4.15.4 以及更高版本的内核更新进入 Fedora。Power 架构的初始修复自 4.15.4 版内核起进入了 Fedora。

上述修补仍然在改进中。最初的修补目的是尽快修补缺陷从而使用户免于暴露在危险中。这个过程结束后，开发人员可以有更多精力弥补上述补丁带来的性能问题。

鉴于上述修补的状态，Fedora 内核团队已经将这些 CVE 的故障报告予以关闭。由于对现有修补的优化仍然在进行，并且会在不久的将来可用，所以及时更新你的内核版本仍然非常重要。由于很多修补依赖 CPU 微码 (microcode) 的更新，所以请尽可能确保固件也是最新的。

> 本文翻译自 Justin Forbes 的文章 [Update on the Meltdown & Spectre vulnerabilities](https://fedoramagazine.org/update-meltdown-spectre-vulnerabilities/)。
