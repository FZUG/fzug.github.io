---

layout: post
title: 用 Bitwarden 和 Podman 打造个人密码管理器
date: 2019-07-29 23:02:13 +0800
---

> 本文翻译自 Eric Gustavsson 的文章 [Manage your passwords with Bitwarden and Podman](https://fedoramagazine.org/manage-your-passwords-with-bitwarden-and-podman/)。

近年来，你可能会看到有些广告在向你推销密码管理器。一些典型案例包括 [LastPass](https://www.lastpass.com)、[1Password](https://1password.com/) 还有 [Dashlane](https://www.dashlane.com/) 等等。密码管理器确实能减轻记忆网站密码的负担。你不必再用重复的密码或者是好记的密码。只需要记住一个主密码，就可以解锁其他所有的密码。

这样一来，使用一个强密码，而不再用许多的弱密码，安全性就更能有保障。如果密码管理器还带云同步的功能，例如 LastPass、1Password 还有 Dashlane 这些，你还可以跨设备同步密码。然而，可惜的是，这几款工具都不开源。不过幸好我们还有可用的开源替代品。

## 开源密码管理器

这些替代器包括 Bitwarden、[LessPass](https://lesspass.com/) 还有 [KeePass](https://keepass.info/)。Bitwarden是[一款开源密码管理器](https://bitwarden.com/)，工作方式与 LastPass、1Password 还有 Dashlane 相同，都是将你的密码加密之后再存储在服务器上。LessPass 有些不同，是因为它专注于做无状态密码管理器。意思就是它会根据主密码、网站和用户名推导出密码，而并不存储加密的密码。另外一款——KeePass——是基于文件的密码管理器，它的插件和应用程序具有很高的灵活性。

这三款应用都有各自的不足。Bitwarden 将所有东西存储在一个地方，并且会通过其 API 和网站界面暴露在网络上。LessPass 无法存储自定义密码，因为它是无状态的，所以你需要使用由它演算出来的密码。KeePass 是一款基于文件的密码管理器，所以在多设备之间同步就会有点麻烦。你可以通过 [WebDAV](https://en.wikipedia.org/wiki/WebDAV) 将云存储应用与之配合来进行同步，但是并非所有的客户端都支持，如果设备同步异常，还可能会遇到文件冲突的情况。

本文就着重讲一下 Bitwarden。

## 运行一个非官方的 Bitwarden 实现

Bitwarden 服务端和 API 有一个社区版实现，名叫 [bitwarden_rs](https://github.com/dani-garcia/bitwarden_rs/)。该实现是完全开源的，因为它可以使用 SQLite 或 MariaDB/MySQL 数据库，而无需像官方服务器那样使用专有的 Microsoft SQL Server。

当然，官方版与非官方版之间还是有一些区别需要了解一下。比如说，[官方服务端经过了第三方的代码审计](https://blog.bitwarden.com/bitwarden-completes-third-party-security-audit-c1cc81b6d33)，而非官方版则没有。在实现方面，非官方版本缺少了[邮件确认的支持和使用 Duo 或邮件接收代码等方式的双因素验证](https://github.com/dani-garcia/bitwarden_rs/wiki#missing-features)。

接下来我们就开始实际操作，跑一个服务端，并且为它启用 SELinux。根据 bitwarden_rs 的文档，你可以构建一个 Podman 命令，如下所示：

```
$ podman run -d \
 --userns=keep-id \
 --name bitwarden \
 -e SIGNUPS_ALLOWED=false \
 -e ROCKET_PORT=8080 \
 -v /home/egustavs/Bitwarden/bw-data/:/data/:Z \
 -p 8080:8080 \
 bitwardenrs/server:latest
```

该命令会下载 bitwarden_rs 镜像，并在用户命名空间下的用户容器中运行它。它使用了一个大于 1024 的非特权端口，所以不需要 root 权限便可以绑定端口。另外，我们用 *:Z* 更改了卷的 SELinux 上下文，这样在 */data* 上读写时就可以避免权限问题。

如果你想将其托管到某个域名下，建议将该服务端放在 Apache 或 Nginx 反向代理下。这样的话你就可以将 80 和 443 端口代理到容器的 8080 端口，就不需要用 root 身份运行容器了。

## 搭配 systemd 运行

现在 Bitwarden 可以正常运行了，你可能就会考虑如何让它保持运行状态。接下来，我们来创建一个能让容器持久运行的 unit 文件，让这做到无响应时自动重启，而且在开机时自动启动。创建一个文件，保存为 */etc/systemd/system/bitwarden.service*：

> 译注：
>
> 1. 请务必将下面的 User 和 Group 分别改成你自己的用户名和组名。
>
> 2. 经过译者测试，为确保正常运行，该文件已在原文基础上稍作修改，感兴趣的朋友可以与英文原文对照查看。

```
[Unit]
Description=Bitwarden Podman container
Wants=syslog.service

[Service]
Type=forking
User=egustavs
Group=egustavs
TimeoutStartSec=0
ExecStart=/usr/bin/podman start 'bitwarden'
ExecStop=-/usr/bin/podman stop -t 10 'bitwarden'
Restart=always
RestartSec=30s
KillMode=none

[Install]
WantedBy=multi-user.target
```

现在就可以用 *[sudo](https://fedoramagazine.org/howto-use-sudo/)* 来启动它了：

```
$ sudo systemctl enable bitwarden.service && sudo systemctl start bitwarden.service
$ systemctl status bitwarden.service
bitwarden.service - Bitwarden Podman container
    Loaded: loaded (/etc/systemd/system/bitwarden.service; enabled; vendor preset: disabled)
    Active: active (running) since Tue 2019-07-09 20:23:16 UTC; 1 day 14h ago
  Main PID: 14861 (podman)
     Tasks: 44 (limit: 4696)
    Memory: 463.4M
```

搞定！现在 Bitwarden 就已经成功跑起来了，而且会在后台持续运行。

## 添加 Let's Encrypt

如果你有域名的话，强烈建议你通过 Let's Encrypt 等加密方式来运行你的 Bitwarden 实例。Certbot 这个工具可以帮我们利用 Let's Encrypt 来生成证书，而且他们网站上也有[在 Fedora 上操作的指导](https://certbot.eff.org/instructions)。

生成证书之后，你可以依照[这里的步骤](https://github.com/dani-garcia/bitwarden_rs/wiki/Enabling-HTTPS)来为 bitwarden_rs 配置 HTTPS。不过请记得在 Let's Encrypt 卷的后面加上 *:Z* 来处理权限，而且不要修改端口哦。
