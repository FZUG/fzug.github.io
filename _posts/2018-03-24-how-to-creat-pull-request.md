---
layout: post
title:  "如何向 Fedora 中文用户组网站投稿"
date:   2018-03-24 10:38:01 +0800
categories: tutorial
---

朋友你好！欢迎访问 Fedora 中文用户组网站。这里有一群喜欢 Fedora Linux 的人，大家会一起交流经验。

如果你有什么心得或经验想要分享的话， `Git` 这个工具不应该成为你投稿路上的最后一块「绊脚石」。

接下来这篇文章会教你上手 `Git` ，轻松贡献文章到 Fedora 中文用户组。

## 准备工作

0. 如果你的电脑上还没有安装 `Git` ，请先执行 `sudo dnf install git -y` 安装

1. 注册 GitHub 帐号

    <https://github.com/join>

2. 访问 <https://github.com/FZUG/fzug.github.io> 并且 `Fork` 仓库

    ![Fork](/assets/2018/03/24/fork.png)

    如上图，点击右上角的 `Fork` 按钮。

3. `Clone` 到你的电脑上

    成功 `Fork` 之后，你就可以看到左上角显示的是「你的名字 / fzug.github.io 」（显然，我的小号名叫 fzug-no-one ）。点击图中那个绿色的「 Clone or download 」按钮，然后点击箭头所指的复制按钮，即可复制链接到剪贴板。

    ![Copy link](/assets/2018/03/24/copy-link.png)

    打开你的电脑上的终端（ Terminal ）， `cd` 到你计划存放这个仓库的目录下，输入 `git clone` ，空格，然后粘贴刚才复制的链接，回车。

    ![Clone](/assets/2018/03/24/clone.png)

    如此，便将这个仓库 `Clone` 到了你的电脑上，之后的投稿、编辑、协作，都可以先在这个本地仓库中修改，再提交到 GitHub 上的仓库中。

## 为社区撰写你的第一篇文章

完成前面的准备工作之后，你就可以专注于创作文章了。不管是个人经验、心得体会，还是将优秀的外文文章翻译成中文，只要你觉得对于 Fedora 中文用户有所禆益，都可以添加进来，当然前提是要遵循原作者的版权要求哦。

- 如果你的文章有配图的话，请将图片添加到 `/assets/年/月/日/`目录中：
  例如， `/assets/2018/03/24/fork.png` 。
- 在 `_posts` 目录下添加名为`年-月-日-标题.md` 的 MarkDown 文档：
  例如， `_posts/2018/03/24/how-to.md` 。
- 用你喜欢的编辑器打开刚添加的 MarkDown 文档
  在开头添加类似如下的基本信息（注意要带上 `---` ）：

```
---
layout: post
title:  "如何向 Fedora 中文用户组网站投稿"
date:   2018-03-24 10:38:01 +0800
categories: tutorial
---
```

在这之后就是你要撰写的正文了。
 
