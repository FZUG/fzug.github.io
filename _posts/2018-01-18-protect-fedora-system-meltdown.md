---
layout: post
title:  "保护你的 Fedora 系统，防范“熔断” (Meltdown) 漏洞"
date:   2018-01-18 12:06:41 +0800
---

你或许已经听过 “熔断”(Meltdown) 漏洞，这是[一种可以用来攻击现代处理器 (CPU)](https://meltdownattack.com/) 的漏洞，它可以恶意地访问内存中的敏感数据。
这个漏洞很严重，会暴露您的敏感数据，比如密码。以下是如何保护您的 Fedora 系统免受“熔断”漏洞攻击的方法。

## 防范“熔断”漏洞

新的内核包包含 Fedora 26 和 27 的补丁（内核版本4.14.11），以及 Fedora Rawhide 的补丁（内核4.15发布候选版本）。
维护人员已经向稳定的软件仓库提交了更新。对于大多数用户来说，它们应该在一天内就可以用上了。

要更新您的 Fedora 系统，如果您配置了 sudo 命令，请使用如下命令。如果需要，请在提示符处输入密码。

````bash
sudo dnf --refresh update kernel
````

Fedora 在世界范围内、在许多站点上提供了镜像，以更好地为用户服务。这些镜像网站的同步速度各不相同。如果你没有马上得到更新，那就等到当天晚些时候。

如果你的系统是 Rawhide，运行 `sudo dnf update` 来得到更新。

然后，重启你的系统并使用最新的内核。

## Fedora Atomic Host

对 Fedora Atomic Host 的补丁在 ostree v27.47 上。为了得到更新，可运行如下命令：

````bash
atomic host upgrade
````

然后重启你的系统。

你可以阅读 [Project Atomic 的博文](http://www.projectatomic.io/blog/2018/01/fedora-atomic-4-jan-18/)来获取更多细节。

## 关于“幽灵” (Spectre) 漏洞的提示

“幽灵” (Spectre) 是另一个严重漏洞的常见名称，它利用处理器和软件的设计缺陷来恶意地获取私密数据。
上游开发人员、供应商和研究人员正在进行工作以缓解这个弱点。
Fedora 团队将继续跟进他们的进展，并在补丁可用时通知公众更新。

> 本文翻译自 Paul W. Frields 的文章 [Protect your Fedora system against Meltdown](https://fedoramagazine.org/protect-fedora-system-meltdown/)。
