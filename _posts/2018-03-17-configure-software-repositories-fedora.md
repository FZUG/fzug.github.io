---
layout: post
title:  "在 Fedora 中配置软件仓库"
date:   2018-03-17 18:56:30 +0800
---

您的 Fedora 系统从软件仓库（简称 repo）中获取软件。每个软件仓库有数个软件，你可以从对应的软件仓库中安装、使用它们。Fedora 官方软件仓库中包含了几千个免费、开源的的软件。有些软件仓库有免费或者专有的软件。有些仓库则只有一个软件。有时，您可能需要配置软件仓库。

幸运的是，在 Fedora 中，配置软件仓库很容易。例如，你可能想要获得一个包来测试它，看看它是否修复了一个 bug。在这种情况下，你需要打开 Fedora 的测试软件仓库。你可能希望保留它来获取更多测试包。或者你可能想关闭测试软件仓库，停止参与测试。

## 通过命令行来配置

可以在命令行界面使用 `dnf` 来配置软件仓库。为了查看所有*开启*着的软件仓库，使用如下命令：

```shell
sudo dnf repolist
```

你可以仅使用一个带参数的命令来改变软件仓库配置。如果想要在一条命令中开启或者关闭一个软件仓库，使用如下命令行参数：

```shell
sudo dnf --enablerepo=<reponame>...
sudo dnf --disablerepo=<reponame>...
```

一个真实的例子，`dnf` 命令带上参数。比如，为了从 Fedora 测试软件仓库安装最新的内核：

```shell
sudo dnf --enablerepo=updates-testing install kernel\*
```

你可以一起使用开启仓库和关闭仓库的选项。比如：

```shell
sudo dnf --enablerepo=repo1 --disablerepo=repo2,repo3 install <package>
```

如果你想持久改变这个配置，使用如下命令：

```shell
sudo dnf config-manager --set-enabled <reponame>
sudo dnf config-manager --set-disabled <reponame>
```

## 消除混淆

或许你会用不同的配置来安装、更新或删除软件。此时，情况会有点复杂。你或许想知道哪个软件是从哪个软件仓库安装的。如果你想知道，请按照如下方式尝试。

首先，关闭额外的软件仓库，比如以 `-testing` 结尾的。最好只开启 fedora 和 updates 软件仓库。即为每个要关闭的软件仓库执行这个命令：

```shell
sudo dnf config-manager --set-disabled <unwanted-repo>
```

然后，运行这个命令来仅同步稳定和官方更新的包：

```shell
sudo dnf distro-sync
```

这将确保你的Fedora系统只会从配置的的软件仓库中获取更新的包。

如果你想获取更多关于软件仓库的信息，请访问 [Fedora 文档页面](https://docs.fedoraproject.org/f27/system-administrators-guide/package-management/DNF.html)。

> 本文翻译自 Paul W. Frields 的文章 [Configure software repositories in Fedora](https://fedoramagazine.org/configure-software-repositories-fedora/)。
