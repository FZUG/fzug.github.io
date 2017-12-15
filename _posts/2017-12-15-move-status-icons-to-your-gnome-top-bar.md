---
layout: post
title:  "如何在 GNOME 顶部栏显示任务托盘"
date:   2017-12-15 16:51:45 +0800
categories: gnome
---
[Fedora Workstation](https://getfedora.org/workstation) 预装的是 [GNOME 桌面环境](https://gnome.org/)。虽然 GNOME 团队已经打磨多年，但是并非所有的第三方软件提供商都相应升级了其应用。部分软件提供商还在为其应用使用已经过时的状态栏图标。这些应用常常是一些专有软件，但是比较流行，包括：
- Hangouts 等 Google Chrome 扩展
- Google Music Manager
- Dropbox
- Skype
不过一些自由开源软件也有类似问题。这些应用尚未像 Fedora Workstation 那样更新使用 GNOME 环境的最新特性。

还在使用状态栏图标的应用可能会使 GNOME 屏幕左下角多出一个「抽屉」。你可以将鼠标移到屏幕的左下角呼出这个「抽屉」。

![GNOME 左下角的抽屉](/assets/2017/12/15/drawer.jpg)

这时就会让用户难以选择，是牺牲一块屏幕空间让这个抽屉打开着呢，还是忽略图标可能会有的提示将其收起呢？所幸的是，你可以选择一种两全齐美的方案，那就是 [TopIcons Plus](https://extensions.gnome.org/extension/1031/topicons/) 这个 GNOME Shell 扩展。

TopIcons Plus 扩展可以将状态栏图标放在 [GNOME Shell 顶部栏](https://wiki.gnome.org/Projects/GnomeShell/Tour)展示，与网络状态等标准图标并排显示。这样你就可以「无痛」看到这些状态图标了。

安装这个扩展的方法很简单，在 Firefox 中打开 [TopIcons Plus 扩展页面](https://extensions.gnome.org/extension/1031/topicons/)，点击`On/Off`按钮即可将其安装到个人环境中。

![TopIcons Plus 扩展页面](/assets/2017/12/15/extension.jpg)

如果你曾安装过使用抽屉状态栏图标的应用，那么你会注意到它们已经到了 GNOME 顶部栏了，鼠标点击等动作与之前的抽屉中完全一致。

![GNOME 顶部栏中显示的图标](/assets/2017/12/15/topicons.jpg)

不过，这些图标的点击使用方式可能依然与标准 GNOME 状态图标有所不同，使用的过程中还请了解。这是由软件供应商控制的，而不是 GNOME 。

> 本文翻译自 Paul W. Frields 的文章 [Move status icons to your GNOME top bar](https://fedoramagazine.org/move-status-icons-to-your-gnome-top-bar/)，图片同样来自该文章。
