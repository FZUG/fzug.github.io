---
layout: post
title: 在 Fedora 上为 SSH 配置双因素验证
date: 2019-02-21 14:36:25 +0800
---

> 本文翻译自 Curt Warfield 的文章 [Set up two-factor authentication for SSH on Fedora](https://fedoramagazine.org/two-factor-authentication-ssh-fedora/)。

似乎每天都有安全漏洞见诸报端，而我们的数据也总是面临着各种风险。SSH 作为一种远程系统的连接方式，虽然已经算是安全的了，不过我们仍然可以采取一些措施来加强其安全性。本文就来介绍一下如何操作。

我们这里会用到双因素身份验证（2FA）。假设我们禁用了密码登录，并且只允许使用公钥和私钥进行 SSH 连接，即便如此，一旦未经授权的用户窃取了密钥，便可以拿到系统访问权限。

启用双因素验证后，就不能仅凭 SSH 密钥连接到服务器了。这时就需要再从移动设备上获取一串由验证器应用随机生成的数字。

本文中展示的方案是一种基于时间的一次性密码（TOTP）算法。以 [Google Authenticator](https://en.wikipedia.org/wiki/Google_Authenticator) 为服务端应用。Google Authenticator 在 Fedora 上默认是可用的。

至于手机端，我们可以使用任意一款兼容 TOTP 的双因素验证器应用。无论 Android 还是 iOS 平台，都有很多可以配合 TOTP 和 Google Authenticator 的免费应用。本文以 [FreeOTP](https://freeotp.github.io/) 为例。

## 安装并设置 Google Authenticator

首先在服务器上安装 Google Authenticator。

```
$ sudo dnf install -y google-authenticator
```

运行该应用程序。

```
$ google-authenticator
```

它会询问一系列问题。以下的片段会示范如何回答，从而进行合理的安全设置。

```
Do you want authentication tokens to be time-based (y/n) y
Do you want me to update your "/home/user/.google_authenticator" file (y/n)?y
```

该应用会分别提供密钥、验证码和恢复码。一定要把这些都存放在一个安全保险的地方。万一手机丢了，恢复码就是**唯一**能帮你登录到服务器的方式。

## 设置手机上的验证器

在手机上安装验证器应用（FreeOTP）。Android 用户可以通过 Google Play 等应用商店下载，iOS 用户可以从 App Store 下载。

屏幕上会展示一个二维码。打开手机上的 FreeOTP。首先添加帐户，点击顶部那个二维码样子的图标，然后扫描二维码。设置完成后，每次远程连接服务器时，都必须提供验证器所生成的随机数。

## 完成配置

应用会再问一些其他的问题。可以参考以下示例来进行合理的安全设置。

```
Do you want to disallow multiple uses of the same authentication token?This restricts you to one login about every 30s, but it increases your chances to notice or even prevent man-in-the-middle attacks (y/n) y
By default, tokens are good for 30 seconds. In order to compensate for possible time-skew between the client and the server, we allow an extra token before and after the current time. If you experience problems with poor time synchronization, you can increase the window from its default size of +-1min (window size of 3) to about +-4min (window size of 17 acceptable tokens).
Do you want to do so?(y/n) n
If the computer that you are logging into isn't hardened against brute-force login attempts, you can enable rate-limiting for the authentication module. By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting (y/n) y
```

现在，我们再来配置 SSH 使之支持双因素验证。

## 配置 SSH

在进行这一步骤前，**请一定先确保已经通过公钥建立了可用的 SSH 连接**，因为我们稍后会禁用密码连接。如果中途出现什么错误的话，还可以通过已经建立的连接来进行补救。

在服务器上通过 *[sudo](https://fedoramagazine.org/howto-use-sudo/)* 来编辑 */etc/pam.d/sshd* 这个文件。

```
$ sudo vi /etc/pam.d/sshd
```

注释掉 *auth substack password-auth* 这一行：

```
#auth       substack     password-auth
```

将下面这一添加到文件的最后。

```
auth sufficient pam_google_authenticator.so
```

保存并退出。接下来，编辑 */etc/ssh/sshd_config* 这一文件。

```
$ sudo vi /etc/ssh/sshd_config
```

找到带有 *ChallengeResponseAuthentication* 的这一行并将其改成 *yes*。

```
ChallengeResponseAuthentication yes
```

找到带有 *PasswordAuthentication* 的这一行并将其改成 *yes*。

```
PasswordAuthentication no
```

将下面这一添加到文件的最后。

```
AuthenticationMethods publickey,password publickey,keyboard-interactive
```

保存并退出，然后重启 SSH。

```
$ sudo systemctl restart sshd
```

## 测试双因素身份验证

当我们再尝试连接到服务器时，系统会提示输入验证码。

```
[user@client ~]$ ssh user@example.com
Verification code:
```

验证码便是由移动设备上的验证器应用随机生成的一串数字。这一串数字会在数秒之后改变，所以我们需要在它变之前完成输入。

![](/assets/2019/02/21/freeotp-1.png)

如果不输入验证码，就无法登录系统，会得到权限被拒绝的错误提示：

```
[user@client ~]$ ssh user@example.com
Verification code:
Verification code:
Verification code:
Permission denied (keyboard-interactive).
[user@client ~]$
```

## 结语

通过添加这种简单的双因素身份验证，我们便可以使未经授权用户访问服务器的难度大大增加。
