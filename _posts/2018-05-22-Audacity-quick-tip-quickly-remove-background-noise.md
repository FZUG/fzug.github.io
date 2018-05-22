---
layout: post
title: Audacity 轻松降噪教程
date: 2018-05-22 09:48:12 +0800
---

> 本文翻译自 Ryan Lerch 的文章 [Audacity quick tip: quickly remove background noise](https://fedoramagazine.org/audacity-quick-tip-quickly-remove-background-noise/)。

当在笔记本电脑上录制声音时——比如最起码的简单录屏——许多用户通常会使用内置麦克风。但是，这些小型麦克风也会捕获很多背景噪音。本文就来教大家在 Fedora 中使用 [Audacity](https://www.audacityteam.org/) 时如何快速剔除音频文件中的背景噪音。

## 安装 Audacity

Fedora 中的 Audacity 是一款用于混音、剪接及音频文件编辑的应用。它在 Fedora 上开箱即用，可以支持各种格式——包括 MP3 和 OGG。我们可以从软件中心安装 Audacity。

![audacity-software](/assets/2018/05/22/audacity-software.jpg)

当然了，你也可通过命令行来安装：

```
sudo dnf install audacity
```

## 导入音频文件和背景噪音样本

安装启动 Audacity 后，在菜单栏选择 **文件（File）> 导入（Import）** 导入你的音频文件。这里有个使用了 [freesound.org 上音频片段](https://freesound.org/people/levinj/sounds/8323/)的示例，其中已经加入了噪音：

<https://ryanlerch.fedorapeople.org/noise.ogg>

接着，从背景噪音中取一段样本以备过滤。在导入的音轨上，选择一段只包含了背景噪音的区域。然后从菜单栏选 **效果（Effect）> 降噪（Noise Reduction）**，然后点击 **取得噪音特征（Get Noise Profile）**。

![select-noise-profile](/assets/2018/05/22/select-noise-profile.gif)

## 过滤噪音

接着选择音轨中你想要进行降噪的区域，可以用鼠标选择部分区域或者 **Ctrl + A** 全选。从菜单中再次选择 **效果（Effect）> 降噪（Noise Reduction）**，在对话框中点击确定就可以完成噪音过滤。

![apply-filter](/assets/2018/05/22/apply-filter.gif)

此外，你也可以试试调整默认的降噪设置，也许能有更好的降噪效果。以下是原始文件和经过（默认设置）降噪后的文件前后对比：

<https://ryanlerch.fedorapeople.org/sidebyside.ogg>
