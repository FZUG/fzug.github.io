---

layout: post
title: 试用为中国大陆用户打造的 Flathub 镜像
date: 2020-05-13 09:43:00 +0000
---

> 本文翻译自 Bartłomiej Piotrowski 的文章 [Test driving Flathub mirror for users in China](https://barthalion.blog/test-driving-flathub-mirror-for-users-in-china/)。

Flathub 无论在哪里使用都比较快的其中一个原因，是它的 CDN 服务由 [Fastly](https://www.fastly.com) 提供。然而在 Fastly 和 Flathub 均被封锁的中国大陆，这对用户并不是一件好事。虽然在中国大陆也有类似 Fastly 的服务，但作为一个开源项目，我们的预算几乎是零。

Arch Linux 开发者 [Felix Yan](https://felixc.at/) 向我们提议了一些「对中国大陆友好」的 VPS 提供商。最终，我使用 Oracle Cloud 的免费套餐配置了两台新服务器。

由于历史原因，Flathub 强制要求设置远端 URL。为了切换到另外的地址，用户需要在终端进行一些手动配置：

1. 检查 `flatpak remote-list` 的输出，看 Flathub 远端地址是全局配置 (`system`) 的还是只对当前用户配置 (`user`) 的。也有可能两者均是。
2. 在文本编辑器打开 `/var/lib/flatpak/repo/config` （对于全局配置）或者 `~/.local/share/flatpak/repo/config` （对于用户配置）
3. 找到 `[remote "flathub"]` 段落。将 `url` 改为 `https://sel.flathub.org/repo/`。另添一行写入以下内容：`url-is-set=true`

整个段落看起来应该像这样：

```
[remote “flathub”]
url=https://sel.flathub.org/repo/
url-is-set=true
xa.title=Flathub
gpg-verify=true
gpg-verify-summary=true
xa.comment=Central repository of Flatpak applications
xa.description=Central repository of Flatpak applications
xa.icon=https://dl.flathub.org/repo/logo.svg
xa.homepage=https://flathub.org/
```

现在之后的所有 Flatpak 操作均会使用位于首尔的镜像之一。我也已经提交了[一个 Pull Request](https://github.com/flatpak/flatpak/pull/3603) 使整个过程不需要那么多手动操作。

如果你位于中国大陆，请尝试一下这个镜像，并在相关的 [GitHub issue](https://github.com/flathub/flathub/issues/813) 提出反馈。我们会一直运行它，如果它工作良好的话，我们会把它加入安装指引。
