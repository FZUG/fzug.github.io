---

layout: post
title: 使用 WireGuard 搭建虚拟专用网络
date: 2020-02-11 21:30:20 +0800
---

> 本文翻译自 Peter Maynard 的文章 [Build a virtual private network with Wireguard](https://fedoramagazine.org/build-a-virtual-private-network-with-wireguard/)。

WireGuard 是一款新型虚拟专用网络，旨在替代 IPSec 和 OpenVPN。它的设计目标就是简单而且安全，并且充分利用[噪声协议框架（Noise Protocol Framework）](http://www.noiseprotocol.org/)等新技术。有人认为 WireGuard 配置起来就像 OpenSSH 一样简单。本文就为你展示一下如何部署和使用。

它目前正在活跃开发中，因此对于生产环境的机器来说可能不是最佳选择。不过，以后 WireGuard 可能会并入 Linux 内核中。该设计已[经过形式验证](https://hal.inria.fr/hal-02100345)*，并被证明可抵抗多种威胁。

部署 WireGuard 之前，请确保 Fedora Linux 系统已更新为最新版本，因为 WireGuard 还没有一个稳定的发布节奏。

## 设置时区

查看时间信息，确保时区设置无误：

```
timedatectl
```

必要的话，可以设置一下正确的时区，例如设置成 `Asia/Shanghai`

```
timedatectl set-timezone Asia/Shanghai
```

请注意，系统的实时时钟（RTC）可能依然设置为 UTC 或其他时区。

## 安装 WireGuard

（译者注：根据 [WireGuard 安装说明](https://www.wireguard.com/install/#fedora-32-tools)，在 Fedora ≥ 32 的系统上，可以直接使用 `$ sudo dnf install wireguard-tools` 安装；而在 Fedora ≤ 31 的系统上，则需要按照如下方法启用 COPR 仓库来安装。）

安装之前需要先启用该项目的 COPR 仓库，然后[使用](https://fedoramagazine.org/howto-use-sudo/) *[sudo](https://fedoramagazine.org/howto-use-sudo/)* 通过 *dnf* 安装：

```
$ sudo dnf copr enable jdoss/wireguard
$ sudo dnf install wireguard-dkms wireguard-tools
```

安装之后，会有两个新命令，以及 systemd 的相关支持：

- **wg**：用于配置 WireGuard 网卡
- **wg-quick**：用于启用虚拟专用网络隧道

创建 WireGuard 的配置目录，并设置 `umask` 为 `077`。`umask 077` 代表允许文件所有者（root）读写，但禁止其他所有人读写。

```
mkdir /etc/wireguard
cd /etc/wireguard
umask 077
```

## 生成密钥对

先生成私钥，再根据它来生成公钥。

```
$ wg genkey > /etc/wireguard/privkey
$ wg pubkey < /etc/wireguard/privkey > /etc/wireguard/publickey
```

或者你也可以将其合并到一行里：

```
wg genkey | tee /etc/wireguard/privatekey | wg pubkey > /etc/wireguard/publickey
```

这里涉及到一个[虚荣地址生成器（vanity address generator）](https://github.com/warner/wireguard-vanity-address)的概念，感兴趣可以了解一下。你还可以生成一串预共享密钥（pre-shared key）以提供一定程度的量子保护（quantum protection）：

```
wg genpsk > psk
```

对于服务器和客户端，这个值都是相同的，所以只需要运行一次命令就可以了。

## 配置 WireGuard 服务器和客户端

客户端和服务器都要有 *[Interface]* 选项，用于指定分配给网卡的 IP 地址以及私钥。

针对每个对端（peer）（服务器和客户端）都要有一个 *[Peer]* 部分，其中包含各自的 *公钥（PublicKey）* 和 *预共享密钥（PresharedKey）*。此外，这一部分中还可列举出可使用此隧道的 IP 地址。

### 服务器

当该网卡接口启用时会自动添加一条防火墙规则，同时启用伪装（masquerade）。请一定注意相应网卡 IPv4 的 */24* 地址段，和客户端是不同的。参考如下所示内容，编辑 */etc/wireguard/wg0.conf* 这个文件， *Address* 使用服务器的 IP 地址，在 *AllowedIPs* 中加上客户端 IP 地址。

```
[Interface]
Address    = 192.168.2.1/24, fd00:7::1/48
PrivateKey = <此处填服务器上的私钥>
PostUp     = firewall-cmd --zone=public --add-port 51820/udp && firewall-cmd --zone=public --add-masquerade
PostDown   = firewall-cmd --zone=public --remove-port 51820/udp && firewall-cmd --zone=public --remove-masquerade
ListenPort = 51820

[Peer]
PublicKey    = <此处填客户端上的公钥>
PresharedKey = <此处填预共享密钥>
AllowedIPs   = 192.168.2.2/32, fd00:7::2/48
```

通过将以下内容添加到 */etc/sysctl.conf* 中，允许转发 IP 数据包：

```
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1
```

加载新设置：

```
$ sysctl -p
```

这样可以使转发在系统重启后依然有效。

### 客户端

客户端与服务器配置非常相似，只不过多了个可选的 *PersistentKeepalive* 保活选项，可以设置为 30 秒。主要是为了防止 NAT 造成问题，根据实际情况，可能不需要设置。如果把 AllowedIPs 设置成 0.0.0.0/0 就会把所有的流量都导向隧道。参考如下内容，编辑客户端的 */etc/wireguard/wg0.conf* 这个文件，*Address* 使用客户端的 IP 地址，*Endpoint*写服务器的 IP 地址。

```
[Interface]
Address    = 192.168.2.2/32, fd00:7::2/48
PrivateKey = <此处填客户端上的私钥>

[Peer]
PublicKey    = <此处填服务器上的公钥>
PresharedKey = <此处填预共享密钥>
AllowedIPs   = 0.0.0.0/0, ::/0

Endpoint     = <服务器 IP>:51820
PersistentKeepalive = 30
```

## 测试 WireGuard

启动并检查服务器和客户端上的隧道状态：

```
$ systemctl start wg-quick@wg0
$ systemctl status wg-quick@wg0
```

通过以下方式测试连接：

```
ping google.com
ping6 ipv6.google.com
```

然后检查外部 IP 地址：

```
dig +short myip.opendns.com @resolver1.opendns.com
dig +short -6 myip.opendns.com aaaa @resolver1.ipv6-sandbox.opendns.com
```

***

* *在这里，「经过形式验证」意味着已证明该设计具有数学意义上的消息正确性和密钥保密性，前向保密，相互认证，会话唯一性，通道绑定以及可以抵抗重放攻击，密钥泄露模拟和拒绝服务（DoS）攻击等。*
