---
layout: post
title: 玩转 Fedora 28 上的模块
date: 2018-06-09 21:01:55 +0800
---

> 本文翻译自 Merlin Mathesius 的文章 [Working with modules in Fedora 28](https://fedoramagazine.org/working-modules-fedora-28)。


本站之前的文章「[Fedora 28 Server 版中的模块化 (Modularity) 新特性]({% post_url 2018-05-10-Modularity-in-Fedora-28-Server-Edition %})」已经为大家介绍了 Fedora 28 中的模块化。其中也列举了几个示例模块，并说明了它们都解决了哪些问题。本文以其中一个来进行实际操作，演示如何通过模块来安装并设置 Review Board 3.0。

## 准备工作

在参考本文使用模块之前，你需要先装好一台 [Fedora 28 Server 版](https://getfedora.org/server/)系统且要有 [sudo 管理员权限](https://fedoramagazine.org/howto-use-sudo/)。另外，也请运行以下命令来确保系统里的软件包都是最新的：

```
sudo dnf -y update
```

其实你也可以在 Fedora 28 非 Server 版上使用模块，但是请留意[这篇文章评论里说到的警告内容](https://fedoramagazine.org/modularity-fedora-28-server-edition/#comment-476696)。

## 检查模块

首先，我们可以看一下 Fedora 28 上都有哪些可用的模块。运行以下命令：

```
dnf module list
```

输出结果会列出一系列模块，并展示其相关模块流（stream）、版本以及各自可以安装的概述（profile）。某个特定模块流旁边的 `[d]` 代表了该模块会默认安装这一模块流。

输出结果同时还会显示大多数模块都有一个名为 `default` 的概述（profile）。这并非巧合，因为 `default` 就是要用到的默认概述。

如果想看全部模块由何而来，可以运行以下命令：

```
dnf repolist
```

除了通常的 [fedora 和 updates 软件仓库](https://fedoraproject.org/wiki/Repositories)外，输出结果还显示了 `fedora-modular` 和 `updates-modular` 软件仓库。

本文便带你来设置 Review Board 3.0。也许你已经在前面的输出内容中注意到了一个名为 `reviewboard` 的模块。接下来，来看一下这个模块的一些详细信息。运行以下命令：

```
dnf module info reviewboard
```

描述信息可以确认这就是 Review Board 模块，同时也说明了它是 2.5 模块流。但是，我们想要安装的是 3.0 版。再来看一下有哪些 `reviewboard` 模块可用：

```
dnf module list reviewboard
```

2.5 模块流旁边的这个 `[d]` 意味着它被设定为 `reviewboard` 的默认模块流。既然如此，那就指明一下我们想要的模块流：

```
dnf module info reviewboard:3.0
```

想了解更多 `reviewboard:3.0` 详细信息的话，可以加上冗长选项：

```
dnf module info reviewboard:3.0 -v
```

## 安装 Review Board 3.0 模块

找到所需的模块之后，使用以下命令来安装：

```
sudo dnf -y module install reviewboard:3.0
```

输出结果显示 ReviewBoard 软件包已经安装，同时安装的还有它所依赖的其他几个软件包，包括 `django:1.6` 模块中的几个软件包。此次安装过程也同时启用了 `reviewboard:3.0` 模块及其所依赖的 `django:1.6` 模块。

接下来，可以看看都有哪些模块启用了，使用以下命令：

```
dnf module list --enabled
```

输出结果中会以 `[e]` 标示已启用的模块流，用 `[i]` 标示已安装的概述。在这个 `reviewboard:3.0` 模块的示例中，默认概述便是已经安装了。我们可以在安装模块时指定一个不同的概述。实际上，你也可以现在指定——只是这次没有必要再指定 3.0 模块流，因为它已经启用了：

```
sudo dnf -y module install reviewboard/server
```

不过，安装 `reviewboard:3.0/server` 概述也没什么影响。`reviewboard:3.0` 模块的 `server` 概述就是 `default` 概述——所以也不会再安装别的什么了。

## 创建 Review Board 站点

Review Board 3.0 模块及其所依赖的软件包都安装之后，就可以在本地系统上[创建一个 Review Board 站点](https://www.reviewboard.org/docs/manual/dev/admin/installation/creating-sites/)了。无需太多麻烦和说明，只需要复制粘贴以下的命令即可：

```
sudo rb-site install --noinput \
    --domain-name=localhost --db-type=sqlite3 \
    --db-name=/var/www/rev.local/data/reviewboard.db \
    --admin-user=rbadmin --admin-password=secret \
    /var/www/rev.local
sudo chown -R apache /var/www/rev.local/htdocs/media/uploaded \
    /var/www/rev.local/data
sudo ln -s /var/www/rev.local/conf/apache-wsgi.conf \
    /etc/httpd/conf.d/reviewboard-localhost.conf
sudo setsebool -P httpd_can_sendmail=1 httpd_can_network_connect=1 \
    httpd_can_network_memcache=1 httpd_unified=1
sudo systemctl enable --now httpd
```

现在打开浏览器，访问 `http://localhost`，体验全新的 Review Board 站点吧！要登录 Review Board 管理后台的话，请使用上面 `rb-site` 命令里的用户名和密码。

## 模块的清理

及时清理是个好习惯。你可以这样删除 Review Board 模块和站点目录：

```
sudo dnf -y module remove reviewboard:3.0
sudo rm -rf /var/www/rev.local
```

## 结语

在探索了如何检查和管理 Review Board 模块之后，你可以尝试一下 Fedora 28 上其他可用的模块。

你可以从 [Fedora Modularity](https://docs.pagure.org/modularity/) 网站上了解到更多关于 Fedora 28 中模块的用法。同时 `dnf` 手册中 `Module Command` 的部分也包含了很多实用的信息。