哦，对，你还需要了解一下 [MarkDown 排版的基本方法](http://wowubuntu.com/markdown/basic.html)。另外文章正文内容还可以参考一下这份[中文文案排版指北](https://github.com/mzlogin/chinese-copywriting-guidelines) 。

## 使用 `Git` 完成投稿

之前在「准备工作」的部分，我们只是用 `Git` 将项目克隆到了本地，还没有对本地的 `Git` 进行相关的设置。现在请参照以下命令设置本地 `Git` 的相关信息。

```bash
# 设置用户名和邮箱，可以跟 GitHub 上的一致
git config --global user.name "yourname"
git config --global user.email "your email"

# 为本地的仓库添加一个 upstream （上游）
git remote add upstream https://github.com/FZUG/fzug.github.io

# 最后可以看一下设置的结果
git config -l
```
![Commit](/assets/2018/03/24/commit.png)

设置完成后，如上图所示，我们把刚才写好的文章先提交到本地的仓库中。

```bash
git status
# 可以看到 Git 提示有尚未被追踪的文件（ Untracked files ）

# 追踪当前目录下的所有文件
git add .
# 注意最后的那个点，不要落掉

# 将刚写的文章和配图提交到本地仓库
git commit -am "add how-to.md"
# 后面双引号中间的部分是你要写的提交信息，便于协作者了解项目的历史
```

在你将文章推送到 GitHub 上之前，还有一个问题要考虑——

> 如果在我写文章的这段时间，已经有别的同学向 FZUG 仓库中推送了新文章，那我的仓库中岂不是缺少了这些文章？

所以，为了解决这一问题，我们要定期使用上游项目仓库（ upstream ）内容更新自己仓库内容。

```bash
# 先从上游拉取一下最新的内容
git fetch upstream

# 把最新的内容合并一下
git rebase upstream/master
```

好了，现在你的仓库就肯定是最新的了—— GitHub 上的中央仓库中有的文章已经合并到了你的本地，同时你还有自己刚刚写好的文章，这是别人没有的，现在推送到 GitHub 上，准备跟别人分享吧！

```bash
git push origin master
# 将仓库中的内容推送到 GitHub 上的远端仓库中
```

![push](/assets/2018/03/24/push.png)

如图中所示，填入 GitHub 的用户名和密码即可推送成功。

最后，「投稿」，在你的 GitHub 仓库页面上，参照下图，提交 `Pull Request` （俗称「提 PR 」），添加标签，并邀请维护者进行 `Review` 。

![pr1](/assets/2018/03/24/pr1.png)

![pr2](/assets/2018/03/24/pr2.png)

等维护者完成合并之后，你的文章就会呈现在各位读者面前了～

本文只是简单介绍一下 `GitHub` 上「提 PR 」的方法，为的是不让 `Git` 阻碍了你分享的脚步。想了解更多 `Git` 的使用方法，推荐了解一下 [git-recipes](https://github.com/geeeeeeeeek/git-recipes/wiki) 这个项目。

## One more thing...

想必你也听说过，在 GitHub 上添加 `SSH 公钥` 后可以无需用户名密码执行 `git push` ，那么具体如何操作呢？上面的示例命令中有哪些地方需要调整呢？

这就当作是一道新手练习题吧，欢迎各位新朋友把你的答案添加在下面。写好了，别忘向 FZUG 提 PR 哦～

0. 生成 ssh 密钥对
 windows 用户可以使用终端登录的工具来生成， linux 下直接使用命令
```
ssh-keygen -t rsa
```

1. 查看公钥内容并上传到 git

```
cat ~/.ssh/id_rsa.pub
```

>
[licheng@localhost ~]$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCblmg8Qn7bBcUFWrukZfFHuHAigSCVChPVqu1WUomcaVRNnhGhboSGCZAMaedL9ppxN4MD2VMcTQY0ateAxT21DB2IbBO6awTdnb/m+Oh3fz8B7VpTKLICUlQIkpLOkvGNtpxRHA8DEURjwsNhk13z7aWykUqhYZ78VlWIldOZ3io9PAZo65GSHtesTTBIrXdBnahPaEbAoXeyYw0bWTGDJ11vVsAnZXhQXLg+e68Eavw2iG4TCubuHW0LKwTGwN0UhJCoRMiy22Cv0fxa6x086mSFn7LXYpqv5dqcdnWO1VRZ74RdtYu9W2oOF/ZPq+REQK28uN8zKMs0pY8Q2B29 licheng@localhost.localdomain
>

![sshgit1](/assets/2018/03/28/sshgit1.png)

![sshgit2](/assets/2018/03/28/sshgit2.png)

![sshgit3](/assets/2018/03/28/sshgit3.png)

![sshkeyadd](/assets/2018/03/28/sshkeyadd.png)

2. 使用 sshclone 并尝试 push 操作

首先使用 ssh 形式克隆一个项目，注意不是 https 

![sshclone](/assets/2018/03/28/sshclone.png)

```
[licheng@localhost l]$ git clone git@github.com:shiptux/lispbox.git
正克隆到 'lispbox'...
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
接收对象中: 100% (5/5), 12.78 KiB | 4.26 MiB/s, 完成.
```
```
[licheng@localhost lispbox]$ git push
Everything up-to-date

```
并不需要输入密码