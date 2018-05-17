---
layout: post
title:  "保护你的 Fedora 系统免受 DHCP 缺陷影响"
date:   2018-05-17 18:01:00 +0800
---

本周早些时候，dhcp-client 中一项高危漏洞被发现及公开。该缺陷会让你的系统和数据面临较高风险，尤其是当你连接了不属于自己的不可信 WiFi 接入点的时候。本文带你了解如何保护你的 Fedora 系统。

动态主机配置协议 (DHCP) 允许你的系统从加入的网络中获取配置。系统首先请求 DHCP 信息，此后通常服务器（例如路由器）做出应答。服务器向你的系统提供配置的必须信息。这就是通常你的电脑连接无线网络时完成配置的流程。

然而，本地网络中的入侵者可能会利用这个漏洞。使用 NetworkManager 的 dhcp-client 脚本中的缺陷，入侵者能够在你的系统中使用 root 权限执行任意命令。该 DHCP 缺陷会使你的系统和数据面临较高风险。这个缺陷被指派了编号 CVE-2018-1111 并且在 Bugzilla 上有 [bug](https://bugzilla.redhat.com/show_bug.cgi?id=1567974) 进行追踪。

## 针对这项 DHCP 缺陷的防护


维护者已经将用于 Fedora 26、27、28 以及 Rawhide 版本的 dhcp 已修复的软件包提交到了 updates-testing 仓库。它们应该会在一到两天后出现在大多数用户使用的稳定仓库中。相关的软件包为：

* Fedora 26: dhcp-4.3.5-11.fc26
* Fedora 27: dhcp-4.3.6-10.fc27
* Fedora 28: dhcp-4.3.6-20.fc28
* Rawhide: dhcp-4.3.6-21.fc29

## 更新稳定版本的 Fedora 系统

你可以使用如下 [sudo](https://fedoramagazine.org/howto-use-sudo/) 命令来更新稳定版 Fedora。必要时输入密码：
```
sudo dnf --refresh --enablerepo=updates-testing update dhcp-client
```
（截至中文稿件发布时，上述命令已不需要。可直接使用下面的命令。译者注。）

之后可以使用标准的稳定版仓库进行更新。你可以使用如下命令从稳定版仓库更新 Fedora：
```
sudo dnf --refresh update dhcp-client
```
## 更新 Rawhide 系统

如果你的系统使用的 Rawhide，立即使用如下命令下载并安装软件包：
```
mkdir dhcp && cd dhcp
koji download-build --arch={x86_64,noarch} dhcp-4.3.6-21.fc29
sudo dnf update ./dhcp-*.rpm
```
（截至中文稿件发布时，上述命令已不需要。可直接使用下面的命令。译者注。）

当每夜构建的 Rawhide 组装之后，只需要执行 `sudo dnf update` 就可以获得更新。

## Fedora Atomic Host

适用于 Fedora Atomic Host 的修复已经进入了 ostree 版本 28.20180515.1。执行如下命令获取该更新：
```
atomic host upgrade -r
```
该命令将重启你的系统以使更新生效。

> 本文翻译自 Paul W. Frields 的文章 [Protect your Fedora system against this DHCP flaw](https://fedoramagazine.org/protect-fedora-system-dhcp-flaw/)。
